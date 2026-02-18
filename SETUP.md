# JAF Website — Setup and Deployment Guide

## Prerequisites

| Tool | Version | Install |
|------|---------|---------|
| Hugo (extended) | ≥ 0.120.0 | `brew install hugo` |
| Go | ≥ 1.21 | `brew install go` |
| Git | any | pre-installed on macOS |

Verify:
```bash
hugo version   # should show "extended"
go version
```

---

## 1. Install the Congo Theme (Hugo Module)

Run once in the project root:

```bash
# Initialise Go module (use your own GitHub path if you have one)
hugo mod init jaf

# Download Congo
hugo mod get github.com/jpanther/congo/v2
```

This creates `go.mod` and `go.sum`. Commit both.

---

## 2. Local Preview

```bash
hugo server
```

Open http://localhost:1313 in your browser.

- English site: http://localhost:1313/
- Spanish site: http://localhost:1313/es/
- Chinese site:  http://localhost:1313/zh/

The language switcher in the header switches between all three automatically.

---

## 3. Build for Production

```bash
hugo --minify
```

Output lands in `public/`. This folder contains the complete static website ready to upload.

---

## 4. Deploy Options

### Option A — Netlify (recommended, free tier)

1. Push this repo to GitHub.
2. Go to https://netlify.com → New site from Git.
3. Build command: `hugo --minify`
4. Publish directory: `public`
5. Set environment variable: `HUGO_VERSION = 0.120.4`
6. Done. Netlify auto-deploys on every `git push`.

Add a `netlify.toml` in the project root for pinned config:

```toml
[build]
  command = "hugo --minify"
  publish = "public"

[build.environment]
  HUGO_VERSION        = "0.120.4"
  HUGO_ENVIRONMENT    = "production"
  GO_VERSION          = "1.21"

[[headers]]
  for = "/*"
  [headers.values]
    X-Frame-Options       = "DENY"
    X-Content-Type-Options = "nosniff"
    Referrer-Policy       = "strict-origin-when-cross-origin"
```

### Option B — GitHub Pages

```bash
# Add GitHub Actions workflow at .github/workflows/hugo.yml
# (Hugo provides an official workflow template at gohugo.io/hosting/github-pages/)
```

### Option C — Any static host

Upload the contents of `public/` to any web host (AWS S3 + CloudFront, Cloudflare Pages, etc.).

---

## 5. Custom Domain

After deploying to Netlify or GitHub Pages, set your custom domain
(e.g., `justiceaccessfoundation.org`) in the host's DNS settings
and update `baseURL` in `hugo.toml`.

---

## 6. Before Launch — Required Substitutions

Search the content files for these placeholders and replace with real values:

| Placeholder | Replace with |
|---|---|
| `[TBD]` / `[Por Determinar]` / `待定` | IRS-issued EIN |
| `[payment processor]` / `[procesador de pago]` / `[支付处理商]` | Donation processor name (e.g., Stripe, PayPal Giving Fund) |
| `https://justiceaccessfoundation.org/` in `hugo.toml` | Actual domain |
| `[Board Development Committee contact]` | Contact email or form URL |

Quick search:
```bash
grep -r "TBD\|Por Determinar\|待定\|payment processor" content/
```

---

## 7. File Structure Reference

```
jaf/
├── hugo.toml                  Main Hugo config + language definitions
├── go.mod / go.sum            Hugo module files (created by step 1)
├── config/_default/
│   ├── params.toml            Congo theme parameters
│   ├── menus.en.toml          English navigation
│   ├── menus.es.toml          Spanish navigation
│   └── menus.zh.toml          Chinese navigation
├── assets/css/custom.css      Institutional CSS refinements
└── content/
    ├── en/                    English pages
    │   ├── _index.md          Homepage
    │   ├── 02-about.md        → /about/
    │   ├── 03-programs.md     → /programs/
    │   ├── 04-governance.md   → /governance/
    │   ├── 05-funding-transparency.md → /funding/
    │   ├── 06-board-recruitment.md    → /board/
    │   ├── 07-compliance-legal.md     → /compliance/
    │   └── 08-donate.md       → /donate/
    ├── es/                    Spanish pages → /es/[slug]/
    └── zh/                    Chinese pages → /zh/[slug]/
```

---

## 8. Updating Content

All content is plain Markdown. To update a page:

1. Open the relevant file in `content/en/`, `content/es/`, or `content/zh/`.
2. Edit the text below the `---` front matter block.
3. Run `hugo server` to preview.
4. Commit and push — Netlify rebuilds automatically.

---

## 9. Theme Updates

```bash
hugo mod get -u github.com/jpanther/congo/v2
hugo mod tidy
```

---

## Theme Attribution

This site uses [Congo](https://github.com/jpanther/congo) by JP Anther, MIT License.
Congo attribution in the footer is disabled in `params.toml` per JAF's institutional style.
If you wish to re-enable it: set `showThemeAttribution = true` in `config/_default/params.toml`.
