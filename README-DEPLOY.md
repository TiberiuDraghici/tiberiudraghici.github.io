# Pachet SEO + LLM-Visibility pentru tiberiudraghici.github.io

## Conținut pachet

```
site/
├── index.html                          → înlocuiește root/index.html
├── robots.txt                          → root/robots.txt
├── sitemap.xml                         → root/sitemap.xml
├── llms.txt                            → root/llms.txt
├── manifest.json                       → root/manifest.json
└── .github/
    └── workflows/
        └── sitemap.yml                 → root/.github/workflows/sitemap.yml
```

## Pași de deployment

### 1. Backup
```bash
git checkout -b seo-refactor
cp index.html index.html.backup
```

### 2. Copiază fișierele
Mută toate fișierele în root-ul repo-ului `tiberiudraghici.github.io`,
păstrând structura de directoare (`.github/workflows/sitemap.yml`).

### 3. Active externe ce trebuie create manual (NU sunt incluse)

Acestea sunt fișiere binare/imagini ce trebuie generate separat:

- `/favicon.svg` — icon vectorial monogramă „TD"
- `/favicon.png` — fallback 32×32 PNG
- `/apple-touch-icon.png` — 180×180 PNG
- `/icons/icon-192.png` — 192×192 PNG (PWA)
- `/icons/icon-512.png` — 512×512 PNG (PWA)
- `/icons/icon-maskable-512.png` — 512×512 PNG (PWA, safe zone 80%)

Generare rapidă cu https://realfavicongenerator.net/ (upload Prezentare.jpg
sau o monogramă „TD" creată în Figma/Inkscape).

### 4. Optimizare imagine hero (opțional, recomandat)

```bash
# Cu cwebp (Google) — instalare: apt install webp
cwebp -q 85 Prezentare.jpg -o prezentare-800.webp
cwebp -q 85 -resize 400 0 Prezentare.jpg -o prezentare-400.webp

# Cu avifenc (mai bun) — instalare: apt install libavif-bin
avifenc --min 30 --max 40 Prezentare.jpg prezentare-800.avif
```

Apoi în `index.html` decomentează block-ul `<picture>` (marcat cu TODO)
și actualizează căile.

### 5. Tailwind CSS — build static (CRITIC pentru performanță)

```bash
npm init -y
npm install -D tailwindcss
npx tailwindcss init

# Creează src/input.css cu:
# @tailwind base; @tailwind components; @tailwind utilities;

# Build:
npx tailwindcss -i ./src/input.css -o ./dist/style.css --minify

# Apoi în index.html înlocuiește:
#   <script src="https://cdn.tailwindcss.com"></script>
# Cu:
#   <link rel="stylesheet" href="/dist/style.css">
```

### 6. Verificare după deploy

**Schema JSON-LD:**
- Google Rich Results Test: https://search.google.com/test/rich-results
- Schema.org Validator: https://validator.schema.org/

**Core Web Vitals:**
- PageSpeed Insights: https://pagespeed.web.dev/
- Lighthouse (din DevTools)

**Indexare:**
- Google Search Console: https://search.google.com/search-console
  → adaugă proprietatea, verifică prin meta tag, submit sitemap.xml
- Bing Webmaster Tools: https://www.bing.com/webmasters
  → submit sitemap.xml

**LLM Visibility:**
- Caută în Perplexity: "Cine este Tiberiu Drăghici specialist agricol Sibiu?"
- Caută în ChatGPT (cu web search): aceeași întrebare
- Caută în Claude (cu web): aceeași întrebare

Rezultatele se vor îmbunătăți în 2-6 săptămâni după ce crawlerele
re-indexează site-ul.

### 7. TODO după ce creezi conturile externe

În `index.html`, în obiectul JSON-LD `Person`, adaugă proprietatea
`sameAs` cu URL-urile reale:

```json
"sameAs": [
    "https://orcid.org/0000-0000-0000-0000",
    "https://scholar.google.com/citations?user=XXXXXX",
    "https://www.researchgate.net/profile/Tiberiu-Draghici",
    "https://www.linkedin.com/in/tiberiu-draghici",
    "https://github.com/tiberiudraghici"
]
```

Prioritate maximă: **ORCID** (cea mai puternică ancoră pentru
entity disambiguation la LLM-uri).

## Verificare locală rapidă

```bash
# Validare HTML
npx html-validate index.html

# Servire locală pentru test
npx serve .
# Apoi deschide http://localhost:3000
```

## Notă despre GitHub Actions workflow

Workflow-ul `sitemap.yml` rulează automat la fiecare push care
modifică un fișier `.html`. Generează sitemap.xml și face commit
înapoi în repo. La primul push, va fi necesar să acorzi permisiuni
de scriere workflow-urilor:

**Settings → Actions → General → Workflow permissions →
"Read and write permissions" → Save**
