# Blueprint teknis (untuk 🛠️ Software Engineer dan 🛡️ Ahli Keamanan)

Dokumen ini untuk agen AI, bukan untuk Bos. Studio di sini membuat **video, gambar,
dan suara** lewat satu antarmuka. Ikuti supaya studio selesai dalam
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
   pertama: `Mulai proyek studio AI`.

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
| `src/lib/models.ts` | daftar model terkurasi per jenis (video/gambar/suara) + aturan input (dipakai UI dan server) |
| `src/lib/yorai.ts` | semua panggilan ke Yorai + pesan error ramah |
| `src/lib/session.ts` | kode akses → cookie sesi, pengecekan sesi |
| `src/lib/validate.ts` | validasi body permintaan |
| `src/lib/demo.ts` | mode demo (job palsu, gratis) |
| `src/app/api/*/route.ts` | route server, satu tanggung jawab per file |
| `src/components/*` | komponen tampilan (rail, topbar, grid, kartu per jenis, composer, popover, viewer) |
| `src/components/useStudio.ts` | state studio: composer, job berjalan, riwayat |
| `.env.example` | daftar variabel **tanpa nilai rahasia** |

## 3. Environment variable

| Nama | Wajib | Isi |
|---|---|---|
| `YORAI_API_KEY` | untuk AI sungguhan | key dari https://yorai.id/app/api |
| `APP_PASSCODE` | ya, saat online | kode akses studio, pilihan Bos |
| `DEMO_MODE` | tidak | `true` = hasil contoh gratis. Bila `YORAI_API_KEY` kosong, aplikasi otomatis demo |
| `MAX_CREDITS_PER_JOB` | tidak | batas biaya satu hasil (video/gambar/suara), default `10` (Rp10.000) |

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
- `POST /files` (multipart, field `file`) → `{id}` untuk foto frame awal
  video dan foto acuan gambar.
- `GET /files/{id}` mengalirkan file (dukung header `Range`; penting untuk
  video dan suara). File hasil
  hilang setelah **14 hari**; beri tahu pengguna untuk mengunduh.
- `GET /balance` → `{available, held}` dalam kredit.
- Batas per key: 20 job baru/menit, 10 job aktif. 1 kredit = Rp1.000.
- Error berbentuk `{error:{code,type,message}}`.
- Satu job bisa menghasilkan **lebih dari satu output** (misalnya musik
  Suno biasanya dua lagu). Simpan semua `outputs`, tampilkan yang pertama di
  kartu, sisanya di viewer.

### Model terkurasi (isi `src/lib/models.ts`)

Setiap entri punya `kind` (`video` | `image` | `audio`), sehingga UI bisa
menampilkan model sesuai tab jenis di composer. Mulai dengan model di bawah,
**hanya untuk jenis yang dipilih Bos**. Nama input **harus persis** seperti
di tabel (sudah dicek dari katalog, 23 November 2026). Kalau Bos ingin model
lain, baca halaman `.md`-nya dulu lalu tambahkan entri.

**Penting untuk semua model:** jangan mengirim field yang tidak ada di tabel;
Yorai menolak field asing dengan error 400. Field opsional yang tidak dipakai
cukup tidak dikirim.

#### Video

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

Default `duration` di katalog adalah **15 detik**. Selalu kirim `duration`
yang dipilih pengguna (default 5).

#### Gambar

**`nano-banana-2-lite`** (label: "Nano Banana 2 Lite", "Serba bisa, hemat")

| type | input |
|---|---|
| `text-to-image` | `prompt` (wajib), `aspect_ratio` (wajib: `1:1`,`16:9`,`9:16`,`4:3`,`3:4`,`4:5`,`5:4`,`2:3`,`3:2`,`21:9`, …, `auto`) |
| `image-to-image` | sama + `image_urls` = **array ID file** (maks 10) |

Harga API: 0,423 kredit per gambar.

**`seedream-5-flash`** (label: "Seedream 5.0 Flash", "Paling murah, cepat")

| type | input |
|---|---|
| `text-to-image` | `prompt` (wajib, maks 5000 karakter), `aspect_ratio` (wajib: `1:1`,`4:3`,`3:4`,`16:9`,`9:16`,`2:3`,`3:2`,`21:9`), `size` (`1K`,`2K`), `output_format` (`jpeg`,`png`) |
| `image-to-image` | sama + `image_urls` = **array ID file** (wajib, maks 10) |

Harga API: 0,342 kredit per gambar (1K dan 2K sama). Kirim `size: "2K"`.

#### Suara

**`google-gemini-3-8-flash-tts`** (label: "Narasi", "Teks dibacakan, untuk voice over")

| type | input |
|---|---|
| `text-to-speech` | `speakers` (wajib) = `[{"speaker_id":"Speaker 1","voice_name":"<nama suara>"}]`, `dialogue_turns` (wajib) = `[{"speaker_id":"Speaker 1","style":"<gaya bicara>","text":"<teks pengguna>"}]`, `temperature` (0–2, opsional) |

- Pakai **satu pembicara** saja. Dengan dua pembicara Yorai otomatis
  menyalakan `filler_words`.
