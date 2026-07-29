# aabhara.shop

Landing page and product catalogue for **Aabhara**, a jewellery shop. Live at [aabhara.shop](https://aabhara.shop).

Static site — plain HTML/CSS, no framework, no build step. The catalogue PDF is served from the same origin at `/catalogue.pdf`, so links stay on the brand domain and WhatsApp/Instagram previews show the catalogue cover (via Open Graph tags pointing at `/cover.jpg`).

## Structure

```
index.html      landing page
styles.css      design tokens + layout (kasavu ivory/gold palette)
logo.png        brand logo (transparent), shown top-left
catalogue.pdf   the current catalogue (web-compressed)
cover.jpg       page 1 of the catalogue, used on the page and in link previews
```

## Local preview

```bash
python3 -m http.server 8000
# open http://localhost:8000
```

## Deployment

Hosted on Vercel, connected to this repo. Every push to `main` triggers an automatic deploy — there is no manual publish step.

DNS (Namecheap → Vercel): `A @ → <IP from Vercel domain settings>`, `CNAME www → <value from Vercel domain settings>`. TLS is provisioned automatically by Vercel.

## Updating the catalogue

Design exports are typically print-resolution (400 MB+). Compress before committing — GitHub blocks files over 100 MB and customers are on mobile data.

```bash
# 1. Compress to screen resolution (~needs ghostscript: brew install ghostscript / apt install ghostscript)
gs -sDEVICE=pdfwrite -dCompatibilityLevel=1.5 -dPDFSETTINGS=/ebook \
   -dNOPAUSE -dQUIET -dBATCH -sOutputFile=catalogue.pdf original.pdf

# 2. Regenerate the cover (needs poppler: brew install poppler / apt install poppler-utils)
pdftoppm -jpeg -jpegopt quality=85 -scale-to 1200 -f 1 -l 1 catalogue.pdf cover
mv cover-01.jpg cover.jpg

# 3. Ship it
git add catalogue.pdf cover.jpg
git commit -m "Update catalogue"
git push
```

Keep the filename exactly `catalogue.pdf` — the buttons, the download link, and anything customers have bookmarked all point at that path.

## To-do after first deploy

- If the PDF size changes, update the "17 MB" label next to the download link.
