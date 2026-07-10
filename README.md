# ConvertSnap – Deployment & Customization Guide

A free, privacy-first image converter web app. Converts JPG, PNG, WEBP, GIF, BMP, TIFF, SVG, HEIC, AVIF and more — plus PDF export. All processing runs in the visitor's browser; no files ever touch a server.

---

## Files Included

| File | Purpose |
|------|---------|
| `index.html` | Main app (self-contained — all CSS & JS inline) |
| `privacy.html` | Privacy Policy page |
| `terms.html` | Terms of Service page |
| `README.md` | This guide |

---

## Quick Deploy (5 minutes)

### Option A — Netlify (Recommended, Free)
1. Go to [netlify.com](https://netlify.com) and sign up.
2. Drag the entire folder onto the Netlify deploy drop zone.
3. Your site is live at `https://random-name.netlify.app`.
4. Add a custom domain under **Site settings → Domain management**.

### Option B — Vercel
1. Install Vercel CLI: `npm i -g vercel`
2. Run `vercel` in this folder and follow the prompts.

### Option C — GitHub Pages
1. Push this folder to a GitHub repository.
2. Go to **Settings → Pages → Source** and select your branch.
3. Site goes live at `https://yourusername.github.io/repo-name/`.

### Option D — Traditional Web Hosting (Apache/Nginx)
Upload all files to your `public_html` or `www` directory via FTP/SFTP.
No server-side config needed — it's all static HTML.

### Option E — Cloudflare Pages
1. Connect your GitHub repo at [pages.cloudflare.com](https://pages.cloudflare.com).
2. Build command: *(none — static site)*
3. Output directory: `/` (root)

---

## Setting Up Ads

Ad slots are pre-wired in the HTML with clearly labeled comments. Search for `<!-- AD SLOT` in `index.html` to find each one.

### Ad Slot Map

| Slot ID | Dimensions | Position | Visibility |
|---------|-----------|----------|------------|
| `ad-slot-left-1` | 160 × 600 (Wide Skyscraper) | Left sidebar | Desktop only (≥1200px) |
| `ad-slot-left-2` | 160 × 250 (Small Rectangle) | Left sidebar | Desktop only |
| `ad-slot-right-1` | 160 × 600 | Right sidebar | Desktop only |
| `ad-slot-right-2` | 160 × 250 | Right sidebar | Desktop only |
| `ad-slot-top` | 728 × 90 (Leaderboard) | Below hero | Mobile / narrow |
| `ad-slot-bottom` | 728 × 90 | Below format section | Mobile / narrow |

On screens narrower than 1200px, the sidebars hide and the leaderboard slots appear instead — so you always have ad coverage.

---

### Google AdSense Integration

1. Sign up at [adsense.google.com](https://adsense.google.com) and get your Publisher ID (`ca-pub-XXXXXXXXXXXXXXXX`).
2. Add the AdSense script in the `<head>` of `index.html`:

```html
<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-XXXXXXXXXXXXXXXX"
        crossorigin="anonymous"></script>
```

3. Replace each placeholder `<div class="ad-unit ...">` with an AdSense `<ins>` tag. Example for the left skyscraper:

```html
<!-- Replace this entire div: -->
<div class="ad-unit ad-unit--tall" id="ad-slot-left-1">...</div>

<!-- With this: -->
<ins class="adsbygoogle"
     style="display:inline-block;width:160px;height:600px"
     data-ad-client="ca-pub-XXXXXXXXXXXXXXXX"
     data-ad-slot="YOUR_AD_SLOT_ID"></ins>
<script>(adsbygoogle = window.adsbygoogle || []).push({});</script>
```

4. Use **Auto ads** to let Google fill all slots automatically — paste the script once and AdSense handles placement.

---

### Ezoic Integration
Ezoic works as a proxy layer — connect your domain in the Ezoic dashboard and they inject ads automatically. No code changes needed beyond adding the Ezoic script to `<head>`.

---

### Media.net Integration
Replace placeholder divs with Media.net tags. Requires approval; sign up at [media.net](https://www.media.net).

---

### Monumetric / Mediavine / AdThrive
These premium networks require minimum monthly traffic thresholds. Once approved, they provide a header script and manage all placements. Replace placeholder divs or let their script auto-fill slots.

---

## Customization

### Rename the Tool
Search and replace `ConvertSnap` in all three HTML files.

### Change Colors
Edit the CSS variables at the top of `index.html` under `/* ─── TOKENS ── */`:
```css
:root {
  --teal:  #06B6D4;   /* Primary accent */
  --amber: #F59E0B;   /* Convert button */
  --bg:    #0F172A;   /* Page background */
  /* ... etc */
}
```

### Add Google Analytics
Paste your GA4 snippet in the `<head>` of each HTML file:
```html
<!-- Google tag (gtag.js) -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-XXXXXXXXXX');
</script>
```

### Add a Favicon
Create a 32×32 and 180×180 PNG icon, then add to `<head>`:
```html
<link rel="icon"       href="/favicon.ico" />
<link rel="apple-touch-icon" href="/apple-touch-icon.png" />
```

### Custom Domain
Point your domain's DNS to your host (Netlify, Vercel, etc.) and enable HTTPS (free with Let's Encrypt on all recommended hosts).

---

## SEO Checklist

- [ ] Update `<title>` and `<meta name="description">` with your real domain/brand
- [ ] Add `<meta property="og:url">` with your live URL
- [ ] Submit `sitemap.xml` to Google Search Console (create a simple one listing your 3 pages)
- [ ] Add structured data (Schema.org `WebApplication`) for rich search results
- [ ] Verify site in Google Search Console

---

## Supported Formats (Technical Notes)

**Input:** Any format the user's browser can decode via `<img>` or `URL.createObjectURL`. Modern browsers support JPG, PNG, WEBP, GIF, BMP, SVG, AVIF, ICO natively. HEIC/HEIF support varies by browser (Safari on Mac/iOS has native support; Chrome/Firefox may need a polyfill — see below).

**Output:** JPG, PNG, WEBP, GIF, BMP (via Canvas API) and PDF (via jsPDF, loaded from cdnjs).

### HEIC Support on Chrome/Firefox
Add the `heic2any` library to improve HEIC support:
```html
<script src="https://cdn.jsdelivr.net/npm/heic2any@0.0.4/dist/heic2any.min.js"></script>
```
Then pre-process HEIC files before passing them to the Canvas:
```javascript
if (file.type === 'image/heic' || file.name.toLowerCase().endsWith('.heic')) {
  const blob = await heic2any({ blob: file, toType: 'image/jpeg', quality: 0.9 });
  file = blob; // use converted blob
}
```

---

## Legal Notes

- Update the contact emails in `privacy.html` and `terms.html` (`privacy@convertsnap.app`, `legal@convertsnap.app`) to your real addresses.
- If serving EU users, add a cookie consent banner for GDPR compliance (required when using ad networks that set cookies).
- Review your ad network's publisher policies — some require specific disclosures near ad units (e.g., the "Advertisements" label, which is already included in the placeholder divs).

---

## Performance Tips

- The main `index.html` is fully self-contained — no build step required.
- Enable Gzip/Brotli compression on your host for faster loads.
- Use Cloudflare's free CDN in front of any host for global edge caching.
- jsPDF (~200KB) loads from cdnjs — it's only needed for PDF export, so you can lazy-load it if desired.

---

## License

The ConvertSnap application code in this package is provided for your use in building and publishing a commercial web tool. You retain full ownership of the deployed product.
