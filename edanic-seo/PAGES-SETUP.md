# Enable your demo site (GitHub Pages)

1. Merge this PR.
2. Repo **Settings → Pages → Build and deployment → Source: GitHub Actions**.
3. Done — every future Edanic PR you merge redeploys the site automatically.

**If `.github/workflows/edanic-pages.yml` is missing from this PR** (the GitHub App may lack the `workflows` permission and the write is skipped — see PR file list), create that file manually with exactly this content:

```yaml
name: Edanic Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: true

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      EDANIC_CONTENT_DIR: "content"
      EDANIC_SITE_ORIGIN: "https://wowma.jp"
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.12'
      - run: pip install markdown pyyaml
      - run: python scripts/build_pages.py
      - uses: actions/upload-pages-artifact@v3
        with:
          path: _site

  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - id: deployment
        uses: actions/deploy-pages@v4
```
