# Blueprint teknis (untuk 🛠️ Software Engineer dan 🛡️ Ahli Keamanan)

Dokumen ini untuk agen AI, bukan untuk Bos. Ikuti supaya studio selesai dalam
waktu kelas dan aman saat online. Kalau cerita Bos meminta hal yang berbeda,
cerita Bos yang menang, selama aturan keamanan di `AGENTS.md` tetap terpenuhi.

## 1. Teknologi

- **Next.js versi terbaru** (App Router, TypeScript, Tailwind CSS, ESLint,
  folder `src/`), npm. **Tanpa library tambahan**: pakai `fetch`, `crypto`
  bawaan Node, dan komponen buatan sendiri. Ikon cukup SVG inline.
- Next.js terbaru punya perubahan yang mungkin tidak ada di data latihmu.
  Setelah instalasi, baca panduan yang relevan di
  `node_modules/next/dist/docs/` sebelum menulis route atau konfigurasi.

### Jangan menimpa `next.config.ts`

Di Next.js terbaru, `next.config.ts` bawaan berisi pengaturan penting
(misalnya `cacheComponents` dan sambungan Tailwind lewat `turbopack.rules`).
**Tambahkan** pengaturan baru (misalnya `headers()`) ke objek yang ada; jangan
menulis ulang filenya. Bila tampilan tiba-tiba tanpa gaya atau build
memperingatkan `Unknown at rule: @theme`, berarti sambungan Tailwind hilang.

### Membuat proyek di folder yang sudah berisi file

Folder ini sudah berisi `AGENTS.md`, `.agents/`, dan `bekal/`, sehingga
`create-next-app` akan menolak folder ini secara langsung. Caranya:

1. Buat di subfolder sementara, misalnya
   `npx create-next-app@latest tmp-app --ts --tailwind --eslint --app --src-dir --use-npm --import-alias "@/*" --yes`.
2. Hapus `tmp-app/.git` (riwayat git bawaan create-next-app), lalu pindahkan
   semua isi `tmp-app/` (termasuk file berawalan titik) ke folder utama,
   **kecuali** `AGENTS.md`, `CLAUDE.md`, dan `README.md` milik `tmp-app`. Kalau `AGENTS.md` dari `tmp-app` berisi blok aturan Next.js,
   salin blok itu ke **bagian paling atas** `AGENTS.md` kita, jangan menimpa
   isi lainnya.
3. Hapus `tmp-app/`, jalankan `npm install` bila perlu, lalu
   `npm run dev` dan pastikan `http://localhost:3000` terbuka.
4. Pastikan `.gitignore` memuat `.env*` **dan** baris `!.env.example`
   (bawaan create-next-app ikut mengabaikan `.env.example`), `node_modules`,
   `.next`, `.vercel`. Lalu `git init` (bila belum) dan commit
   pertama: `Mulai proyek studio video`.

## 2. Arsitektur

```
Browser (tampilan studio)
   │  fetch ke /api/* milik kita (cookie sesi, tanpa API key)
   ▼
Route server Next.js  (src/app/api/*)  ← satu-satunya yang tahu YORAI_API_KEY
   │  Authorization: Bearer YORAI_API_KEY
   ▼
API Yorai  https://yorai.id/api/v1
```

**Browser tidak pernah memanggil yorai.id secara langsung.**

### Struktur file yang disarankan

| Lokasi | Isi |
|---|---|
| `src/lib/config.ts` | membaca environment variable (hanya server) |
| `src/lib/models.ts` | daftar model terkurasi + aturan input (dipakai UI dan server) |
| `src/lib/yorai.ts` | semua panggilan ke Yorai + pesan error ramah |
| `src/lib/session.ts` | kode akses → cookie sesi, pengecekan sesi |
| `src/lib/validate.ts` | validasi body permintaan |
| `src/lib/demo.ts` | mode demo (job palsu, gratis) |
| `src/app/api/*/route.ts` | route server, satu tanggung jawab per file |
| `src/components/*` | komponen tampilan (rail, topbar, grid, kartu, composer, popover, viewer) |
| `src/components/useStudio.ts` | state studio: composer, job berjalan, riwayat |
| `.env.example` | daftar variabel **tanpa nilai rahasia** |

## 3. Environment variable

