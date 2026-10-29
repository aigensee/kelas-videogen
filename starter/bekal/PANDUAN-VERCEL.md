# Panduan online-kan (untuk 🚀 DevOps)

Tujuan: situs `https://<nama>.vercel.app` yang memakai AI sungguhan, dengan
API key tersimpan aman di Vercel. Bos tidak mengetik perintah; kamu yang
menjalankan, Bos hanya login dan mengisi nilai rahasia di browser.

**Syarat sebelum mulai:** audit keamanan di `bekal/CEK-KUALITAS.md` bagian D
lulus semua, semua perubahan sudah di-commit.

Pilih **Jalur A** (disarankan, update otomatis setiap push). Kalau login
GitHub macet lebih dari 10 menit, pindah ke **Jalur B**.

## Jalur A — GitHub + Vercel

1. Cek `git status` bersih dan branch bernama `main`.
2. Cek alat GitHub: `gh --version`.
   - Ada → `gh auth status`; bila belum login, jalankan `gh auth login --web`
     dan pandu Bos: salin kode 8 huruf yang muncul, buka link, tempel, klik
     **Authorize**.
     Lalu `gh repo create <nama-proyek> --private --source . --push`.
   - Tidak ada → pandu Bos membuat repository kosong di
     https://github.com/new (nama proyek, **Private**, jangan centang
     README), minta Bos menempelkan link repo, lalu
     `git remote add origin <link>` dan `git push -u origin main`. Bila
     diminta login, jendela browser akan muncul; pandu Bos.
3. Pandu Bos di browser, satu langkah per pesan:
   1. Buka https://vercel.com/new → **Continue with GitHub**.
   2. Di **Import Git Repository**, bila repo belum tampil: klik
      **Adjust GitHub App Permissions** → pilih repo itu → **Save**.
   3. Klik **Import**. Framework otomatis **Next.js**; jangan ubah pengaturan
      build.
   4. Buka **Environment Variables**, tambahkan:

      | Key | Value |
      |---|---|
      | `YORAI_API_KEY` | key Yorai Bos (salin dari `.env.local` atau buat key baru di yorai.id/app/api) |
      | `APP_PASSCODE` | kode akses studio |
      | `DEMO_MODE` | `false` |

   5. Klik **Deploy**, tunggu sampai muncul selamat 🎉, lalu **Continue to
      Dashboard** → klik **Visit**.
4. Minta Bos menempelkan URL situsnya, lalu jalankan cek bagian E di
   `bekal/CEK-KUALITAS.md` (curl dari terminal, Bos mencoba dari HP).
5. Tulis URL di bagian **Kemajuan** `AGENTS.md`, commit, dan push. Jelaskan:
   "Mulai sekarang, setiap kali aku menyimpan dan mengirim perubahan, situsmu
   otomatis diperbarui dalam ±1 menit."

## Jalur B — Vercel CLI (tanpa GitHub)

1. `npx vercel@latest login` → pandu Bos login lewat browser yang terbuka.
2. `npx vercel@latest link --yes` (buat proyek baru dengan nama proyek).
3. Variabel rahasia: **jangan** mengetik atau mencetak key. Pandu Bos
   mengisinya di dashboard: Vercel → proyek → **Settings → Environment
   Variables** (tiga variabel seperti Jalur A, centang **Production**).
4. `npx vercel@latest --prod --yes` → berikan URL produksi ke Bos.
5. Lanjut langkah 4–5 Jalur A. Untuk update berikutnya, jalankan ulang
   langkah 4.

## Masalah umum

| Gejala | Penyebab | Tindakan |
|---|---|---|
| Build merah di Vercel | error build | minta Bos menyalin log (Deployments → klik yang merah → **Build Logs**), perbaiki, `npm run build` lokal, commit, push |
| Situs menampilkan "Mode demo" | `DEMO_MODE` masih `true` / key kosong | perbaiki di Environment Variables → **Deployments → ⋯ → Redeploy** |
| "Kode akses belum diatur" | `APP_PASSCODE` kosong | isi lalu Redeploy |
| "Kunci API salah" | key salah ketik/dicabut | buat key baru di yorai.id/app/api, ganti, Redeploy |
| "Saldo tidak cukup" | saldo Yorai habis | Bos mengisi saldo di yorai.id |
| Video tidak bisa diputar | proxy file tidak meneruskan `Range` atau mem-buffer | perbaiki `/api/file/[id]` (blueprint bagian 6) |
| Push ditolak (`rejected`) | ada commit di GitHub yang tidak ada di lokal | `git pull --rebase` lalu push; **jangan** `--force` |

Vercel paket **Hobby** gratis untuk proyek pribadi/non-komersial. Untuk
dipakai jualan, Bos perlu paket Pro (sampaikan sekali, tanpa menakut-nakuti).
