# Farming Game

Game farming ala Stardew Valley / Harvest Moon. Dibangun bertiga di Ruang Muse
(Adi yang punya ide + kasih tugas awal, tiga Muse yang kerjain):

- **robo (Muse 1)** — lead build, kode + engine Godot, integrasi.
- **Ren (Muse 2)** — desain sistem + naskah/dialog NPC.
- **Blue (Muse 3)** — aset visual + testing + bahan publish.

Judul final game belum diputuskan — `farming-game` ini nama kerja repo.

## Cara jalanin

Godot 4.3. Buka `project.godot` di editor, atau:

```
Godot_v4.3-stable_linux.x86_64 --path .
```

## Milestone M1 (playable pertama, scope dikunci di room #274)

- Tanam – siram – panen di lahan.
- Siklus hari.
- 2 NPC + dialog dasar.
- Belum ada sistem uang / ATM — ATM masuk bareng sistem uang di milestone
  berikut (lihat `ATM-SPEC.md`).

## Keputusan desain (disepakati di Ruang Muse)

- `DESIGN-DECISIONS.md` — pilih gender awal (model Mineral Town, romance
  ikut kebalik), sistem ATM & bank, 5 kandidat nikah + 1 saingan tiap
  kandidat, scope tambahan (events, upgrade rumah, furnitur, masak, mancing,
  TV berita/belanja, minigame yang bisa dimainkan).
- `ATM-SPEC.md` — spek lengkap ATM & bank v1.0 (angka final Ren, room #261:
  pinjaman masuk v1 tapi terkunci sampai akhir musim 1).

## Struktur

- `scenes/` — scene Godot (Main, farm, rumah, kota, dst.)
- `scripts/` — GDScript
- `assets/` — sprite/tile/audio (bagian Blue)
- `data/` — data game (dialog JSON, tanaman, NPC — bagian Ren)
