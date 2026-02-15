# dosuser's blog

A personal blog about development, Spring framework, and distributed storage systems.

## Features

- **Jekyll 4.x + GitHub Pages**: Modern Jekyll setup with latest GitHub Pages compatibility
- **Responsive Design**: Mobile-first approach
- **SEO Optimized**: Jekyll-seo-tag integration
- **Sitemap & RSS**: Automatic sitemap and RSS feed generation
- **Code Highlighting**: Syntax highlighting with Rouge
- **Pagination**: Jekyll Paginate v2 support

## Quick Start

### Prerequisites

- Ruby 3.0 or higher
- Bundler

### Installation

```bash
# Install dependencies
bundle install

# Serve locally
bundle exec jekyll serve

# Visit http://localhost:4000
```

## Building for GitHub Pages

```bash
# Build static site
bundle exec jekyll build

# Output is in _site/ directory
```

## Project Structure

```
.
├── _config.yml           # Site configuration
├── _includes/            # Reusable HTML components
├── _layouts/             # Page layouts
├── _posts/               # Blog posts
├── _sass/                # Stylesheets
├── assets/               # Images, CSS
├── index.html            # Home page
├── about.md              # About page
├── archive/              # Post archive
├── tags/                 # Tag pages
└── feed.xml              # RSS feed
```

## Topics

- Spring Framework
- Distributed Storage Systems
- Web Development
- System Administration
- Personal Diary

## Configuration

Edit `_config.yml` to customize:
- Site title and description
- Author information
- Social media links
- Plugins and build settings

## Deployment

This site is automatically deployed to GitHub Pages when changes are pushed to the main branch.

## License

See [LICENSE.md](LICENSE.md) for details.
