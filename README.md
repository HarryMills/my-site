# Harry Mills - Personal Website

[![Build And Deploy](https://github.com/HarryMills/my-site/actions/workflows/hugo.yaml/badge.svg?branch=main)](https://github.com/HarryMills/my-site/actions/workflows/hugo.yaml)
[![Hugo](https://img.shields.io/badge/Hugo-0.124.0+-FF4088?style=flat&logo=hugo)](https://gohugo.io/)
[![FixIt Theme](https://img.shields.io/badge/Theme-FixIt-blue)](https://github.com/hugo-fixit/FixIt)
[![GitHub Pages](https://img.shields.io/badge/Hosted%20on-GitHub%20Pages-222222?style=flat&logo=github)](https://pages.github.com/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

This is the source code for my personal website, built with [Hugo](https://gohugo.io/) and hosted on GitHub Pages.

🌐 **Live Site**: [harry-mills.com](https://harry-mills.com)

## About

After years of managing a self-hosted WordPress site with all its complexity, updates, and hosting costs, I switched to Hugo for a simpler, faster, and free solution. This static site is automatically deployed to GitHub Pages whenever changes are pushed to the main branch.

## Tech Stack

- **Static Site Generator**: [Hugo](https://gohugo.io/)
- **Theme**: [FixIt](https://github.com/hugo-fixit/FixIt)
- **Hosting**: GitHub Pages
- **CI/CD**: GitHub Actions
- **Domain**: Custom domain via GoDaddy

## Prerequisites

Before you can build this site locally, you need:

- [Hugo](https://gohugo.io/installation/) (v0.124.0 or later)
- [Git](https://git-scm.com/)
- A text editor (VS Code, Sublime Text, etc.)

## Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/testsite.git
cd testsite
```

### 2. Initialize Theme Submodule

The FixIt theme is included as a Git submodule. Initialize it with:

```bash
git submodule update --init --recursive
```

### 3. Install Hugo

If you haven't already, install Hugo:

**macOS (using Homebrew)**:
```bash
brew install hugo
```

**Windows (using Chocolatey)**:
```bash
choco install hugo-extended
```

**Linux (using Snap)**:
```bash
snap install hugo
```

For other installation methods, see the [official Hugo installation guide](https://gohugo.io/installation/).

### 4. Run the Development Server

Start the local development server:

```bash
hugo server
```

Or with draft content visible:

```bash
hugo server -D
```

The site will be available at `http://localhost:1313`

The server will automatically reload when you make changes to content or templates.

## Deployment

This site uses GitHub Actions for automated deployment. When you push to the main branch:

1. GitHub Actions triggers the workflow (`.github/workflows/hugo.yml`)
2. Hugo builds the site
3. The site is deployed to GitHub Pages
4. Changes are live at your custom domain

## Custom Domain Configuration

The custom domain is configured via:
1. The GitHub repository settings (Settings → Pages → Custom domain)
2. DNS settings in GoDaddy (A records and CNAME)

GitHub automatically manages the CNAME file when you configure the custom domain in repository settings.

## Theme Customization

To customize the theme:

- Edit `hugo.toml` for configuration changes
- Add custom CSS in `assets/css/_custom.scss`
- Override theme layouts by creating files in `layouts/` with the same structure as the theme

## Updating the Theme

To update the FixIt theme to the latest version:

```bash
git submodule update --remote themes/FixIt
git add themes/FixIt
git commit -m "Update FixIt theme"
git push
```

## License

The content of this site is my own. The FixIt theme is licensed under the MIT License.

## Contact

For any questions or issues, please open an issue on GitHub or contact me through my website.
