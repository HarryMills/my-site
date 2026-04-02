---
title: "Hosting Your Hugo Site on GitHub Pages with a Custom GoDaddy Domain"
date: 2026-04-01
draft: false
tags: ["hugo", "github-pages", "godaddy", "tutorial", "web-development"]
categories: ["tutorials"]
---

## Introduction

After years of struggling with a self-hosted WordPress site, dealing with constant updates, security patches, plugin 
conflicts, and hosting costs. I finally decided it was time for a change. I wanted something simpler, more reliable, and 
ideally free. That's when I landed on the combination of Hugo and GitHub Pages.

Hugo is a fast and flexible static site generator that eliminates the complexity of database management, PHP updates, 
and server maintenance. Paired with GitHub Pages for free hosting, it's been a game-changer. No more worrying about 
uptime, no more dealing with broken plugins after updates, and best of all, no hosting fees.

In this guide, I'll walk you through the exact process I used to set up my Hugo site on GitHub Pages and configure it 
with a custom domain from GoDaddy. If you're tired of the WordPress headaches like I was, this might be the solution 
you've been looking for.

## Prerequisites

- A GitHub account
- Hugo installed on your computer ([Install Hugo](https://gohugo.io/installation/))
- A custom domain purchased from GoDaddy
- Git installed on your machine

## Part 1: Setting Up Your Hugo Site

### 1. Create a New Hugo Site

First, create your Hugo site locally:

```bash
hugo new site mysite
cd mysite
```

### 2. Add a Theme

Choose a theme from [Hugo Themes](https://themes.gohugo.io/). For this I used FixIt:

```bash
git init
git submodule add https://github.com/hugo-fixit/FixIt.git themes/FixIt
```

Update your `hugo.toml` configuration:

```toml
theme = ["FixIt"]
title = "Your Site Title"
```

### 3. Create Content

Create your first post:

```bash
hugo new posts/my-first-post.md
```

Edit the post and set `draft: false` when ready to publish.

## Part 2: Deploying to GitHub Pages

### 1. Create a GitHub Repository

Create a new repository on GitHub. For a user/organization site, name it `username.github.io`. For a project site, use 
any name.

### 2. Set Up GitHub Actions Workflow

Create `.github/workflows/hugo.yml` in your project:

```yaml
name: Build and deploy
on:
   push:
      branches:
         - main
   workflow_dispatch:
permissions:
   contents: read
   pages: write
   id-token: write
concurrency:
   group: pages
   cancel-in-progress: false
defaults:
   run:
      shell: bash
jobs:
   build:
      runs-on: ubuntu-latest
      env:
         DART_SASS_VERSION: 1.98.0
         GO_VERSION: 1.26.1
         HUGO_VERSION: 0.158.0
         NODE_VERSION: 24.14.0
         TZ: Europe/Oslo
      steps:
         - name: Checkout
           uses: actions/checkout@v6
           with:
              submodules: recursive
              fetch-depth: 0
         - name: Setup Go
           uses: actions/setup-go@v6
           with:
              go-version: ${{ env.GO_VERSION }}
              cache: false
         - name: Setup Node.js
           uses: actions/setup-node@v6
           with:
              node-version: ${{ env.NODE_VERSION }}
         - name: Setup Pages
           id: pages
           uses: actions/configure-pages@v5
         - name: Create directory for user-specific executable files
           run: |
              mkdir -p "${HOME}/.local"
         - name: Install Dart Sass
           run: |
              curl -sLJO "https://github.com/sass/dart-sass/releases/download/${DART_SASS_VERSION}/dart-sass-${DART_SASS_VERSION}-linux-x64.tar.gz"
              tar -C "${HOME}/.local" -xf "dart-sass-${DART_SASS_VERSION}-linux-x64.tar.gz"
              rm "dart-sass-${DART_SASS_VERSION}-linux-x64.tar.gz"
              echo "${HOME}/.local/dart-sass" >> "${GITHUB_PATH}"
         - name: Install Hugo
           run: |
              curl -sLJO "https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.tar.gz"
              mkdir "${HOME}/.local/hugo"
              tar -C "${HOME}/.local/hugo" -xf "hugo_extended_${HUGO_VERSION}_linux-amd64.tar.gz"
              rm "hugo_extended_${HUGO_VERSION}_linux-amd64.tar.gz"
              echo "${HOME}/.local/hugo" >> "${GITHUB_PATH}"
         - name: Verify installations
           run: |
              echo "Dart Sass: $(sass --version)"
              echo "Go: $(go version)"
              echo "Hugo: $(hugo version)"
              echo "Node.js: $(node --version)"
         - name: Install Node.js dependencies
           run: |
              [[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true
         - name: Configure Git
           run: |
              git config core.quotepath false
         - name: Cache restore
           id: cache-restore
           uses: actions/cache/restore@v5
           with:
              path: ${{ runner.temp }}/hugo_cache
              key: hugo-${{ github.run_id }}
              restore-keys:
                 hugo-
         - name: Build the site
           run: |
              hugo build \
                --gc \
                --minify \
                --baseURL "${{ steps.pages.outputs.base_url }}/" \
                --cacheDir "${{ runner.temp }}/hugo_cache"
         - name: Cache save
           id: cache-save
           uses: actions/cache/save@v5
           with:
              path: ${{ runner.temp }}/hugo_cache
              key: ${{ steps.cache-restore.outputs.cache-primary-key }}
         - name: Upload artifact
           uses: actions/upload-pages-artifact@v3
           with:
              path: ./public
   deploy:
      environment:
         name: github-pages
         url: ${{ steps.deployment.outputs.page_url }}
      runs-on: ubuntu-latest
      needs: build
      steps:
         - name: Deploy to GitHub Pages
           id: deployment
           uses: actions/deploy-pages@v4
```

### 3. Configure GitHub Pages

1. Push your code to GitHub:

```bash
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/username/repository.git
git push -u origin main
```

2. Go to your repository on GitHub
3. Navigate to **Settings** → **Pages**
4. Under **Source**, select **GitHub Actions**

Your site should now be live at `https://username.github.io` (or `https://username.github.io/repository` for project sites).

## Part 3: Setting Up a Custom GoDaddy Domain

### 1. Configure DNS Settings in GoDaddy

First, log into your GoDaddy account and navigate to your domain's DNS settings.

#### Recommended Configuration (Using Apex Domain)

Configure these DNS records:

1. **A Records** (for apex domain):
   - Type: `A`
   - Name: `@`
   - Value: `185.199.108.153`
   - TTL: 600 seconds (or default)

   Add three more A records with these IP addresses:
   - `185.199.109.153`
   - `185.199.110.153`
   - `185.199.111.153`

2. **AAAA Records** (for apex domain):
   - Type: `AAAA`
   - Name: `@`
   - Value: `2606:50c0:8000::153`
   - TTL: 600 seconds (or default)

   Add three more AAAA records with these IP addresses:
   - `2606:50c0:8001::153`
   - `2606:50c0:8002::153`
   - `2606:50c0:8003::153`

3. **CNAME Record** (for www subdomain):
   - Type: `CNAME`
   - Name: `www`
   - Value: `username.github.io.` (replace with your GitHub Pages URL)
   - TTL: 1 Hour

### 2. Configure Custom Domain on GitHub

1. Go to your repository on GitHub
2. Navigate to **Settings** → **Pages**
3. Under **Custom domain**, enter your domain (e.g., `yourdomain.com`)
4. Click **Save**

**Important**: GitHub will automatically create and manage the CNAME file, during the deploy action, once you save the 
custom domain. You don't need to create this file manually.

5. Wait for the DNS check to complete (may take a few minutes)
6. Once verified, check **Enforce HTTPS** (may take up to 24 hours to become available)

GitHub Pages will handle both your apex domain and www subdomain automatically, redirecting www to the apex domain.

## Troubleshooting

### DNS Not Propagating
- DNS changes can take up to 48 hours to propagate globally
- Use [DNS Checker](https://dnschecker.org/) to verify your DNS records

### HTTPS Not Available
- Wait up to 24 hours after DNS verification
- Ensure your DNS records are correct
- Try removing and re-adding the custom domain in GitHub settings

### Site Not Updating
- Check the **Actions** tab in your GitHub repository for build errors
- Verify your workflow file syntax
- Ensure `draft: false` in your content front matter

## Best Practices

1. **Always use HTTPS**: Enable "Enforce HTTPS" in GitHub Pages settings
2. **Keep themes updated**: Regularly update your theme submodule
3. **Test locally**: Run `hugo server` before deploying
4. **Use environment-specific configs**: Create separate config files for development and production
5. **Monitor builds**: Check GitHub Actions regularly for any deployment issues

## Conclusion

You now have a fully functional Hugo site hosted on GitHub Pages with a custom domain from GoDaddy! This setup provides:

- Free hosting with GitHub Pages
- Automatic deployments via GitHub Actions
- A professional custom domain
- HTTPS security
- Fast, static site performance

Hugo's speed combined with GitHub's reliability makes this an excellent choice for blogs, portfolios, and 
documentation sites.

Happy blogging! 🚀
