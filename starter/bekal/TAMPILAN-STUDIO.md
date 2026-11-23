# Spesifikasi tampilan studio (untuk 🎨 Desainer)

Targetnya: rasa seperti **Studio di yorai.id** (gaya "Flow"): layar hasil
yang lega, composer melayang di bawah, pengaturan di popover. Satu studio
membuat tiga jenis hasil: **Video**, **Gambar**, dan **Suara** (narasi atau
musik). Di antarmuka, sebut produknya "studio" dan hasilnya "hasil" (bukan
"video" saja), kecuali saat memang membicarakan satu jenis.

Tiru **tata letak dan nuansa**, bukan logo atau nama Yorai, dan bukan nama
"Kopiku" dari screenshot. Pakai nama studio Bos dari `AGENTS.md`. Boleh ada
teks kecil "Ditenagai API Yorai" di pengaturan atau footer.

Screenshot acuan ada di `bekal/referensi-tampilan/`. Semuanya diambil dari
studio contoh "Studio Kopiku" (mode demo, aksen `#de0c77`) saat jenis
**Video** terpilih. Gambar dan Suara memakai tata letak yang sama; bedanya
dijelaskan di dokumen ini. Buka dan lihat gambarnya sebelum membuat
tampilan:

| File | Yang ditiru |
|---|---|
| `01-studio-grid.jpg` | tata letak utama: rail kiri, topbar, tab, grid masonry, composer melayang |
| `02-studio-kosong.jpg` | keadaan kosong + chip contoh prompt |
| `03-popover-pengaturan.jpg` | popover pengaturan: model, rasio, durasi, resolusi, suara |
| `04-kartu-gagal.jpg` | kartu gagal + tab "Gagal" |
| `05-kartu-progres.jpg` | kartu yang sedang diproses + pesan batas 3 proses |
| `06-viewer.jpg` | tampilan detail satu hasil |
| `07-tema-terang.jpg` | mode terang |
| `08-hp-390px.jpg` | tampilan HP |
| `09-hp-pengaturan.jpg` | pengaturan di HP sebagai lembar dari bawah |
| `10-kode-akses.jpg` | layar kode akses (dengan pesan salah) |

**Aturan prioritas:** nama studio, teks contoh, dan warna aksen di screenshot
hanyalah contoh ("Kopiku"). Pakai nama, contoh prompt, dan warna dari cerita
Bos. Bila gambar dan teks dokumen ini berbeda, **ikuti teks dokumen ini**.
Gambar kotak berwarna di kartu adalah hasil mode demo, bukan desain kartu.

**Jangan** menambah gradien dekoratif, efek kaca buram (glassmorphism) di
luar topbar, emoji sebagai ikon, ilustrasi, atau library UI. Tampilan harus
netral dan tenang; warna hanya dari token di bawah, aksen hanya untuk tombol
utama, pilihan aktif, sakelar, dan bar progres.

## Warna (token CSS di `globals.css`)

