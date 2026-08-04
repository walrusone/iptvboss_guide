# IPTVBoss Guide

This repository contains the source files for the official IPTVBoss user guide.

The guide is built with [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/) and deployed to GitHub Pages after approved changes are merged into `main`.

## Requirements

- Python 3
- pip
- Git

## Local setup

### Linux or macOS

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### Windows PowerShell

```powershell
py -m venv .venv
.venv\Scripts\Activate.ps1
```

Install the dependencies:

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

Run the local documentation server:

```bash
mkdocs serve
```

Open the local address printed in the terminal. The default is normally `http://127.0.0.1:8000/`.

## Production build

Run:

```bash
mkdocs build --strict
```

The generated site is placed in `site/`. The `site/` directory is generated and must not be committed.

## Contribution workflow

1. Create a branch from `main`.
2. Make documentation changes in Markdown and add sanitized screenshots under `docs/assets/images/`.
3. Run `mkdocs build --strict` locally.
4. Open a pull request.
5. Merge only after validation passes and the content is reviewed.

The pull-request workflow validates the site without deploying it. Merges to `main` deploy through GitHub Pages.

