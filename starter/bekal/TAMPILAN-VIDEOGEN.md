# Spesifikasi tampilan (untuk 🎨 Desainer)

Targetnya: rasa seperti **Video Generator di yorai.id** (gaya "Flow"):
layar hasil yang lega, composer melayang di bawah, pengaturan di popover.
Tiru **tata letak dan nuansa**, bukan logo atau nama Yorai/PosterKu. Pakai
nama studio Bos dari `AGENTS.md`. Boleh ada teks kecil "Ditenagai API Yorai"
di pengaturan atau footer.

Screenshot acuan ada di `bekal/referensi-tampilan/` (dari contoh aplikasi
"PosterKu Studio" yang meniru Yorai). Buka dan lihat gambarnya sebelum
membuat tampilan:

| File | Yang ditiru |
|---|---|
| `01-studio-grid.jpg` | tata letak utama: rail kiri, topbar, tab, grid hasil, composer melayang |
| `02-studio-kosong.jpg` | keadaan kosong + contoh prompt |
| `03-popover-pengaturan.jpg` | popover pengaturan: model, rasio, durasi, kualitas |
| `05-kartu-progres.jpg` | kartu yang sedang diproses |
| `06-viewer.jpg` | tampilan detail satu hasil |
| `07-tema-terang.jpg` | mode terang |
| `08-hp-390px.jpg` | tampilan HP |

(`04-prompt-otomatis.jpg` hanya untuk bonus "Bantu tulis prompt".)

## Warna (token CSS di `globals.css`)

Gelap adalah bawaan; terang lewat tombol tema. Simpan pilihan tema di
`localStorage`.

| Token | Gelap | Terang |
|---|---|---|
| `--bg` | `#0a0a0c` | `#fafaf9` |
| `--surface` (kartu, composer) | `#121215` | `#ffffff` |
| `--surface-2` (pill, input) | `#1b1b20` | `#f4f4f1` |
| `--line` (garis) | `#2a2a31` | `#e7e5e0` |
| `--fg` (teks) | `#f4f4f5` | `#1c1917` |
| `--muted` (teks redup) | `#a1a1aa` | `#6b665e` |
| `--primary` (aksen, tombol Buat) | `#de0c77` atau warna pilihan Bos | sama |

Font: Geist (bawaan create-next-app). Sudut: kartu 16px, composer 20px,
pill dan tombol kecil `rounded-full` atau 10px. Bayangan hanya pada composer
dan popover.

## Tata letak desktop (≥ 1024px)

```
┌──┬──────────────────────────────────────────────────────────┐
│◆ │ Nama Studio                     [Mode demo] [Saldo] [⋯] │ topbar 56px
│▦ ├──────────────────────────────────────────────────────────┤
│  │ [Semua 9] [Berhasil] [Diproses]                          │
│  │ ┌────────┐ ┌────────┐ ┌────────┐                         │
│  │ │ video  │ │ video  │ │ video  │   grid masonry 3 kolom  │
│  │ │        │ └────────┘ │        │   (CSS columns)         │
│☀ │ └────────┘            └────────┘                         │
│🔒│      ┌───────────────────────────────────────┐           │
│  │      │ [+ Frame awal]                         │ composer │
│  │      │ Apa yang ingin kamu buat?              │ melayang │
│  │      │                              0 / 2000  │ lebar    │
│  │      │ [Seedance 2 Mini · 9:16 · 5 dtk · 480p ⌃] [Buat · Rp2.010]│
│  │      └───────────────────────────────────────┘           │
└──┴──────────────────────────────────────────────────────────┘
```

- **Rail kiri** 64px: logo (huruf pertama nama studio dalam kotak aksen),
  ikon galeri (aktif), lalu di bawah: tombol tema dan tombol keluar (kunci).
- **Topbar**: nama studio (kata kedua tebal), di kanan lencana "Mode demo"
  (bila demo) dan sisa saldo (bila tersedia), semuanya kecil dan redup.
- **Tab filter** dalam satu kotak `surface-2` dengan angka jumlah.
- **Grid**: masonry 3 kolom, jarak 16px, kartu mengikuti rasio videonya.
  Beri ruang kosong di bawah grid setinggi composer supaya kartu terakhir
  tidak tertutup.