- Nama suara yang terlihat di katalog: `Fola` dan `Alnilam`. Sebelum
  menambah pilihan suara lain, baca halaman `.md` modelnya untuk daftar nama
  yang valid. UI menampilkan nama suara apa adanya.
- `style` diisi server dari pilihan gaya di UI, dalam bahasa Inggris
  singkat: Hangat → `"warm, friendly, natural pace"`, Semangat →
  `"energetic, upbeat"`, Tenang → `"calm, measured, soft"`.
- Harga dihitung dari panjang suara: kira-kira **Rp200 per menit suara**.
  Selalu pakai `/quote` untuk angka pasti.

**`ai-music-api-generate`** (label: "Musik", "Lagu atau musik latar dari deskripsi")

| type | input |
|---|---|
| `text-to-music` | `custom_mode` (wajib, kirim `false`), `instrumental` (wajib, boolean), `model` (wajib, kirim `"V6"`), `prompt` (deskripsi musik dari pengguna) |

Harga API: 1,267 kredit per job (biasanya menghasilkan dua lagu). Mode
`custom_mode: true` (lirik, judul, gaya sendiri) adalah fitur tahap 6.

#### Perkiraan untuk Bos

Cek ulang dengan `/quote`; harga bisa berubah.

| Hasil | Perkiraan |
|---|---|
| Gambar Seedream 5.0 Flash | ±Rp350 |
| Gambar Nano Banana 2 Lite | ±Rp420 |
| Narasi 30 detik | ±Rp100 |
| Musik (satu job) | ±Rp1.300 |
| Video Seedance 2 Mini, 5 detik, 480p | ±Rp2.000 |
| Video Seedance 2 Mini, 5 detik, 720p | ±Rp4.300 |
| Video Seedance 2 Mini, 10 detik, 480p | ±Rp4.000 |
| Video PixVerse V6, 5 detik, 360p, tanpa suara | ±Rp2.100 |

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
| `POST /api/upload` | foto JPG/PNG/WebP ≤ 4 MB (frame awal video / foto acuan gambar) → `/files` → `{fileId}` |
| `GET /api/file/[id]` | proxy hasil (bagian 6) |
| `GET /api/balance` | sisa saldo (opsional, untuk topbar) |

Browser mengirim **pilihan** (`kind` video/gambar/suara, `modelId`, `mode`
teks/foto, `prompt`, lalu sesuai jenis: `ratio`, `duration`, `resolution`,
`audio`, `fileId`, `voice`, `style`, `instrumental`), bukan body Yorai
mentah. Server
menyusun body Yorai dari `models.ts`, sehingga pengguna tidak bisa mengirim
model, durasi, atau suara di luar daftar, dan `modelId` harus cocok dengan
`kind`.

### Alur membuat hasil (sama untuk semua jenis)

1. Pengguna mengubah pengaturan → UI memanggil `/api/quote` (debounce
   ±400 ms) → tombol **Buat** menampilkan harga, misalnya "Buat · Rp2.010".
   Untuk narasi, harga tergantung panjang teks, jadi quote juga dipanggil
   ulang saat teks berubah (debounce sama).
2. Klik **Buat** → `/api/generate` → kartu baru muncul paling depan dengan
   status "Mengantre".
3. Browser mengecek `/api/jobs/[id]` (gambar dan suara **setiap 3 detik**,
   video **setiap 5 detik**) sampai `succeeded`
   atau `failed`. Berhenti setelah 15 menit dengan pesan "Masih diproses, cek
   lagi nanti" dan tombol "Cek lagi".
4. Maksimal 3 job berjalan bersamaan per browser; tombol Buat menjelaskan
   alasannya kalau penuh.
5. Riwayat (id job, jenis, prompt, pengaturan, waktu, status, id semua
   output beserta `mime`-nya) disimpan di
   `localStorage`. Saat halaman dibuka lagi, job yang belum selesai dicek
   ulang.

Route harus selesai cepat (jangan menunggu hasil jadi di dalam route), karena
fungsi Vercel punya batas waktu.

## 6. Proxy file hasil

- `GET /api/file/[id]`: validasi UUID, teruskan header `Range` ke Yorai,
  kembalikan `upstream.body` **sebagai stream** (jangan `arrayBuffer()`),
  salin `Content-Type`, `Content-Length`, `Content-Range`, `Accept-Ranges`,
  dan status (200/206). Tambah `X-Content-Type-Options: nosniff`.
- `?download=1` → `Content-Disposition: attachment; filename="<jenis>-<8 huruf id>.<ext>"`,
  dengan ekstensi dari `Content-Type` (`video/mp4` → `mp4`, `image/png` →
  `png`, `image/jpeg` → `jpg`, `image/webp` → `webp`, `audio/mpeg` → `mp3`,
  `audio/wav` → `wav`; selain itu `bin`).
- Error 410 → pesan "File sudah kedaluwarsa (lebih dari 14 hari)".

## 7. Keamanan (🛡️ wajib)

