# gatra-frontend — situs publik GATRA

Situs statis untuk landing page dan kebijakan privasi GATRA: game kartu UNO modifikasi untuk
penelitian intervensi psikofinansial remaja.

- Tanpa build step, tanpa framework, tanpa npm.
- Satu file CSS, tanpa JavaScript.
- Tanpa font eksternal, CDN, analytics, cookie, atau iframe.
- Semua gambar dilayani dari repo ini.

Operator: Tim Peneliti GATRA, Lab Media Game dan Mobile, Fakultas Ilmu Komputer,
Universitas Brawijaya.

## Pratinjau lokal

```bash
cd gatra-frontend
python3 -m http.server 8080
```

Buka `http://localhost:8080/` dan `http://localhost:8080/privacy.html`.
Hentikan server dengan `Ctrl+C`. Karena semua tautan relatif, membuka `index.html` langsung dari
berkas juga bisa, tetapi server lokal lebih dekat ke kondisi GitHub Pages.

## Deploy

Situs ini dilayani GitHub Pages dari branch `main` lewat GitHub Actions.

1. Repo publik: `github.com/hadilo/gatra-frontend`.
2. Workflow `.github/workflows/pages.yml` berjalan pada setiap push ke `main` dan pada
   `workflow_dispatch`.
3. Di GitHub: **Settings → Pages → Build and deployment → Source: GitHub Actions**.
   Jangan pilih "Deploy from a branch"; artefak diunggah oleh workflow.
4. Setelah deploy pertama, situs tersedia di:
   - `https://hadilo.github.io/gatra-frontend/`
   - `https://hadilo.github.io/gatra-frontend/privacy.html`

Workflow memakai `actions/configure-pages`, `actions/upload-pages-artifact`, dan
`actions/deploy-pages`, dengan izin `pages: write` dan `id-token: write`. Karena akar repo yang
di-deploy, `.nojekyll` disertakan agar GitHub tidak memproses situs lewat Jekyll.

Alur publikasi (dikerjakan Tech Lead, bukan bagian dari perubahan berkas ini):

```bash
cd gatra-frontend
git init
git add .
git commit -m "Add GATRA landing page and privacy policy"
git remote add origin git@github.com:hadilo/gatra-frontend.git
git push -u origin main
```

## Struktur berkas

```
gatra-frontend/
├── index.html                  # landing page (bahasa Indonesia)
├── privacy.html                # kebijakan privasi
├── styles.css                  # satu-satunya stylesheet
├── assets/img/                 # Banner, Table_0, Mystery_Card, Deck, favicon
├── docs/play-data-safety.md    # pemetaan data untuk form Play Data Safety
├── .github/workflows/pages.yml # deploy GitHub Pages
├── .nojekyll                   # matikan pemrosesan Jekyll
├── robots.txt                  # allow semua, menunjuk sitemap
├── sitemap.xml                 # dua halaman publik
└── README.md
```

Aset di `assets/img/` adalah salinan byte-identik dari `gatra/assets/images/`
(`Banner.png`, `Table_0.png`, `Mystery_Card.png`, `Deck.png`). `favicon.png` dibuat dari
`Deck.png` dengan `sips -z 64 64`. `Mockup.png` dan `MockUp 0.png` sengaja tidak dipakai.
Kalau aset sumber berubah, salin ulang, jangan mengedit salinan di sini.

## Mengganti alamat email kontak

Alamat saat ini **placeholder** dan ditandai di halaman maupun di sumber:

```text
kontak@example.ac.id   ← PLACEHOLDER
```

Penggantian alamat adalah **tiket 12**, bukan bagian dari tiket 11. Build pilot yang masuk Play
Store tidak boleh dirilis selama alamat placeholder masih ada. Setelah alamat nyata tersedia,
ganti di semua titik berikut:

1. `index.html`
   - tautan CTA: `href="mailto:kontak@example.ac.id?subject=Pilot%20GATRA"`
   - teks catatan placeholder di `#catatan-email` (termasuk label `PLACEHOLDER`)
   - komentar `<!-- TODO(contact-email): ... -->` di dekat CTA
2. `privacy.html`
   - catatan `.note--alert` pada bagian `#kontak` (termasuk label `PLACEHOLDER`)
   - komentar `<!-- TODO(contact-email): ... -->`
3. `docs/play-data-safety.md` — bagian "Ringkasan deklarasi" dan checklist sebelum pengajuan.
4. `README.md` — bagian ini.

Setelah diganti, verifikasi:

```bash
grep -RIn "PLACEHOLDER\|TODO(contact-email)" .
grep -RIn "example.ac.id" .
```

Keduanya harus tidak menghasilkan apa pun. Play Console akan menolak kebijakan privasi yang
alamat kontaknya tidak bisa dipakai.

## Verifikasi

```bash
cd gatra-frontend
python3 -m http.server 8080 &
curl -s -o /dev/null -w "%{http_code} %{url_effective}\n" \
  http://localhost:8080/ \
  http://localhost:8080/privacy.html \
  http://localhost:8080/robots.txt \
  http://localhost:8080/sitemap.xml \
  http://localhost:8080/assets/img/Banner.png
grep -RIn "http://\|https://" --include="*.html" --include="*.css" --include="*.js" --include="*.xml" --include="*.txt" .
kill %1
```

Alamat `https://hadilo.github.io/gatra-frontend/...` hanya boleh muncul di `<link rel="canonical">`,
meta Open Graph/Twitter, `robots.txt`, dan `sitemap.xml`. `http://www.sitemaps.org` di
`sitemap.xml` adalah namespace XML, bukan permintaan jaringan.

## Aturan yang tidak boleh dilanggar

- Jangan menambah `<script>`, analytics, cookie, iframe, atau aset dari domain lain.
- Jangan memakai font eksternal; gunakan system font stack di `styles.css`.
- Jangan mengklaim GATRA tersedia di Play Store, punya hasil penelitian, atau memuat testimoni.
- Angka yang ditampilkan di halaman harus berasal dari `gatra/AGENTS.md` atau
  `gatra/docs/api-contract.md`.
- Perubahan bentuk payload backend harus disertai pembaruan `privacy.html` dan
  `docs/play-data-safety.md`.