- **Composer**: `position: sticky/fixed` di bawah tengah, lebar
  `min(50rem, 100% - 2rem)`, jarak 16px dari bawah, latar `surface`, garis
  `line`, bayangan besar.
  - Baris atas: slot **+ Frame awal** (kotak putus-putus 64px; klik atau seret
    foto; setelah dipilih tampil thumbnail + tombol ×), teks bantuan redup.
  - Tengah: textarea tanpa garis, placeholder "Apa yang ingin kamu buat?",
    tumbuh sampai 6 baris, penghitung karakter kecil di kanan bawah.
    Enter = kirim, Shift+Enter = baris baru.
  - Baris bawah kanan: **pill pengaturan** (ringkasan model · rasio · durasi ·
    resolusi + panah ⌃) dan tombol **Buat** berwarna aksen yang menampilkan
    harga ("Buat · Rp2.010", atau "Buat · Gratis" di mode demo).
  - Setelah Buat berhasil dikirim, prompt dan frame awal dikosongkan;
    pengaturan tetap.
- **Popover pengaturan** (muncul di atas pill, lebar 20rem, judul
  "Pengaturan video", tombol ×, tutup dengan Esc atau klik di luar):
  1. daftar model sebagai kartu yang bisa dipilih (nama, satu baris
     penjelasan, "mulai RpX" di kanan),
  2. **Rasio**: grid 3 kolom, tiap tombol berikon persegi panjang sesuai rasio,
  3. **Durasi**: tombol 5 dtk / 10 dtk,
  4. **Resolusi**: tombol sesuai model,
  5. sakelar **Suara**,
  6. catatan redup: "Harga final dicek sebelum dibuat."
  Pilihan aktif bergaris aksen. Opsi yang tidak didukung model terpilih
  disembunyikan (misalnya rasio saat memakai frame awal di PixVerse).

## Kartu hasil

- **Mengantre / diproses** (`05-kartu-progres.jpg`): latar `surface-2`
  dengan kilau bergerak halus, spinner kecil, teks "Mengantre…" atau
  "Sedang dibuat… 0:42" (waktu berjalan), potongan prompt 2 baris, dan bar
  progres perkiraan (naik pelan sampai 90% lalu menunggu).
- **Berhasil**: `<video>` muted, loop, `playsInline`, putar saat kursor di
  atas kartu (di HP: tampilkan frame pertama, ketuk untuk membuka viewer).
  Lencana kecil "▶ VIDEO" kiri atas. Saat hover, tombol ikon di kanan atas:
  Unduh, Pakai lagi, Hapus (dengan tooltip).
- **Gagal**: latar redup, ikon peringatan, pesan ramah, tombol "Coba lagi".
- Klik kartu → **viewer** (`06-viewer.jpg`): video besar dengan kontrol,
  panel samping berisi prompt (tombol Salin), model, rasio, durasi, resolusi,
  biaya, waktu dibuat, tanggal kedaluwarsa, tombol Unduh dan Pakai lagi.
  Esc/× menutup.

## Keadaan kosong (`02-studio-kosong.jpg`)

Ikon besar redup, judul "Belum ada video", satu kalimat ajakan, lalu 3 chip
contoh prompt yang relevan dengan usaha Bos. Klik chip → mengisi composer.

## Layar kode akses

Kartu kecil di tengah layar: nama studio, kolom "Kode akses" (type password,
tombol lihat), tombol "Masuk", pesan error di bawah kolom. Tanpa kode akses
aktif, layar ini dilewati.

## HP (390px, `08-hp-390px.jpg`)

- Rail kiri hilang; ikon tema dan keluar pindah ke topbar (menu ⋯).
- Grid 2 kolom, jarak 8px; tombol aksi kartu selalu tampil sebagai ikon kecil.
- Composer menempel di bawah selebar layar, sudut atas membulat, ikut
  `env(safe-area-inset-bottom)`. Pill pengaturan boleh menyingkat
  ("9:16 · 5 dtk") dan tombol Buat menjadi ikon kirim berharga kecil.
- Popover pengaturan menjadi lembar dari bawah (bottom sheet).
- Ukuran font input minimal 16px supaya iPhone tidak memperbesar halaman.
- Tidak boleh ada scroll ke samping.

## Aksesibilitas dan rasa profesional

- Semua tombol ikon punya `aria-label` dan tooltip; fokus keyboard terlihat
  (ring aksen).
- Kontras teks cukup di kedua tema.
- Setiap aksi punya umpan balik: tombol sedang diproses (spinner + nonaktif),
  toast kecil untuk berhasil/gagal ("Video sedang dibuat", "Prompt disalin").
- Teks antarmuka berbahasa Indonesia, singkat, tanpa istilah teknis.
