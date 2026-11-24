# KEPUTUSAN DESAIN — Game Farming Ruang Muse

Catatan keputusan desain yang sudah disepakati di Ruang Muse. Satu keputusan satu
bagian, dengan nomor pesan room-nya supaya bisa dilacak. Implementasi menyusul
sesudah Ren memfinalisasi detail sistemnya.

> Catatan: file riset yang disebut Ren
> (`game-bareng/riset-harvest-moon-stardew-valley-atm.md`) TIDAK ditemukan di
> workspace robo — kemungkinan ada di direktori Ren. Keputusan di bawah ini
> dicatat di sini supaya tidak hilang sebelum implementasi; Ren tolong
> sinkronkan ke dokumen risetnya.

---

## #252 — Pilih gender di awal game, romance ikut kebalik (model Mineral Town)

- **Status:** diputuskan di room oleh Adi (pesan #252, 24 Nov 2026), dijawab muse1 (pesan #256).
- **Keputusan:** di awal game pemain memilih karakter **cowok atau cewek**.
  Pilihan itu memengaruhi romance: cowok dikejar bachelorette, cewek dikejar
  bachelor — roster romance ikut kebalik mengikuti gender yang dipilih.
- **Bedanya dari Harvest Moon asli:** FoMT (Friends of Mineral Town) satu gender
  per versi game; di game kita dua-duanya ada dalam satu game.
- **Yang masih terbuka:** daftar bachelor/bachelorette + event romance-nya
  (bagian Ren: naskah/dialog NPC), dan aset karakter cowok/cewek (bagian Blue).
- **Implementasi:** bareng sistem ATM (#248) — gender-select masuk character
  creation, romance roster jadi data yang dibalik berdasarkan pilihan pemain.

## #248 — Sistem ATM & Bank

- **Status:** draf spek v1.0 selesai (24 Nov 2026), menunggu finalisasi Ren.
- **Dokumen:** [ATM-SPEC.md](ATM-SPEC.md) — 384 baris.
- **Ringkasan:** mesin ATM di alun-alun kota (24 jam), rekening via surat hari
  ke-3, setor/tarik bebas, bunga tabungan 1%/musim dari rata-rata saldo (cap
  1.000.000g), pinjaman bertingkat bunga 5%/musim + cicilan 10%/malam + denda
  500g, auto-deposit 0–100% dari hasil panen, pajak OFF di v1, buku bank 50
  entri, event bank jinak, 8 aturan anti-exploit.
- **Pembagian kerja:** Ren (angka + naskah), Blue (aset mesin ATM + UI), robo
  (implementasi Godot).

## #262 — 5 kandidat nikah, masing-masing ada saingannya

- **Status:** diputuskan di room oleh Adi (pesan #262, 24 Nov 2026), dijawab muse1 (pesan #265).
- **Keputusan:** seperti Harvest Moon — ada **5 kandidat nikah**, dan **setiap
  kandidat punya 1 saingan**. Roster kebalik per gender pemain (lanjutan #252):
  pemain cowok → 5 bachelorette + 5 saingan cowok; pemain cewek → 5 bachelor
  + 5 saingan cewek.
- **Sistem rival progresif:** saingan jalan sendiri (punya relasi sendiri dengan
  kandidatnya) dan **bisa menikahi kandidatnya kalau pemain kelamaan** — jadi
  ada tekanan waktu yang nyata, bukan sekadar pajangan.
- **Yang masih terbuka:** nama + kepribadian 5 kandidat dan 5 saingan per jalur,
  event rival, dan kecepatan progres rival (bagian Ren: naskah/dialog NPC;
  Blue: aset karakter).
- **Implementasi:** bareng #252 (gender-select + romance) dan #248 (ATM) —
  sistem relasi NPC dengan rival yang progresif.

## #268 — Roster + batas waktu diserahkan ke Ren; scope baru game

- **Status:** diputuskan di room oleh Adi (pesan #268, 24 Nov 2026 — menjawab
  pertanyaan Ren di #267), dijawab muse1 (pesan #270).
- **Keputusan (a) — pemilik sistem:** roster 5 kandidat + 5 saingan dan batas
  waktu lamaran saingan **diputuskan Ren saja** — itu bagian sistem/dialog dia
  (menutup poin terbuka di #262). Adi tidak ikut menentukan angkanya.
- **Keputusan (b) — scope baru game bareng:** ditambah fitur-fitur ini:
  - Events (acara musiman/kota).
  - Rumah bisa dibesarkan (upgrade rumah).
  - Beli barang buat isi rumah (furnitur/belanja perabot).
  - Masak.
  - Mancing.
  - TV — buat tau berita dan belanja.
  - Minigame yang bisa dimainkan (bukan sekadar cutscene).
- **Yang masih terbuka:** detail tiap fitur di atas (bagian Ren: sistem;
  Blue: aset). Scope ini menambah kerja — prioritaskan sesudah fondasi
  (bertani + ATM + romance) jalan.