| Nama | Wajib | Isi |
|---|---|---|
| `YORAI_API_KEY` | untuk AI sungguhan | key dari https://yorai.id/app/api |
| `APP_PASSCODE` | ya, saat online | kode akses studio, pilihan Bos |
| `DEMO_MODE` | tidak | `true` = hasil contoh gratis. Bila `YORAI_API_KEY` kosong, aplikasi otomatis demo |
| `MAX_CREDITS_PER_JOB` | tidak | batas biaya satu video, default `10` (Rp10.000) |

Buat `.env.local` berisi nama variabel dengan nilai kosong/demo, lalu pandu
Bos mengisi `YORAI_API_KEY` dan `APP_PASSCODE` sendiri. Buat `.env.example`
dengan nama yang sama tanpa nilai.

Cara membukakan file itu untuk Bos:

- **Antigravity:** minta Bos klik `.env.local` di daftar file sebelah kiri.
- **Codex / tanpa editor:** jalankan `open -e .env.local` (Mac, membuka
  TextEdit) atau `notepad .env.local` (Windows).

Lalu pandu: tempel key tepat setelah `YORAI_API_KEY=` tanpa spasi atau tanda
kutip, isi `APP_PASSCODE=`, ubah `DEMO_MODE=false`, simpan (Cmd/Ctrl+S),
tutup, dan ketik "sudah". Setelah itu restart `npm run dev` dan cek dengan
`/api/status` (jangan membaca isi file).

## 4. API Yorai (ringkas; sumber resmi: https://yorai.id/llms.txt)

Sebelum menulis `src/lib/yorai.ts`, baca
https://yorai.id/docs/api/llms-full.txt bagian awal (sampai "Live model
reference") dan halaman model yang dipakai:
`https://yorai.id/docs/api/models/<slug>.md`.

- Base URL `https://yorai.id/api/v1`, header
  `Authorization: Bearer <YORAI_API_KEY>`.
- `POST /quote` body `{model, type, input}` → `{credits, ...}`. Tidak memotong
  saldo. Pakai untuk harga di tombol **Buat** dan untuk batas biaya.
- `POST /jobs` body sama, header `Idempotency-Key: <uuid>` → `202` job
  `{id, status, credits, outputs, error}`. Status: `queued`, `running`,
  `succeeded`, `failed`.
- `GET /jobs/{id}` untuk mengecek status. Hasil di `outputs[0]`:
  `{id, mime, url, expires_at}`. `url` tetap butuh API key, jadi tampilkan
  lewat proxy kita (bagian 6).
- `POST /files` (multipart, field `file`) → `{id}` untuk foto frame awal.
- `GET /files/{id}` mengalirkan file (dukung header `Range`). File hasil
  hilang setelah **14 hari**; beri tahu pengguna untuk mengunduh.
- `GET /balance` → `{available, held}` dalam kredit.
- Batas per key: 20 job baru/menit, 10 job aktif. 1 kredit = Rp1.000.
- Error berbentuk `{error:{code,type,message}}`.

### Model terkurasi (isi `src/lib/models.ts`)

Mulai dengan dua model ini. Nama input **harus persis** seperti di bawah
(sudah dicek dari katalog, 29 Oktober 2026). Kalau Bos ingin model lain, baca
halaman `.md`-nya dulu lalu tambahkan entri.

**`bytedance-seedance-2-mini`** (label: "Seedance 2 Mini", "Video + suara, hemat")

| type | input |
|---|---|
| `text-to-video` | `prompt`, `aspect_ratio` (`16:9`,`9:16`,`1:1`,`4:3`,`3:4`,`21:9`), `duration` (1–15), `resolution` (`480p`,`720p`), `generate_audio` (boolean) |
| `image-to-video` | sama + `first_frame_url` = **ID file** dari `/files` (bukan URL); `aspect_ratio` boleh `adaptive` |

Harga API: 480p 0,402 kredit/detik, 720p 0,866 kredit/detik.

**`pixverse-v6`** (label: "PixVerse V6", "Cepat, kualitas bisa dipilih")

| type | input |
|---|---|
| `text-to-video` | `prompt`, `aspect_ratio` (wajib), `duration` (wajib, 1–15), `quality` (`360p`,`540p`,`720p`,`1080p`; wajib), `generate_audio_switch` (boolean) |
| `image-to-video` | `prompt`, `duration`, `quality`, `image_urls` = **array ID file** (maks 2), `generate_audio_switch`. **Tidak ada** `aspect_ratio` (ikut foto) |

Harga API tanpa suara: 360p 0,423, 540p 0,592, 720p 0,76 kredit/detik;
dengan suara lebih mahal.

