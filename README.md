# VCF QC & Relatedness Tutorial (Quarto)

A Quarto tutorial covering VCF quality control, diversity statistics,
relatedness/kinship estimation, and breeding-group selection with `swingeR`.

## Repository layout

```
vcf-tutorial/
├── _quarto.yml       # site config
├── index.qmd         # the tutorial itself
└── README.md
```

## 1. Prerequisites

- [Quarto CLI](https://quarto.org/docs/get-started/) installed
  (RStudio >= 2022.07 bundles it; otherwise download from quarto.org)
- R with the packages listed in the "Setup" section of `index.qmd`
- Your VCF + metadata files, placed in this folder (or update the paths in
  the "File paths" chunk of `index.qmd` to point elsewhere)
- The `swingeR` package `.tar.gz`, or `swingeR` already installed

## 2. Render locally

From the project folder:

```bash
quarto render
```

or, in RStudio, open `index.qmd` and click **Render**.

This produces the site in `docs/` (as set in `_quarto.yml`), which is the
folder GitHub Pages will serve from.

To preview with live reload while editing:

```bash
quarto preview
```

## 3. Publish to GitHub Pages

### Option A: Serve the `docs/` folder directly (simplest)

1. Push this whole folder (including the rendered `docs/`) to a GitHub repo.
2. In the repo settings on GitHub: **Settings -> Pages -> Build and
   deployment -> Source: Deploy from a branch**, then set **Branch: main**,
   **Folder: /docs**.
3. Your site will be live at `https://<username>.github.io/<repo>/`.

Re-render (`quarto render`) and commit/push the updated `docs/` folder each
time you change `index.qmd`.

### Option B: `quarto publish gh-pages` (handles the branch/push for you)

```bash
quarto publish gh-pages
```

This renders the site and pushes it to a `gh-pages` branch automatically.
Then in GitHub **Settings -> Pages**, set the source to the `gh-pages`
branch. Subsequent updates are just:

```bash
quarto publish gh-pages
```

### Option C: GitHub Actions (auto-rebuild on every push)

Add `.github/workflows/publish.yml`:

```yaml
on:
  push:
    branches: main

name: Render and Publish

jobs:
  build-deploy:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v4
      - uses: quarto-dev/quarto-actions/setup@v2
      - uses: r-lib/actions/setup-r@v2
      - name: Install R packages
        run: |
          install.packages(c("ggplot2","dplyr","tidyr","patchwork","reshape2",
            "tidyverse","vegan","hierfstat","visNetwork","BiocManager"))
          BiocManager::install(c("vcfR","SNPRelate","gdsfmt"))
        shell: Rscript {0}
      - uses: quarto-dev/quarto-actions/publish@v2
        with:
          target: gh-pages
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

Note: this workflow doesn't install `dartR.base`, `dartR.popgen`, or
`swingeR` (not on CRAN/Bioconductor) — you'd need to add steps to install
those from their source (e.g. `remotes::install_github(...)` for dartR, and
committing/installing the `swingeR` tarball from within the repo) if you
want the Action to fully reproduce the analysis rather than just rendering
pre-computed output.

## 4. A note on data files

VCF/genomic data files are usually too large (and often shouldn't be public)
for a GitHub repo. Two common approaches for a teaching site like this:

- **Render locally with the real data, then only commit the rendered
  `docs/` HTML** (Option A above) — the site shows real results, but the
  raw data never touches GitHub.
- **Use a small simulated/subset dataset** (like `SPBRsimulated.vcf.gz`)
  that's safe to commit, so the Quarto document is fully reproducible by
  anyone who clones the repo.
