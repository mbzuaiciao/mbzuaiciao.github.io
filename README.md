# mbzuaiciao.github.io

Technical research notebook and blog focusing on AI agents, reasoning systems, agent governance, world models, and financial AI.

## Architecture & Technology

* **Static Site Generator**: [Jekyll](https://jekyllrb.com/)
* **Theme**: [Minima](https://github.com/jekyll/minima)
* **Deployment**: GitHub Pages via [GitHub Actions](.github/workflows/pages.yml)

## Content Organization

* `_posts/`: Markdown articles and research notes formatted as `YYYY-MM-DD-title.md`.
* `assets/images/`: Images and architecture diagrams embedded across posts.
* `about.md`: Overview of research topics and notebook focus.
* `index.md`: Homepage presenting recent writing.

## Local Preview

To build and preview the site locally:

```bash
# Install dependencies
bundle install

# Run local development server
bundle exec jekyll serve
```

Then navigate to:

```text
http://localhost:4000
```

## Deployment

The site deploys automatically to GitHub Pages on every push to the `main` branch via the `.github/workflows/pages.yml` workflow. Deployment can also be triggered manually using `workflow_dispatch`.