**Nama kelas CSS:** proyek memakai Tailwind, jadi jangan memberi nama kelas
buatan sendiri yang sama dengan kelas Tailwind (`grid`, `flex`, `hidden`,
`block`, `container`, `columns-*`, dan sejenisnya). Contoh: pakai `.gallery`,
bukan `.grid`, untuk grid hasil; kalau tidak, grid berubah jadi satu kolom
besar.

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
│  │ [Semua 9] [Video] [Gambar] [Suara] [Diproses] [Gagal]    │
│  │ ┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐                     │
│  │ │video │ │gambar│ │suara │ │video │ grid masonry 4 kolom│
│  │ │      │ └──────┘ └──────┘ │      │ (CSS columns)       │
│☀ │ └──────┘                   └──────┘                     │
│🔒│      ┌───────────────────────────────────────┐           │
│  │      │ (Video) (Gambar) (Suara)               │ composer │
│  │      │ [+ Frame awal]                         │ melayang │
│  │      │ Apa yang ingin kamu buat?              │ lebar    │
│  │      │                              0 / 2000  │          │
│  │      │ [Seedance 2 Mini · 9:16 · 5 dtk · 480p ⌃] [Buat · Rp2.010]│
│  │      └───────────────────────────────────────┘           │
└──┴──────────────────────────────────────────────────────────┘
```

- **Rail kiri** 64px: logo (huruf pertama nama studio dalam kotak aksen),
  ikon galeri (aktif), lalu di bawah: tombol tema dan tombol keluar (kunci).
- **Topbar**: nama studio (kata kedua tebal), di kanan lencana "Mode demo"
  (bila demo) dan sisa saldo (bila tersedia), semuanya kecil dan redup.
- **Tab filter** dalam satu kotak `surface-2` dengan angka jumlah: Semua,
  lalu satu tab per jenis yang aktif di studio (Video / Gambar / Suara),
  Diproses, Gagal. Jenis yang tidak dipakai Bos tidak punya tab.
- **Grid**: masonry dengan CSS `columns`: 4 kolom di layar ≥ 1280px, 3 kolom
  di 1024–1279px, 2 kolom di bawahnya; jarak 16px. Kartu video dan gambar
  mengikuti rasionya; kartu suara berukuran tetap (lihat Kartu hasil).
  Beri ruang kosong di bawah grid setinggi composer supaya kartu terakhir
  tidak tertutup.
- **Composer**: `position: sticky/fixed` di bawah tengah, lebar
  `min(50rem, 100% - 2rem)`, jarak 16px dari bawah, latar `surface`, garis
  `line`, bayangan besar.
  - Paling atas: **pilihan jenis** berupa segmen kecil (Video · Gambar ·
    Suara) di kiri. Pilihan aktif bergaris/berlatar aksen. Bila studio hanya
    punya satu jenis, segmen ini disembunyikan. Jenis terakhir yang dipakai
    diingat di `localStorage`.
  - Baris lampiran (hanya bila jenis itu menerima foto): slot kotak
    putus-putus 64px; klik atau seret foto; setelah dipilih tampil thumbnail
    + tombol ×, teks bantuan redup. Labelnya mengikuti jenis:
    **+ Frame awal** (Video), **+ Foto acuan** (Gambar). Suara tidak punya
    slot ini.
  - Tengah: textarea tanpa garis, tumbuh sampai 6 baris, penghitung karakter
    kecil di kanan bawah. Enter = kirim, Shift+Enter = baris baru.
    Placeholder mengikuti jenis:
    - Video: "Apa yang ingin kamu buat?"
    - Gambar: "Gambarkan gambar yang kamu mau…"
    - Suara (narasi): "Tulis teks yang ingin dibacakan…"
    - Suara (musik): "Ceritakan musik yang kamu mau: suasana, alat musik…"
  - Baris bawah kanan: **pill pengaturan** (ringkasan sesuai jenis + panah ⌃)
    dan tombol **Buat** berwarna aksen yang menampilkan harga
    ("Buat · Rp2.010", atau "Buat · Gratis" di mode demo). Contoh ringkasan:
    - Video: "Seedance 2 Mini · 9:16 · 5 dtk · 480p"
    - Gambar: "Nano Banana 2 Lite · 1:1"
    - Suara: "Narasi · Fola" atau "Musik · instrumental"
  - Setelah Buat berhasil dikirim, prompt dan lampiran dikosongkan;
    pengaturan tetap.
- **Popover pengaturan** (muncul di atas pill, lebar 20rem, judul mengikuti
  jenis: "Pengaturan video" / "Pengaturan gambar" / "Pengaturan suara",
  tombol ×, tutup dengan Esc atau klik di luar). Selalu diawali daftar model
  sebagai kartu yang bisa dipilih (nama, satu baris penjelasan, "mulai RpX"
  di kanan), lalu:
  - **Video**: Rasio (grid 3 kolom, tiap tombol berikon persegi panjang
    sesuai rasio), Durasi (5 dtk / 10 dtk), Resolusi (sesuai model), sakelar
    **Suara di video**.
  - **Gambar**: Rasio (grid 3 kolom seperti video).
  - **Suara**: pilihan **Narasi / Musik** (sebagai kartu model). Narasi:
    pilihan **Suara pembaca** (tombol berlabel nama suara) dan **Gaya bicara**
    (Hangat · Semangat · Tenang). Musik: sakelar **Tanpa vokal
    (instrumental)**.
  - Catatan redup di bawah: "Harga final dicek sebelum dibuat."
  Pilihan aktif bergaris aksen. Opsi yang tidak didukung model terpilih
  disembunyikan (misalnya rasio saat memakai frame awal di PixVerse).

## Kartu hasil

Setiap kartu punya **lencana jenis** kecil di kiri atas: "▶ VIDEO",
"GAMBAR", atau "♪ SUARA".

- **Mengantre / diproses** (`05-kartu-progres.jpg`): latar `surface-2`
  dengan kilau bergerak halus, spinner kecil, teks "Mengantre…" atau
  "Sedang dibuat… 0:42" (waktu berjalan), potongan prompt 2 baris, dan bar
  progres perkiraan (naik pelan sampai 90% lalu menunggu). Bentuk kartu
  sudah mengikuti jenis dan rasio yang dipilih.
- **Berhasil**:
  - **Video**: `<video>` muted, loop, `playsInline`, putar saat kursor di
    atas kartu (di HP: tampilkan frame pertama, ketuk untuk membuka viewer).
  - **Gambar**: `<img>` dengan `loading="lazy"` dan teks `alt` dari potongan
    prompt.
  - **Suara**: kartu berukuran tetap (kira-kira rasio 4:3) berlatar
    `surface-2`: ikon gelombang suara besar redup, potongan prompt 2 baris,
    durasi, dan tombol putar/jeda bulat beraksen. Pakai `<audio>` bawaan
    (tanpa library); kontrol tampil saat diputar atau di viewer.
  - Saat hover, tombol ikon di kanan atas: Unduh, Pakai lagi, Hapus (dengan
    tooltip).
- **Gagal**: latar redup, ikon peringatan, pesan ramah, tombol "Coba lagi".
- Klik kartu → **viewer** (`06-viewer.jpg`): hasil besar (video dengan
  kontrol, gambar ukuran penuh, atau pemutar suara lebar), panel samping
  berisi jenis, prompt (tombol Salin), model, pengaturan yang dipakai (rasio,
  durasi, resolusi, suara pembaca, sesuai jenis), biaya, waktu dibuat,
  tanggal kedaluwarsa, tombol Unduh dan Pakai lagi. Esc/× menutup.

## Keadaan kosong (`02-studio-kosong.jpg`)

Ikon besar redup, judul "Belum ada hasil", satu kalimat ajakan (misalnya
"Buat video, gambar, atau suara pertamamu."), lalu 3 chip contoh prompt yang
relevan dengan usaha Bos untuk jenis yang sedang dipilih. Klik chip →
mengisi composer.

## Layar kode akses

Kartu kecil di tengah layar: nama studio, kolom "Kode akses" (type password,
tombol lihat), tombol "Masuk", pesan error di bawah kolom. Tanpa kode akses
aktif, layar ini dilewati.

## HP (390px, `08-hp-390px.jpg`)

- Rail kiri hilang; ikon tema dan keluar pindah ke topbar (menu ⋯).
- Tab filter boleh digeser ke samping di dalam kotaknya sendiri (bukan
  halaman).
- Grid 2 kolom, jarak 8px; tombol aksi kartu selalu tampil sebagai ikon kecil.
- Composer menempel di bawah selebar layar, sudut atas membulat, ikut
  `env(safe-area-inset-bottom)`. Segmen jenis tetap tampil di atas. Pill
  pengaturan boleh menyingkat ("9:16 · 5 dtk", "1:1", "Narasi") dan tombol
  Buat menjadi ikon kirim berharga kecil.
- Popover pengaturan menjadi lembar dari bawah (bottom sheet).
- Ukuran font input minimal 16px supaya iPhone tidak memperbesar halaman.
- Tidak boleh ada scroll ke samping.

## Aksesibilitas dan rasa profesional

- Semua tombol ikon punya `aria-label` dan tooltip; fokus keyboard terlihat
  (ring aksen).
- Kontras teks cukup di kedua tema.
- Setiap aksi punya umpan balik: tombol sedang diproses (spinner + nonaktif),
  toast kecil untuk berhasil/gagal ("Gambar sedang dibuat", "Prompt
  disalin").
- Teks antarmuka berbahasa Indonesia, singkat, tanpa istilah teknis.
