# Selama membangun: apa yang Bos lakukan di setiap tahap

Tim AI bekerja dalam 6 tahap. Di setiap tahap, tugasmu hanya: **baca
laporannya, coba hasilnya, beri keputusan**. Setiap laporan AI diakhiri
pilihan (a)(b)(c); kamu boleh memilih atau menulis keinginanmu sendiri.

> **Aturan emas:** satu permintaan per pesan. Tunggu AI selesai dan coba
> hasilnya dulu sebelum meminta hal berikutnya.

---

## Tahap 1 — Fondasi (±15 menit)

AI memasang kerangka aplikasi. Prosesnya bisa 2–5 menit dan terlihat banyak
tulisan berjalan. Itu normal.

- **Antigravity / Codex minta izin menjalankan perintah?** Baca kalimatnya.
  Untuk `npm`, `npx`, `git`, dan `gh`, pilih **izinkan** (Accept / Yes). Kalau
  ragu, tanya: *"perintah ini untuk apa?"*
- Setelah selesai, AI memberi link `http://localhost:3000`. Buka di browser.
  Kalau tampil halaman, ketik **"lanjut"**.

> `localhost` artinya aplikasimu baru tayang **di laptopmu sendiri**, belum
> di internet. Seperti latihan di rumah sebelum tampil di panggung.

## Tahap 2 — Studio mode demo (±35 menit)

AI menyusun tampilan studio. Mode demo = hasil contoh, **gratis**, belum
memakai saldo.

Supaya tampilan mirip yorai.id, kirim pesan ini sambil **melampirkan
screenshot** dari folder `bekal/referensi-tampilan/` (seret gambar
`01-studio-grid.jpg` ke kolom chat):

```
Buat tampilan studionya semirip mungkin dengan screenshot ini, tapi pakai
nama dan warna studioku. Masih mode demo dulu.
```

Coba hal-hal ini, lalu sampaikan yang kurang dengan bahasamu:

☐ Ketik ide video → klik **Buat** → muncul kartu "sedang dibuat" → jadi.
☐ Klik pill pengaturan di composer → pilih rasio, durasi, kualitas.
☐ Harga di tombol Buat berubah saat durasi diganti.
☐ Pasang foto sebagai **frame awal**.
☐ Muat ulang halaman → hasilnya masih ada.
☐ Buka di HP (minta AI: *"tunjukkan cara membukanya di HP-ku"*) atau
   perkecil jendela browser.

Contoh masukan yang baik:

- *"Tombol Buat kurang menonjol."*
- *"Di HP, tulisan di kartu kepotong."*
- *"Warna cokelatnya terlalu gelap, buat lebih hangat."*
- *"Tambahkan contoh prompt tentang kopi di layar kosong."*

Kalau sudah puas: **"simpan, lalu lanjut ke tahap 3"**.

## Tahap 3 — AI sungguhan (±20 menit)

1. Ketik **"sambungkan ke AI sungguhan"**.
2. **Hentikan share screen dulu** kalau sedang share di meeting.
3. AI membuka file rahasia bernama `.env.local` untukmu. Di file itu:
   - tempel kunci API Yorai-mu tepat setelah `YORAI_API_KEY=` (tanpa spasi),
   - isi kode akses pilihanmu setelah `APP_PASSCODE=`, misalnya
     `kopiku2026`,
   - pastikan `DEMO_MODE=false`,
   - simpan (Cmd+S / Ctrl+S), tutup, lalu ketik **"sudah"** di chat.

   > 🔐 Jangan menempelkan kunci API di chat AI. Kalau terlanjur, cabut
   > kuncinya di https://yorai.id/app/api dan buat yang baru.

4. Nyalakan share screen lagi bila diminta pengajar.
5. AI menyebut perkiraan biaya video uji (±Rp2.000). Kalau setuju, ketik
   **"boleh, buat satu video uji"**.
6. Tunggu 1–3 menit. Video sungguhan pertamamu jadi! 🎉 Bagikan
   tangkapan layarnya (bukan kuncinya) di chat meeting.

## Tahap 4 — Uji dan audit keamanan (±10 menit)

Ketik:

```
cek keamanan dan kualitas sebelum online
```

AI berganti topi menjadi tester dan ahli keamanan, lalu melapor dengan
tanda ✅/❌. Kalau ada ❌, cukup ketik **"perbaiki"**.

## Tahap 5 — Online-kan (±20 menit)

Ketik **"online-kan"**, lalu ikuti
[03-ONLINE-KAN.md](03-ONLINE-KAN.md).

## Tahap 6 — Kembangkan (bonus / PR)

Contoh permintaan:

- *"Tambahkan tombol bantu tulis prompt dari ide singkat."*
- *"Tambahkan model video lain yang lebih bagus untuk produk."*
- *"Tambahkan tombol bagikan ke WhatsApp."*

---

## Kamus kalimat Bos

| Kamu ingin… | Ketik |
|---|---|
| AI meneruskan | **lanjut** |
| melihat hasil | **tunjukkan cara mencobanya** |
| menyimpan titik aman | **simpan** |
| membatalkan perubahan barusan | **batalkan** |
| meniru tampilan | lampirkan screenshot + **buat mirip ini** |
| tidak paham | **aku bingung, jelaskan pelan-pelan dengan contoh** |
| ada tulisan merah/error | tempel errornya + **ini apa? tolong perbaiki** |
| memeriksa keamanan | **cek keamanan** |
| menayangkan ke internet | **online-kan** |
| melanjutkan di chat baru | **lanjutkan proyek ini** |
| melihat catatan proyek | **tunjukkan catatan proyek kita** |
| memberi aturan baru ke tim | **catat: …** atau **mulai sekarang selalu …** |