**Penting:** default `duration` di katalog adalah **15 detik**. Selalu kirim
`duration` yang dipilih pengguna (default 5). Jangan mengirim field yang
tidak ada di tabel; Yorai menolak field asing dengan error 400.

Perkiraan untuk Bos (cek ulang dengan `/quote`, harga bisa berubah):

| Video | Perkiraan |
|---|---|
| Seedance 2 Mini, 5 detik, 480p | ±Rp2.000 |
| Seedance 2 Mini, 5 detik, 720p | ±Rp4.300 |
| Seedance 2 Mini, 10 detik, 480p | ±Rp4.000 |
| PixVerse V6, 5 detik, 360p, tanpa suara | ±Rp2.100 |

## 5. Route server

Semua route kecuali `login` dan `status` **wajib mengecek sesi** (bagian 7).
Route berjalan di Node.js secara bawaan. **Jangan** menambah
`export const runtime = …`: Next.js terbaru dengan `cacheComponents` menolak
build bila ada baris itu. Di route dinamis, `params` berupa Promise
(`const { id } = await ctx.params`).

| Route | Gunanya |
|---|---|
| `POST /api/login` | cek kode akses → pasang cookie sesi |
| `POST /api/logout` | hapus cookie |
| `GET /api/status` | `{demo, needsPasscode, loggedIn}` (tanpa rahasia apa pun) |
| `POST /api/quote` | validasi pilihan → `/quote` → `{credits, rupiah}` |
| `POST /api/generate` | validasi → `/quote` → tolak bila > `MAX_CREDITS_PER_JOB` → `/jobs` → job |
| `GET /api/jobs/[id]` | status job (validasi `id` format UUID) |
| `POST /api/upload` | foto JPG/PNG/WebP ≤ 4 MB → `/files` → `{fileId}` |
| `GET /api/file/[id]` | proxy hasil (bagian 6) |
| `GET /api/balance` | sisa saldo (opsional, untuk topbar) |

Browser mengirim **pilihan** (`modelId`, `mode` teks/foto, `prompt`, `ratio`,
`duration`, `resolution`, `audio`, `fileId`), bukan body Yorai mentah. Server
menyusun body Yorai dari `models.ts`, sehingga pengguna tidak bisa mengirim
model atau durasi di luar daftar.

### Alur membuat video

1. Pengguna mengubah pengaturan → UI memanggil `/api/quote` (debounce
   ±400 ms) → tombol **Buat** menampilkan harga, misalnya "Buat · Rp2.010".
2. Klik **Buat** → `/api/generate` → kartu baru muncul paling depan dengan
   status "Mengantre".
3. Browser mengecek `/api/jobs/[id]` **setiap 5 detik** sampai `succeeded`
   atau `failed`. Berhenti setelah 15 menit dengan pesan "Masih diproses, cek
   lagi nanti" dan tombol "Cek lagi".
4. Maksimal 3 job berjalan bersamaan per browser; tombol Buat menjelaskan
   alasannya kalau penuh.
5. Riwayat (id job, prompt, pengaturan, waktu, status, fileId) disimpan di
   `localStorage`. Saat halaman dibuka lagi, job yang belum selesai dicek
   ulang.

Route harus selesai cepat (jangan menunggu video jadi di dalam route), karena
fungsi Vercel punya batas waktu.

## 6. Proxy file hasil

- `GET /api/file/[id]`: validasi UUID, teruskan header `Range` ke Yorai,
  kembalikan `upstream.body` **sebagai stream** (jangan `arrayBuffer()`),
  salin `Content-Type`, `Content-Length`, `Content-Range`, `Accept-Ranges`,
  dan status (200/206). Tambah `X-Content-Type-Options: nosniff`.
- `?download=1` → `Content-Disposition: attachment; filename="video-<8 huruf id>.mp4"`.
- Error 410 → pesan "File sudah kedaluwarsa (lebih dari 14 hari)".

## 7. Keamanan (🛡️ wajib)

- **Sesi dengan cookie**: `POST /api/login` membandingkan kode akses dengan
  `APP_PASSCODE` memakai `crypto.timingSafeEqual` (samakan panjang dulu
  dengan hash SHA-256 kedua sisi). Bila cocok, pasang cookie `studio_session`
  bernilai HMAC-SHA256(`APP_PASSCODE`, `"studio-session-v1"`), `httpOnly`,
  `sameSite: "lax"`, `secure` di production, `path: "/"`, umur 30 hari.
  Kode akses salah → jeda ±1 detik lalu 401 "Kode akses salah".
  Mengganti `APP_PASSCODE` otomatis mengeluarkan semua sesi lama.
