# Mulai: kamu Bosnya, AI timnya

Hari ini kamu tidak belajar ngoding. Kamu belajar **memimpin tim AI**.

- **Kamu = Bos.** Kamu bercerita, menjawab pertanyaan, mencoba hasilnya, dan
  memutuskan.
- **AI = seluruh tim.** Manajer proyek, desainer, programmer, tester, ahli
  keamanan, sampai orang yang menayangkan aplikasimu ke internet.

Hasil akhirnya: **studio AI milikmu sendiri** untuk membuat video, gambar,
dan suara (mirip Studio di yorai.id), yang bisa dibuka dari HP siapa pun
lewat link `.vercel.app`.

---

## Selama meeting

- Ikuti layar pengajar di meeting, tapi kerjakan semuanya di laptopmu
  sendiri.
- Tertinggal atau bingung? Tulis di chat meeting, misalnya *"Tahap 2, AI-ku
  berhenti"*. Pengajar/asisten bisa mengajakmu ke breakout room dan memintamu
  share screen.
- 🔐 **Hentikan share screen** sebelum mengisi kunci API, mengisi kode akses,
  atau login GitHub/Vercel. Nyalakan lagi setelah selesai.
- Ceritamu (S-P-M-K-C) boleh dibagikan di chat meeting untuk inspirasi
  teman. Kunci API tidak boleh.

## Langkah 1 — Siapkan folder proyek (5 menit)

1. Buat **folder kosong** di `Documents` dengan nama proyekmu, huruf kecil
   tanpa spasi, misalnya `studio-kopiku`.
2. Buka folder itu:
   - **Antigravity:** buka tampilan **Editor** (Cmd/Ctrl+E kalau yang muncul
     Agent Manager), **File → Open Folder**, pilih `studio-kopiku`. Di panel
     agen, pilih mode **Planning** dan model terbaru yang tersedia.
   - **Codex:** buka folder di aplikasi Codex, atau di Terminal ketik `cd`
     lalu seret folder ke jendela Terminal, Enter, lalu ketik `codex`.
3. Salin kalimat ini ke chat agen, lalu kirim:

   ```
   Ambil isi folder starter dari https://github.com/aigensee/kelas-videogen
   ke folder ini, termasuk folder tersembunyi, tanpa riwayat git milik kit
   itu. Lalu baca AGENTS.md dan ikuti aturannya. Jelaskan ke aku dalam 2
   kalimat apa yang sudah kamu siapkan.
   ```

   Kalau agen minta izin menjalankan perintah (untuk mengunduh), pilih
   **izinkan**.

> Cara lain: kalau pengajar membagikan folder `starter` lewat zip/Drive di
> chat meeting,
> salin folder itu, ganti namanya, buka, dan lewati langkah 3.

### Tentang file `AGENTS.md`: cukup tahu, tidak perlu menulis

Di dalam folder ada file bernama `AGENTS.md`. Bayangkan kamu membuka warung
dan merekrut karyawan baru. Supaya mereka langsung bisa bekerja, kamu
menempel **buku SOP** di dinding dapur: siapa mengerjakan apa, apa yang
dilarang, dan catatan pesanan hari ini. Karyawan membacanya sendiri setiap
pagi.

`AGENTS.md` adalah buku SOP itu untuk tim AI-mu:

- **Sudah disiapkan pengajar.** Kamu tidak menulisnya dari awal.
- **Dibaca AI otomatis** setiap kali chat dimulai.
- **Ditulis dan dirawat oleh AI.** Ceritamu, keputusanmu, dan kemajuan proyek
  dicatat AI sendiri di sana.
- **Kamu tidak perlu membukanya.** Semuanya lewat ngobrol:

| Kamu ingin… | Ketik |
|---|---|
| melihat isi catatan dengan bahasa biasa | **tunjukkan catatan proyek kita** |
| menambah aturan untuk tim | **catat: semua teks tombol pakai bahasa santai** |
| melarang sesuatu | **jangan pernah pakai warna ungu** |
| tahu file itu apa | **apa itu AGENTS.md?** |

