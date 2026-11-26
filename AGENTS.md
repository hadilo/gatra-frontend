# AGENTS.md — GATRA Frontend

Situs publik GATRA: landing page dan kebijakan privasi. Repo ini **terpisah** dari klien Flutter
dan backend. Isinya HTML dan CSS statis tanpa build step, tanpa JavaScript aplikasi, dan tanpa
dependency runtime. Baca ini sebelum mengubah halaman.

## Status

Live di `https://hadilo.github.io/gatra-frontend/` dan `.../privacy.html`. Deploy otomatis dari
branch `main` lewat GitHub Actions ke GitHub Pages. Halamannya statis: tidak ada backend, tidak ada
form, tidak ada autentikasi. Situs harus tetap hidup walaupun backend GATRA mati.

## Di mana dokumennya

Dokumen otoritatif tinggal di repo klien, diclone sebagai sibling `../gatra/`:

| Dokumen | Path |
|---|---|
| Kontrak API v1 — sumber kebenaran isi privacy | `../gatra/docs/api-contract.md` |
| Spec Phase 2, bagian "Privasi dan Data" | `../gatra/.scratch/phase-2/spec.md` |
| Tiket situs (`11`) dan email kontak (`12`) | `../gatra/.scratch/phase-2/issues/` |
| Konvensi dan angka tetap permainan | `../gatra/AGENTS.md` |
| Konsep abstrak | `../gatra/GLOSSARY.md` |

Jika `../gatra/` tidak ada, **berhenti dan minta konteks**. Jangan menebak angka, janji fitur, atau
praktik data. Jangan membuat `.scratch/` terpisah di repo ini.

## Aturan yang mengikat

1. **Nol permintaan ke domain lain.** Tidak ada CDN, Google Fonts, analytics, piksel, cookie,
   iframe, atau skrip pihak ketiga. Font memakai system stack; gambar dan CSS dilayani sendiri.
   URL absolut `https://hadilo.github.io/gatra-frontend/...` hanya boleh di `rel="canonical"`,
   meta Open Graph/Twitter, dan `sitemap.xml`.
2. **Tanpa build step.** Tidak ada npm, bundler, framework, atau file yang perlu dikompilasi.
   Yang di-commit harus persis yang disajikan.
3. **Email kontak masih placeholder.** `kontak@example.ac.id` wajib tetap bertanda `PLACEHOLDER`
   dan `TODO(contact-email)` sampai tiket `12` selesai. Jangan menghapus penandanya dan jangan
   mengarang alamat pengganti.
4. **Isi privacy mengikuti kontrak.** Setiap jenis data yang disebut harus benar-benar ada di
   `../gatra/docs/api-contract.md` (§3 dan §4). Retensi 365 hari, tanpa PII, pihak ketiga
   Sumopod/Gemini, dan hak akses/hapus tidak boleh dilunakkan tanpa perubahan kontrak.
5. **Tanpa klaim yang belum benar.** Jangan menulis "tersedia di Play Store", jumlah unduhan,
   testimoni, hasil penelitian, atau statistik yang tidak ada di dokumen acuan. Operator adalah
   Tim Peneliti GATRA, Lab Media Game dan Mobile, Fakultas Ilmu Komputer, Universitas Brawijaya.
6. **Aset disalin, bukan digambar ulang.** Gambar berasal dari `../gatra/assets/images/` dan harus
   byte-identik. `Mockup.png` dan `MockUp 0.png` tidak dipakai di runtime.
7. **Aksesibilitas adalah syarat, bukan tambahan.** `lang="id"`, tepat satu `h1` per halaman,
   landmark semantik, `alt` bermakna (dekoratif memakai `alt=""` + `aria-hidden`), `:focus-visible`
   terlihat, kontras ≥ WCAG AA, target sentuh ≥ 44 px, dan tanpa scroll horizontal 360–1440 px.

## Perintah

```bash
# Preview lokal
cd gatra-frontend && python3 -m http.server 8080
# lalu buka http://localhost:8080/

# Verifikasi sebelum commit
grep -RIn "http://\|https://" --include="*.html" --include="*.css" --include="*.xml" --include="*.txt" .
grep -RIn "PLACEHOLDER\|TODO(contact-email)" .
grep -RIn "<script\|<iframe\|analytics\|gtag\|pixel" --include="*.html" .
```

## Deploy

Push ke `main` memicu `.github/workflows/pages.yml`. Pages harus disetel ke
**Settings → Pages → Source: GitHub Actions**; kalau disetel "Deploy from a branch", workflow tidak
men-deploy apa pun. URL akhir tidak berubah antar-deploy.

## Menutup tiket

Checklist tiket ada di repo klien (`../gatra/.scratch/phase-2/issues/`). Centang hanya yang sudah
diverifikasi lewat perintah atau URL nyata, dan tulis bukti di `## Comments` di sana.

## Jangan lakukan

- Menambah tracker, cookie, form, atau iframe pihak ketiga.
- Menambah JavaScript kecuali benar-benar tidak bisa diselesaikan dengan HTML/CSS.
- Mengubah identitas operator, retensi, atau janji privasi tanpa memperbarui dokumen acuan.
- Meng-commit `Mockup.png`, `MockUp 0.png`, atau tangkapan layar bertanda air.
- Menaruh kunci, token, atau `.env` dalam bentuk apa pun.