- Karena memakai cookie, `<video src="/api/file/...">` tetap terlindungi
  tanpa header tambahan.
- Bila `APP_PASSCODE` kosong: di lokal boleh tanpa kode akses; **di production
  route berbiaya menolak** dengan pesan "Kode akses belum diatur".
- `YORAI_API_KEY` hanya dibaca di `src/lib/config.ts` dan dipakai di
  `src/lib/yorai.ts`. Tidak ada variabel berawalan `NEXT_PUBLIC_` untuk
  rahasia. Jangan pernah mencetaknya di log atau mengirimnya ke browser.
- Validasi server: `prompt` 1–2000 karakter (di-trim), `modelId`, `mode`,
  `ratio`, `duration`, `resolution` harus ada di `models.ts`, `fileId` dan
  `id` berformat UUID, upload hanya `image/jpeg|png|webp` ≤ 4 MB.
- Batas biaya: tolak job bila `/quote` > `MAX_CREDITS_PER_JOB`, dengan pesan
  yang menjelaskan cara menurunkan durasi/resolusi.
- Header keamanan di `next.config.ts` untuk semua halaman:
  `X-Content-Type-Options: nosniff`, `Referrer-Policy: strict-origin-when-cross-origin`,
  `X-Frame-Options: DENY`, `Permissions-Policy: camera=(), microphone=(), geolocation=()`.
- Jangan memakai `dangerouslySetInnerHTML` untuk teks pengguna. Pesan error
  dari Yorai diterjemahkan, bukan ditampilkan mentah beserta detail teknis.

## 8. Pesan error ramah

| Dari Yorai | Tampilkan ke pengguna |
|---|---|
| 401 `invalid_api_key` | "Kunci API Yorai salah atau sudah dicabut. Hubungi pemilik studio." |
| 402 `insufficient_credits` | "Saldo Yorai tidak cukup. Isi ulang di yorai.id." |
| 400 `invalid_request` | "Pengaturan ini tidak didukung model tersebut. Coba ubah rasio/durasi." |
| 409 `model_unavailable` / `no_price` | "Model ini sedang tidak tersedia. Pilih model lain." |
| 410 `file_expired` | "File sudah kedaluwarsa (lebih dari 14 hari)." |
| 429 | "Terlalu banyak permintaan. Tunggu sebentar lalu coba lagi." |
| 5xx / jaringan | "Server AI sedang sibuk. Coba lagi beberapa saat." |
| job `failed` | "Video gagal dibuat. Saldo dikembalikan otomatis. Coba ubah prompt." |

## 9. Mode demo

Aktif bila `DEMO_MODE=true` atau `YORAI_API_KEY` kosong. Semua route tetap
berjalan dengan alur yang sama, tanpa memanggil Yorai:

- `/api/quote` menghitung dari harga per detik di `models.ts`.
- `/api/generate` membuat id `demo-<uuid>`; `/api/jobs/[id]` mengubah status
  berdasarkan waktu (mengantre 3 detik → diproses ±12 detik → berhasil).
  Sesekali (misalnya prompt mengandung kata "gagal") hasilnya `failed`, untuk
  menguji tampilan gagal.
- `/api/file/[id]` untuk id demo mengembalikan SVG beranimasi sesuai rasio
  dengan tulisan "Contoh video · mode demo". Kartu demo memakai `<img>`,
  kartu asli memakai `<video>`.
- Topbar menampilkan lencana **Mode demo** agar Bos tidak bingung.

## 10. Bonus tahap 6 (hanya bila waktu tersisa)

- **Bantu tulis prompt**: `POST /api/prompt` → `POST /chat/completions` model
  `deepseek-v4-1-flash`, `max_tokens` 600, batas waktu 40 detik; mengubah ide
  singkat berbahasa Indonesia menjadi prompt video (subjek, aksi, kamera,
  suasana). Biaya < Rp10 per klik.
- Tombol **Pakai lagi** (isi composer dari kartu), **Hapus dari riwayat**,
  filter Semua/Berhasil/Gagal.
- Model tambahan dari https://yorai.id/docs/api/models (baca `.md`-nya).

## 11. Cek sebelum melapor

```
npm run lint
npx tsc --noEmit
npm run build
```

Ketiganya wajib lulus. Lalu ikuti `bekal/CEK-KUALITAS.md`.