- **Sesi dengan cookie**: `POST /api/login` membandingkan kode akses dengan
  `APP_PASSCODE` memakai `crypto.timingSafeEqual` (samakan panjang dulu
  dengan hash SHA-256 kedua sisi). Bila cocok, pasang cookie `studio_session`
  bernilai HMAC-SHA256(`APP_PASSCODE`, `"studio-session-v1"`), `httpOnly`,
  `sameSite: "lax"`, `secure` di production, `path: "/"`, umur 30 hari.
  Kode akses salah → jeda ±1 detik lalu 401 "Kode akses salah".
  Mengganti `APP_PASSCODE` otomatis mengeluarkan semua sesi lama.
- Karena memakai cookie, `<video>`, `<img>`, dan `<audio>` dengan
  `src="/api/file/..."` tetap terlindungi
  tanpa header tambahan.
- Bila `APP_PASSCODE` kosong: di lokal boleh tanpa kode akses; **di production
  route berbiaya menolak** dengan pesan "Kode akses belum diatur".
- `YORAI_API_KEY` hanya dibaca di `src/lib/config.ts` dan dipakai di
  `src/lib/yorai.ts`. Tidak ada variabel berawalan `NEXT_PUBLIC_` untuk
  rahasia. Jangan pernah mencetaknya di log atau mengirimnya ke browser.
- Validasi server: `prompt` 1–2000 karakter (di-trim), `kind`, `modelId`,
  `mode`, `ratio`, `duration`, `resolution`, `voice`, `style` harus ada di
  `models.ts` untuk jenis itu, `instrumental` dan `audio` boolean, `fileId` dan
  `id` berformat UUID, upload hanya `image/jpeg|png|webp` ≤ 4 MB.
- Batas biaya: tolak job bila `/quote` > `MAX_CREDITS_PER_JOB`, dengan pesan
  yang menjelaskan cara menurunkan biayanya (durasi/resolusi untuk video,
  teks lebih pendek untuk narasi).
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
| 400 `invalid_request` | "Pengaturan ini tidak didukung model tersebut. Coba ubah pengaturannya." |
| 409 `model_unavailable` / `no_price` | "Model ini sedang tidak tersedia. Pilih model lain." |
| 410 `file_expired` | "File sudah kedaluwarsa (lebih dari 14 hari)." |
| 429 | "Terlalu banyak permintaan. Tunggu sebentar lalu coba lagi." |
| 5xx / jaringan | "Server AI sedang sibuk. Coba lagi beberapa saat." |
| job `failed` | "Gagal dibuat. Saldo dikembalikan otomatis. Coba ubah prompt." (sebut jenisnya: "Video/Gambar/Suara gagal dibuat…") |

## 9. Mode demo

Aktif bila `DEMO_MODE=true` atau `YORAI_API_KEY` kosong. Semua route tetap
berjalan dengan alur yang sama, tanpa memanggil Yorai:

- `/api/quote` menghitung dari harga di `models.ts` (per detik untuk video,
  per gambar, per job untuk musik, perkiraan dari panjang teks untuk narasi:
  ±15 karakter per detik suara).
- `/api/generate` membuat id `demo-<uuid>`; `/api/jobs/[id]` mengubah status
  berdasarkan waktu (mengantre 3 detik → diproses ±12 detik untuk video,
  ±5 detik untuk gambar dan suara → berhasil).
  Sesekali (misalnya prompt mengandung kata "gagal") hasilnya `failed`, untuk
  menguji tampilan gagal.
- `/api/file/[id]` untuk id demo mengembalikan, sesuai jenis:
  - video: SVG beranimasi sesuai rasio bertuliskan "Contoh video · mode
    demo" (kartu video demo memakai `<img>`, kartu asli memakai `<video>`),
  - gambar: SVG diam sesuai rasio bertuliskan "Contoh gambar · mode demo",
  - suara: file WAV 3 detik berisi nada lembut yang dibuat dengan `Buffer`
    bawaan Node (tanpa library), supaya tombol putar bisa dicoba.
- Topbar menampilkan lencana **Mode demo** agar Bos tidak bingung.

## 10. Bonus tahap 6 (hanya bila waktu tersisa)

- **Bantu tulis prompt**: `POST /api/prompt` → `POST /chat/completions` model
  `deepseek-v4-1-flash`, `max_tokens` 600, batas waktu 40 detik; mengubah ide
  singkat berbahasa Indonesia menjadi prompt sesuai jenis (video: subjek,
  aksi, kamera, suasana; gambar: subjek, gaya, cahaya, komposisi; musik:
  suasana, genre, alat musik). Biaya < Rp10 per klik.
- Tombol **Pakai lagi** (isi composer dari kartu, termasuk jenisnya),
  **Hapus dari riwayat**, filter per jenis dan status.
- **Gambar → video**: tombol "Jadikan video" di kartu gambar yang memakai
  gambar itu sebagai frame awal video.
- **Musik dengan lirik**: `custom_mode: true` + `lyrics`, `title`, `style`.
- Model tambahan dari https://yorai.id/docs/api/models (baca `.md`-nya).

## 11. Cek sebelum melapor

```
npm run lint
npx tsc --noEmit
npm run build
```

Ketiganya wajib lulus. Lalu ikuti `bekal/CEK-KUALITAS.md`.
