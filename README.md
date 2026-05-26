# liveshotapp.com

Static website for LiveShot — landing page + privacy + terms + support.
Vanilla HTML/CSS, no build step. Served via GitHub Pages on the custom
domain `liveshotapp.com`.

## Structure

```
.
├── index.html              landing page
├── privacy/index.html      privacy policy (App Store URL)
├── terms/index.html        terms of use   (App Store URL)
├── support/index.html      FAQ + support email (App Store URL)
├── 404.html                missing-page fallback
├── CNAME                   GitHub Pages custom domain → liveshotapp.com
├── robots.txt              crawler config
├── sitemap.xml             URL list for search engines
└── assets/style.css        shared styles
```

The four URLs Apple's App Review will click are:

- `https://liveshotapp.com/`
- `https://liveshotapp.com/privacy/`
- `https://liveshotapp.com/terms/`
- `https://liveshotapp.com/support/`

All must return 200 before submission or the listing will be rejected.

---

## One-time setup (do this once, then forget)

### 1. Create the GitHub repo

```bash
cd /Users/fernando/Documents/GitHub/LiveShot/liveshot-website
git init -b main
git add .
git commit -m "Initial site"
gh repo create ferguimara/liveshot-website --public --source=. --remote=origin --push
```

### 2. Enable GitHub Pages

In the new repo's settings:
**Settings → Pages → Build and deployment**
- Source: **Deploy from a branch**
- Branch: **main** / **/ (root)**
- Custom domain: **liveshotapp.com**
- Tick **Enforce HTTPS** (it'll be available a few minutes after the cert provisions)

The `CNAME` file in the repo already tells GitHub the custom domain.

### 3. DNS at your registrar

Add **four A records** at the apex (`@`) pointing to GitHub Pages's IPs:

```
@   A   185.199.108.153
@   A   185.199.109.153
@   A   185.199.110.153
@   A   185.199.111.153
```

And one **CNAME** for `www`:

```
www CNAME ferguimara.github.io.
```

(Replace `ferguimara` with your actual GitHub username if different.)

DNS propagation usually takes 15-30 min. You can check with:

```bash
dig liveshotapp.com +short
# should return the four GitHub IPs above
```

### 4. Verify

Once DNS resolves and the GitHub Pages cert is issued (usually 5-10 min after
DNS is live), all four URLs should return 200:

```bash
for path in / /privacy/ /terms/ /support/; do
  echo -n "https://liveshotapp.com$path → "
  curl -sI "https://liveshotapp.com$path" | head -1
done
```

---

## Updating content

Just edit the HTML and push. GitHub Pages re-deploys within a minute.

```bash
git add privacy/index.html
git commit -m "Update privacy policy retention window"
git push
```

The privacy/terms HTML is the **source of truth** going forward. The
`liveshot-backend/docs/legal/*.md` files in the backend repo are now
historical drafts — keep editing the HTML here.

---

## Local preview

Open `index.html` directly in a browser, or run a local server:

```bash
python3 -m http.server 8000 --directory .
# → http://localhost:8000
```

Note that with absolute paths (`<a href="/privacy/">`) you need the
local server — `file://` URLs won't resolve them.

---

## Future polish (post-launch)

- App Store badge image once the listing goes live
- Screenshots in the landing hero
- OG image (currently we ship without one)
- Open Graph / Twitter card images for richer link previews
- Cache-busting query string on `style.css` for hard updates
