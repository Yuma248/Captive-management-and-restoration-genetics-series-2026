## BIOL3722_2026 Conservation and Ecological Genetics Lab

A Quarto tutorial covering VCF quality control, diversity statistics,
relatedness/kinship estimation, and breeding-group selection with `swingeR`.

This repo works two ways:

- **As a webpage** — `index.qmd` renders to a static site with all the code
  shown for reading/copying, but code is **not executed** at render time
  (so the page builds without needing any data files or packages).
- **As a live RStudio project** — clone the repo and run the code chunks
  for real against your own data.

## Repository layout

```
vcf-tutorial/
├── _quarto.yml          # site config
├── index.qmd             # the tutorial itself (display-only on the web)
├── vcf-tutorial.Rproj    # RStudio project file
└── README.md
```

## Option A: Just read the page

Nothing to install — open the published GitHub Pages URL (see "Publish to
GitHub Pages" below) and read/copy code blocks as needed. Every code chunk
has a copy-to-clipboard icon on hover in the rendered HTML.

## Option B: Run it for real in RStudio, straight from GitHub

1. In RStudio: **File -> New Project -> Version Control -> Git**, then
   paste the repo URL (e.g. `https://github.com/<username>/<repo>.git`)
   and click **Create Project**. RStudio will clone the repo and open it
   as a project (recognized via `vcf-tutorial.Rproj`).

   Alternatively, from an R console you already have open:
   ```r
   usethis::create_from_github("https://github.com/<username>/<repo>", destdir = "~/Projects")
   ```

2. Open `index.qmd` in RStudio.

3. Run chunks interactively as you go: click the green "play" arrow on
   each chunk, or **Run -> Run All** from the editor toolbar, or step
   through with Ctrl/Cmd+Enter line by line — same as any `.R`/`.Rmd`
   script. Since the YAML sets `eval: false`, running chunks this way
   (interactively in RStudio) still works fine — that setting only
   affects what happens during `quarto render`, not manual chunk
   execution.

4. Update the file paths in the "File paths" chunk near the top of
   `index.qmd` to point at your own VCF/metadata files before running.

5. Install any missing packages first by running the (non-evaluated,
   copy-only) install chunk near the top of the document.

## Render the page (optional, only needed if you're editing content)

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

## Publish to GitHub Pages

### Serve the `docs/` folder directly (simplest)

1. Push this whole folder (including the rendered `docs/`) to a GitHub repo.
2. In the repo settings on GitHub: **Settings -> Pages -> Build and
   deployment -> Source: Deploy from a branch**, then set **Branch: main**,
   **Folder: /docs**.
3. Your site will be live at `https://<username>.github.io/<repo>/`.

Re-render (`quarto render`) and commit/push the updated `docs/` folder each
time you change `index.qmd`.

### `quarto publish gh-pages` (handles the branch/push for you)

```bash
quarto publish gh-pages
```

This renders the site and pushes it to a `gh-pages` branch automatically.
Then in GitHub **Settings -> Pages**, set the source to the `gh-pages`
branch. Subsequent updates are just:

```bash
quarto publish gh-pages
```

### GitHub Actions (auto-rebuild on every push)

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

## A note on data files

VCF/genomic data files are usually too large (and often shouldn't be public)
for a GitHub repo. Two common approaches for a teaching site like this:

- **Render locally with the real data, then only commit the rendered
  `docs/` HTML** (see "Serve the docs/ folder directly" above) — the site
  shows real results, but the raw data never touches GitHub.
- **Use a small simulated/subset dataset** (like `SPBRsimulated.vcf.gz`)
  that's safe to commit, so the Quarto document is fully reproducible by
  anyone who clones the repo.
