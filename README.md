# OCMem — website

Landing page for **OCMem**, a token-cheap long-term memory plugin for [OpenCode](https://opencode.ai).

- Pure static site: a single `index.html` (Chinese) plus `en.html` (English)
- No CDN, no build step, no external assets
- Ready to publish with GitHub Pages from the repository root

**Live:** https://fish303303.github.io/ocmem-site/ · **English:** https://fish303303.github.io/ocmem-site/en.html

[中文说明](./README.zh-CN.md)

## Local preview

Double-click `index.html`, or serve the folder:

```bash
python -m http.server 8080
```

Then open http://localhost:8080

## Publish (GitHub Pages)

Repository **Settings → Pages → Source: Deploy from a branch → `main` / `(root)` → Save**.

The site is served at:

```
https://<user>.github.io/<repo>/
```

## Files

| File | Purpose |
| --- | --- |
| `index.html` | Chinese landing page |
| `en.html` | English landing page |
| `.nojekyll` | Skip Jekyll processing |
| `README.md` / `README.zh-CN.md` | Repository docs |

## License

MIT
