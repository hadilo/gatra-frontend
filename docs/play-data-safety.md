# GATRA — pemetaan Play Data Safety

Dokumen kerja untuk mengisi form **Data safety** di Play Console. Isinya harus sama dengan
`privacy.html`; kalau form dan kebijakan berbeda, peninjau Play akan menandainya.

Basis data: `gatra/docs/api-contract.md` bagian 3 dan 4, serta
`.scratch/phase-2/spec.md` bagian "Privasi dan Data".

## Ringkasan deklarasi

| Pertanyaan Play Console | Jawaban |
|---|---|
| Apakah aplikasi mengumpulkan atau membagikan data pengguna? | Ya — data aktivitas permainan |
| Apakah data dienkripsi saat transit? | Ya, seluruh permintaan ke backend memakai HTTPS |
| Apakah pengguna dapat meminta penghapusan data? | Ya, lewat email kontak (masih placeholder sampai tiket 12) |
| Apakah data dijual? | Tidak |
| Apakah data dipakai untuk iklan? | Tidak |
| Apakah aplikasi memakai advertising ID? | Tidak |
| Apakah pengumpulan data opsional? | Ya — tanpa jaringan tidak ada data yang dikirim; game tetap dapat dimainkan |
| Apakah ada PII (nama, email, lokasi, kontak, ID perangkat)? | Tidak |

## Tabel data

Kolom "Dibagikan" mengikuti definisi Play: data yang diteruskan ke pihak lain di luar aplikasi dan
backend operator. Penyedia model AI (Sumopod/Gemini) menerima data permainan untuk memproses
keputusan Bot dan catatan Evaluasi, sehingga dicatat sebagai penerima.

| Data GATRA | Kategori Data Safety | Dikumpulkan | Dibagikan | Tujuan | Wajib/opsional | Catatan |
|---|---|---|---|---|---|---|
| ID Sesi (`session_id`) | App activity › Other actions | Ya | Ya — Sumopod (path permintaan) | App functionality, Analytics | Opsional (hanya saat daring) | UUID acak buatan klien; bukan ID perangkat dan tidak terhubung ke akun |
| Hasil Sesi (`result`: menang/kalah/seri/bangkrut) | App activity › Other actions | Ya | Ya — Sumopod (permintaan coach) | App functionality, Analytics | Opsional (hanya saat daring) | Ikut di ringkasan Sesi dan permintaan Evaluasi |
| Saldo pemain dan Bot (awal, akhir, total belanja, jumlah Roll, efek terbaik) | App activity › Other actions | Ya | Ya — Sumopod (giliran Bot dan coach) | App functionality, Analytics | Opsional (hanya saat daring) | Angka permainan, bukan data keuangan nyata |
| Catatan Roll (maks 100 entri: urutan, pihak, tier, biaya, efek, Saldo setelah) | App activity › Other actions | Ya | Ya — Sumopod (permintaan coach) | App functionality, Analytics | Opsional (hanya saat daring) | Tidak pernah memuat kartu di tangan pemain di luar state giliran Bot |
| Jejak Saldo (`trajectories`) | App activity › Other actions | Ya | Ya — Sumopod (permintaan coach) | App functionality, Analytics | Opsional (hanya saat daring) | Deret angka Saldo sepanjang Sesi |
| Durasi Sesi (`duration_seconds`) | App activity › Other actions | Ya | Ya — Sumopod (permintaan coach) | App functionality, Analytics | Opsional (hanya saat daring) | Detik |
| Waktu mulai Sesi (`client_created_at`) | App activity › Other actions | Ya | Tidak | Analytics | Opsional (hanya saat daring) | Stempel waktu dari perangkat; tanpa zona pengguna |
| Versi aplikasi (`app_version`) | App info and performance › Other app performance data | Ya | Tidak | App functionality, Analytics | Otomatis, bukan pilihan pengguna | Dipakai untuk memetakan bug ke build pilot |
| Platform (`platform`, mis. `android`) | App info and performance › Other app performance data | Ya | Tidak | App functionality, Analytics | Otomatis, bukan pilihan pengguna | Nama platform saja, bukan model perangkat |
| Catatan Evaluasi (tiga paragraf coach) | App activity › Other actions | Ya | Tidak | App functionality, Analytics | Opsional (hanya saat daring) | Dihasilkan template atau model, lalu disimpan di ringkasan |

Tidak ada baris untuk **Personal info**, **Location**, **Financial info**, **Contacts**, **Messages**,
**Photos and videos**, **Files and docs**, **Calendar**, **Health and fitness**, **Web browsing**, dan
**Device or other IDs**. Semua dijawab "tidak dikumpulkan".

## Yang dikirim ke penyedia AI

Permintaan giliran Bot (`POST /v1/game/{session_id}/bot-turn`) memuat: nomor giliran, warna aktif,
kartu teratas tumpukan buang, isi tangan Bot, daftar kartu legal, jumlah kartu di tangan pemain,
jumlah kartu di tumpukan tarik, Saldo kedua pihak, dan maksimal 10 kejadian terakhir.

Permintaan coach (`POST /v1/coach/evaluate`) memuat: ID Sesi, hasil, durasi, statistik Saldo kedua
pihak, catatan Roll, jejak Saldo, dan locale.

Tidak ada nama, email, lokasi, kontak, atau ID perangkat di kedua payload. Backend tidak menyimpan
alamat IP (pembatasan laju berjalan di memori) dan log tidak memuat kunci API, prompt penuh, atau
isi percakapan.

## Retensi

Ringkasan Sesi disimpan maksimal 365 hari (`GATRA_RETENTION_DAYS`, default 365), lalu dihapus.
Permintaan penghapusan lebih awal diproses lewat email kontak.

## Sebelum pengajuan ke Play Console

- [ ] Ganti alamat email placeholder `kontak@example.ac.id` dengan alamat nyata (tiket 12).
  Alamat pada form Data safety harus sama dengan yang ada di `privacy.html`.
- [ ] Pastikan build yang diajukan benar-benar tidak mengirim data di luar daftar di atas.
- [ ] Periksa ulang tabel ini bila kontrak API berubah (`docs/api-contract.md`).