> Jangan menghapus, mengganti nama, atau memindahkan file `AGENTS.md` dan
> folder `bekal`. Tanpa itu, tim AI-mu seperti karyawan baru tanpa SOP.

## Langkah 2 — Bercerita dengan rumus S-P-M-K-C (10 menit)

Ceritakan idemu seperti curhat ke teman. Supaya tim AI cepat paham, sebut
**5 hal** ini dengan bahasamu sendiri:

| | Pertanyaan untuk dirimu | Contoh |
|---|---|---|
| **S** — Siapa aku | Apa usaha / pekerjaanku? | "Aku punya usaha kopi susu kekinian di Bandung, namanya Kopiku." |
| **P** — Pengguna | Siapa yang akan memakai aplikasinya, lewat apa? | "Aku dan 2 admin media sosialku, kebanyakan lewat HP." |
| **M** — Masalah | Apa yang merepotkan sekarang? | "Tiap minggu butuh video pendek, foto promo, dan suara narasi untuk Reels/TikTok, tapi sewa videografer dan desainer mahal dan lama." |
| **K** — Keajaiban | Andai ada aplikasi ajaib, ia bisa apa? | "Ketik ide atau unggah foto produk, lalu jadi video 5–10 detik vertikal, gambar promo, atau suara narasi yang siap diunggah." |
| **C** — Contoh rasa | Tampilannya ingin seperti apa? | "Seperti Studio di yorai.id: gelap, elegan, rapi. Warna aksen cokelat kopi." |

Ketik ceritamu di chat agen. **Kalimat pembukanya selalu sama**, lalu
lanjutkan dengan ceritamu:

```
Halo tim! Aku Bos di proyek ini. Baca AGENTS.md dulu, lalu dengarkan
ceritaku:
```

Contoh pesan lengkap:

> Halo tim! Aku Bos di proyek ini. Baca AGENTS.md dulu, lalu dengarkan
> ceritaku:
>
> Aku punya usaha kopi susu kekinian di Bandung, namanya Kopiku. Yang akan
> memakai aplikasinya aku dan 2 admin sosmedku, kebanyakan dari HP. Masalahku,
> tiap minggu butuh video pendek, foto promo, dan suara narasi untuk Reels
> dan TikTok, tapi videografer dan desainer mahal dan lama. Aku ingin studio
> yang cukup diketik idenya atau diunggah foto produknya, lalu langsung jadi
> video 5–10 detik vertikal, gambar promo, atau suara narasi yang siap
> diunggah. Tampilannya seperti Studio di yorai.id: gelap, elegan, rapi,
> dengan warna aksen cokelat kopi.

Belum punya ide? Isi [LEMBAR-CERITA.md](LEMBAR-CERITA.md) dulu di aplikasi
catatan, lalu salin ke chat agen.

## Langkah 3 — Jawab pertanyaan tim (5 menit)

Tim AI akan bertanya balik **satu per satu**, selalu dengan pilihan.
Contohnya:

> 🧭 *Apakah studio ini perlu kode akses supaya saldo Yorai-mu tidak dipakai
> orang lain?*
> *(a) Ya, pakai kode akses (disarankan) (b) Tidak perlu*

Jawab cukup **a** atau **b**, atau dengan kata-katamu sendiri. Kalau bingung,
ketik **"terserah kamu"** dan AI memakai pilihan yang disarankan.

Setelah itu AI menulis **rencana 5 poin** dan perkiraan biaya per hasil
(per video, per gambar, per suara).
Kalau setuju, ketik:

```
lanjut
```

Mulai saat itu, lihat [02-SELAMA-MEMBANGUN.md](02-SELAMA-MEMBANGUN.md).

---

## Kalau besok mau melanjutkan

Buka folder yang sama, mulai **chat baru**, lalu ketik:

```
lanjutkan proyek ini
```

AI membaca catatan yang ia tulis sendiri di `AGENTS.md` dan melanjutkan dari
langkah terakhir.
