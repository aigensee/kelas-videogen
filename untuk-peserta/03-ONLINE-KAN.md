# Online-kan ke Vercel (±20 menit)

Setelah tahap ini, studiomu punya alamat seperti
`https://studio-kopiku.vercel.app` yang bisa dibuka dari HP siapa pun yang
tahu kode aksesnya.

Ketik di chat:

```
online-kan
```

AI (topi 🚀 DevOps) mengerjakan bagian teknis. Bagianmu hanya **tiga hal di
browser**.

> 🔐 **Hentikan share screen** selama langkah 1 dan 3 (login dan mengisi
> rahasia). Nyalakan lagi setelah situsmu tayang.

## 1. Login GitHub (sekali saja)

Kalau AI menampilkan **kode 8 huruf** (misalnya `ABCD-1234`):

1. Salin kodenya.
2. Buka link yang diberikan AI (biasanya https://github.com/login/device).
3. Tempel kode → **Continue** → **Authorize**.
4. Kembali ke chat, ketik **"sudah"**.

Kalau AI memintamu membuat repository sendiri: buka https://github.com/new,
isi nama proyek, pilih **Private**, jangan centang apa pun, klik **Create
repository**, lalu salin link-nya ke chat.

## 2. Import di Vercel

1. Buka https://vercel.com/new → **Continue with GitHub**.
2. Cari nama proyekmu → klik **Import**.
   (Tidak muncul? Klik **Adjust GitHub App Permissions**, pilih repository
   itu, **Save**.)
3. Jangan ubah pengaturan apa pun **kecuali** langkah 3 di bawah.

## 3. Isi rahasia di Vercel

Buka bagian **Environment Variables**, lalu tambahkan tiga baris:

| Key | Value |
|---|---|
| `YORAI_API_KEY` | kunci API Yorai-mu |
| `APP_PASSCODE` | kode akses studio |
| `DEMO_MODE` | `false` |

Klik **Deploy**. Tunggu 1–2 menit sampai muncul tulisan selamat 🎉, lalu
klik **Continue to Dashboard → Visit**. Salin alamat situsnya ke chat AI
supaya AI bisa mengeceknya.

## Selesai! Coba dari HP

1. Buka alamat situsmu di HP.
2. Masukkan kode akses.
3. Buat satu hasil, misalnya satu gambar. (Memakai saldo Yorai: gambar
   ±Rp400, video ±Rp2.000.)
4. Kirim link situsmu di chat meeting supaya teman-teman mencobanya. Kode
   aksesnya kirim lewat pesan pribadi saja, atau beri kode akses khusus
   untuk demo lalu ganti setelah kelas.

Mau mengubah sesuatu setelah online? Minta saja ke AI seperti biasa, lalu
bilang **"simpan dan kirim ke situs"**. Situsmu diperbarui otomatis dalam
±1 menit.

## Kalau ada masalah

| Yang terlihat | Yang kamu ketik ke AI |
|---|---|
| Deploy berwarna merah (Failed) | buka deploy yang merah → **Build Logs** → salin semua → tempel ke chat + "deploy gagal, tolong perbaiki" |
| Situs masih bertuliskan "Mode demo" | "situsku masih mode demo" |
| "Kunci API salah" | "kunci API di Vercel salah, pandu aku menggantinya" |
| "Saldo tidak cukup" | isi saldo di yorai.id, lalu coba lagi |

> Vercel paket **Hobby** gratis untuk proyek pribadi. Kalau studio ini nanti
> dipakai untuk jualan jasa, pertimbangkan paket Pro.
