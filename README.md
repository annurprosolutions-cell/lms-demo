# LMS percuma (tanpa bayaran) cuba

Sistem kuliah dalam talian: pelajar mendaftar dengan akaun Google dan terus boleh menonton. Tiada bayaran, tiada langganan.
Berjalan di Cloudflare (Workers + D1) pada pelan percuma. JavaScript biasa, tiada build step.

## Apa yang ada

- Laman awam, log masuk Google, dashboard pelajar, pemain video **YouTube** (khatam automatik) atau **Google Drive** (pelajar tekan Tandakan selesai).
- Siri dan kuliah dengan **thumbnail**, **kategori** (penapis dan lencana), bahagian **Rujukan** (teks Arab kanan ke kiri), kad **guru** dengan gambar, kelayakan dan bio, blok **penafian**.
- **Nota PDF** sebagai pautan fail Google Drive (dialih ke Drive untuk yang layak dan sudah log masuk).
- Butang **Lapor** pada kuliah dan soal jawab; tab **Laporan** di panel.
- Halaman **`/privasi`** dihidang dari pelayan (emel hubungan dan notis privasi dari Tetapan laman).
- Panel `/panel/`: siri dan kuliah, kategori, profil guru, guru tambahan dengan kelulusan, soal jawab, laporan, pengguna, artikel, tetapan laman (nama, teks, penafian, privasi, warna tema, suis ciri).
- Sijil bercetak bila pelajar khatam satu siri (`/sijil/<kod>`) dan kod kelas (golongan): **lalai Sembunyi**, dihidupkan di Tetapan laman.
- Boleh dipasang di telefon (PWA).
- Pilihan, perlu R2: muat naik fail nota PDF, logo, gambar artikel, sandaran harian.

## Peraturan akses

| Siapa | Boleh buat |
|---|---|
| Pelawat (belum log masuk) | Lihat senarai siri; tonton kuliah bertanda "tonton tanpa log masuk" sahaja |
| Pelajar (log masuk Google) | Tonton semua kuliah terbit, tanda khatam, nota, soal jawab, sijil |
| Pelajar dengan kod kelas | Juga course yang dihadkan kepada golongan itu |
| Guru | Urus course sendiri di panel; penerbitan perlu kelulusan admin |
| Admin (`ADMIN_EMELS`) | Semua |

## Tiga nilai yang mesti diisi dalam `wrangler.toml`

`GOOGLE_CLIENT_ID`, `ADMIN_EMELS`, `database_id`. Semak dengan `node tools/semak-konfig.mjs`. Tiada rahsia (secret) diperlukan.

**Tiada SQL perlu dijalankan.** Jadual dicipta sendiri pada permintaan pertama (`src/skema.js`). Untuk menambah jadual atau lajur kemudian, tambah satu langkah baharu di hujung `LANGKAH` dalam fail itu.

## Peta fail

| Fail | Fungsi |
|---|---|
| `public/index.html`, `public/app.js` | Halaman pelajar (laluan hash) |
| `public/panel/` | Panel admin dan guru |
| `public/tema.css`, `public/gaya.css` | Rupa. Warna tema: token `:root` dan `:root[data-warna=...]` |
| `public/efek.js`, `public/sw.js`, `public/manifest.webmanifest`, `public/ikon/` | Gerakan, PWA. Naikkan `VERSI` dalam `sw.js` bila ubah fail statik |
| `src/worker.js` | Laluan utama: `/auth/google`, `/api/saya`, `/api/konfig`, dan penghantaran ke modul lain |
| `src/skema.js` | Skema pangkalan data yang memasang dirinya |
| `src/repo.js` | Semua SQL |
| `src/pelajar-api.js`, `src/lib/pelajar.js` | API pelajar dan peraturan akses |
| `src/admin-api.js` | API panel |
| `src/lib/` | Fungsi tulen: golongan dan kod, kandungan, sesi, sijil, soalan, nota, artikel, tetapan, `media.js` (pautan Drive dan gambar), `laporan.js` |
| `src/artikel-halaman.js`, `src/sijil-halaman.js`, `src/privasi-halaman.js` | Halaman yang dihasilkan pelayan (artikel, sijil, dasar privasi) |
| `src/sandaran.js` | Sandaran D1 ke R2 (pilihan) |
| `tools/tukar-nama.mjs` | Tukar nama laman di semua fail |
| `tools/semak-konfig.mjs` | Semak `wrangler.toml` sebelum deploy |
| `wrangler.uji.toml` | Konfigurasi ujian dan `npm run dev` sahaja |

## Ujian

```bash
npm install
```
```bash
npm test
```
```bash
npm run test:d1
```
```bash
npm run test:e2e
```

`npm test` ialah ujian unit (pantas, tiada rangkaian). `npm run test:d1` menguji SQL sebenar pada D1 tempatan. `npm run test:e2e` memandu pelayar Chromium (perlu Playwright) pada Worker tempatan; `SKRIN_DIR=<folder>` menyimpan tangkapan skrin.
