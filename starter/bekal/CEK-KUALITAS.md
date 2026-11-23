# Cek kualitas (untuk 🧪 Tester dan 🛡️ Ahli Keamanan)

Jalankan bagian yang sesuai di akhir setiap tahap. Laporkan ke Bos dalam
bahasa awam: berapa yang lulus, apa yang diperbaiki, apa yang perlu Bos coba.

## A. Pemeriksaan otomatis (setiap langkah)

1. `npm run lint` lulus.
2. `npx tsc --noEmit` lulus.
3. `npm run build` lulus.

Gagal → perbaiki sendiri → ulangi. Jangan melapor "selesai" saat masih
merah.

## B. Uji di browser (tahap 2 dan 4)

Kalau kamu bisa membuka browser (Antigravity), lakukan sendiri dan ambil
screenshot. Kalau tidak bisa (Codex), uji route dengan `curl` ke
`http://localhost:3000/api/...` dan minta Bos mencoba langkah 1–11 di
browsernya, satu per satu.

Ukuran laptop (1440×900) **dan** HP (390×844):

1. Halaman terbuka tanpa error di console.
2. Keadaan kosong tampil; klik chip contoh mengisi composer.
3. Ganti jenis Video / Gambar / Suara di composer → placeholder, slot foto,
   isi popover, dan ringkasan di pill ikut berubah.
4. Popover pengaturan terbuka/tertutup (klik, Esc, klik di luar); ringkasan
   di pill berubah sesuai pilihan.
5. Harga di tombol Buat berubah saat pengaturan diubah (durasi/resolusi
   video, model gambar, panjang teks narasi).
6. Untuk **setiap jenis** yang aktif: Buat → kartu "Mengantre" → "Sedang
   dibuat" → berhasil (demo ±15 detik untuk video, ±8 detik untuk gambar dan
   suara). Kartu suara bisa diputar.
7. Prompt berisi kata "gagal" (mode demo) → kartu gagal dengan pesan ramah
   dan tombol Coba lagi.
8. Pilih foto (frame awal video / foto acuan gambar) → thumbnail tampil →
   bisa dihapus → hasil dibuat dengan mode foto.
9. Muat ulang halaman → riwayat semua jenis masih ada; job yang belum
   selesai lanjut dicek.
10. Unduh (nama file dan ekstensinya benar per jenis), Pakai lagi, Hapus,
    viewer (Esc menutup), tab filter per jenis, tombol tema.
11. Tidak ada scroll ke samping di HP; composer tidak menutupi kartu
    terakhir.

## C. Kasus nakal (tahap 4)

- Prompt kosong atau hanya spasi → tombol Buat nonaktif, server juga menolak.
- Prompt 2.001 karakter → ditolak dengan pesan jelas.
- Foto PDF / > 4 MB → ditolak dengan pesan jelas (foto besar diperkecil di
  browser dulu).
- Klik Buat berkali-kali cepat → tidak membuat job ganda (tombol nonaktif
  selama mengirim).
- 4 job sekaligus → yang keempat ditahan dengan penjelasan.
- Internet putus saat mengecek status → kartu tidak hilang, pengecekan
  dilanjutkan.

## D. Audit keamanan (wajib sebelum online-kan)

Jalankan dan laporkan setiap butir sebagai ✅/❌:

1. `git ls-files | grep -i env` hanya menampilkan `.env.example`.
2. Cari kebocoran key di kode dan riwayat git:
   `git grep -n -i "yorai_api_key\|sk-\|bearer" -- . ':!*.md'` → hanya di
   `src/lib/config.ts` / `src/lib/yorai.ts` sebagai **nama variabel**, tanpa
   nilai. Pastikan juga file rahasia tidak pernah di-commit:
   `git log --all --oneline -- .env .env.local` kosong. Jangan pernah
   mencari key dengan menuliskan nilai aslinya di perintah.
3. Hasil `npm run build`: cari key di bundel browser —
   `grep -r "YORAI_API_KEY" .next/static` kosong.
4. Tidak ada variabel `NEXT_PUBLIC_` berisi rahasia.
5. Tanpa cookie sesi (kode akses aktif), `curl -X POST localhost:3000/api/generate`
   → 401. Sama untuk `/api/quote`, `/api/upload`, `/api/jobs/<id>`,
   `/api/file/<id>`.
6. Kode akses salah → 401 dengan jeda; benar → cookie `HttpOnly` dan
   `SameSite=Lax` (cek header `Set-Cookie`).
7. Kirim `modelId` asing, `duration: 15`, `resolution: "1080p"`, `voice`
   asing, atau `modelId` gambar dengan `kind: "video"` lewat curl → ditolak
   400 (server tidak percaya pilihan dari browser).
8. Biaya di atas `MAX_CREDITS_PER_JOB` → ditolak sebelum job dibuat.
9. `id` bukan UUID di `/api/jobs/` dan `/api/file/` → 400.
10. Header keamanan ada di respons halaman utama
    (`curl -I localhost:3000`).
11. Pesan error ke browser tidak berisi stack trace, URL internal, atau
    isi header.
12. `npm audit --omit=dev` tidak ada temuan *high/critical* (bila ada,
    laporkan ke Bos, jangan menambah library tanpa izin).

## E. Setelah online (tahap 5)

1. Buka URL `*.vercel.app` → layar kode akses tampil.
2. Kode akses benar → studio tampil, lencana **Mode demo tidak muncul**.
3. Satu hasil uji termurah (dengan izin Bos, misalnya satu gambar) berhasil
   dan bisa dibuka serta diunduh di HP. Bila studio punya video atau suara,
   pastikan juga bisa diputar di HP.
4. `curl -X POST https://<url>/api/generate` tanpa cookie → 401.
