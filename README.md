# Kelas online "Jadi Bos, AI Timnya": membuat studio video AI dalam 2,5 jam

Peserta tanpa latar belakang IT membangun **studio video AI** mirip Video
Generator di yorai.id, memakai **API Yorai**, lalu menayangkannya di
**Vercel**, hanya dengan bercerita ke **Google Antigravity** atau
**ChatGPT Codex**. Kelas berlangsung **online lewat meeting** (Zoom/Google
Meet): peserta bekerja di laptop masing-masing sambil mengikuti meeting.

- **Peserta = Bos**: bercerita dengan rumus **S-P-M-K-C** (Siapa, Pengguna,
  Masalah, Keajaiban, Contoh rasa), menjawab pertanyaan, mencoba, memutuskan.
- **AI = seluruh tim**: 🧭 manajer proyek, 🎨 desainer, 🛠️ software engineer,
  🧪 tester, 🛡️ ahli keamanan, 🚀 DevOps. AI bertanya balik bila ada yang
  belum jelas.

## Isi folder

| Folder / file | Untuk | Isi |
|---|---|---|
| [`starter/`](starter/) | peserta (disalin jadi folder proyek) | `AGENTS.md` (aturan tim AI), `.agents/rules/`, `bekal/` (blueprint teknis, spesifikasi tampilan, cek kualitas & keamanan, panduan Vercel, screenshot acuan) |
| [`untuk-peserta/00-PERSIAPAN.md`](untuk-peserta/00-PERSIAPAN.md) | peserta, H-3 | akun GitHub/Vercel/Yorai, Node.js, Git, Antigravity/Codex |
| [`untuk-peserta/01-MULAI-DAN-BERCERITA.md`](untuk-peserta/01-MULAI-DAN-BERCERITA.md) | peserta | membuka proyek, rumus S-P-M-K-C, contoh cerita |
| [`untuk-peserta/LEMBAR-CERITA.md`](untuk-peserta/LEMBAR-CERITA.md) | peserta | lembar isian cerita + inspirasi |
| [`untuk-peserta/02-SELAMA-MEMBANGUN.md`](untuk-peserta/02-SELAMA-MEMBANGUN.md) | peserta | apa yang Bos lakukan di tiap tahap + kamus kalimat |
| [`untuk-peserta/03-ONLINE-KAN.md`](untuk-peserta/03-ONLINE-KAN.md) | peserta | GitHub + Vercel klik demi klik |
| [`untuk-peserta/04-KALAU-ADA-MASALAH.md`](untuk-peserta/04-KALAU-ADA-MASALAH.md) | peserta | pesan darurat siap salin |

## Alur singkat

```
Persiapan (sebelum meeting) → folder kosong + kalimat pembuka → "Halo tim! Aku Bos…" + cerita S-P-M-K-C
→ AI bertanya (≤5) → rencana → "lanjut"
→ Tahap 1 fondasi → Tahap 2 studio mode demo (gratis)
→ Tahap 3 AI sungguhan (isi .env.local, video uji ±Rp2.000)
→ Tahap 4 uji + audit keamanan → Tahap 5 "online-kan" (Vercel)
→ Tahap 6 kembangkan (PR)
```

## Cara peserta memulai

Buka folder kosong di Antigravity atau Codex, lalu kirim:

```
Ambil isi folder starter dari https://github.com/aigensee/kelas-videogen
ke folder ini, termasuk folder tersembunyi, tanpa riwayat git milik kit
itu. Lalu baca AGENTS.md dan ikuti aturannya. Jelaskan ke aku dalam 2
kalimat apa yang sudah kamu siapkan.
```

Setelah itu peserta bercerita (lihat `untuk-peserta/01-MULAI-DAN-BERCERITA.md`).
Peserta tidak pernah menulis atau membuka `AGENTS.md`; AI yang membaca dan
mencatat di sana.

## Mencoba kit ini

```bash
cp -R starter ~/Documents/uji-studio
cd ~/Documents/uji-studio && codex     # atau buka folder di Antigravity
```

Lalu kirim contoh cerita dari `untuk-peserta/01-MULAI-DAN-BERCERITA.md`.

Data model dan harga di `starter/bekal/BLUEPRINT-TEKNIS.md` dicek dari katalog
API Yorai pada 29 Oktober 2026. Cek ulang sebelum sesi di
https://yorai.id/docs/api/models.
