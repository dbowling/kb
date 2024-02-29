# MkDocs Site Generator

[MkDocs](https://www.mkdocs.org/) is a fast, simple and downright gorgeous static site generator that's geared towards building project documentation.

## Installation

```shell
brew install mkdocs
```

## Configuration

```yaml
site_name: K8s Documentation

site_url: https://dbowling.github.io/kb
repo_url: https://github.com/umd-lib/dbowling/kb
edit_uri: edit/main/docs/
docs_dir: docs
theme: readthedocs
```


## Usage

Run a server locally. It will watch your directory and rebuild the site as you make changes.

```shell
mkdocs serve

# Deploy to GitHub Pages
mkdocs gh-deploy
```

You can also build it to a directory and deploy it to a web server, or deploy it to GitHub Pages.

> [!NOTE]
> GitHub Pages only work on paid accounts or public repositories.