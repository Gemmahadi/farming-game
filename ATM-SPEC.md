# SPEK SISTEM ATM & BANK — Game Farming Ruang Muse

> **Status:** DRAF v1.0 — 24 November 2026
> **Tujuan:** bahan kerja untuk **Ren** (desain sistem + angka + naskah/dialog NPC) sebagai pemilik detail sistem. Bukan keputusan final sepihak — semua angka bertanda ✅ REKOMENDASI masih boleh diubah Ren, lalu diputuskan bareng Adi.
> **Pembaca:** Ren (sistem + naskah), Blue (aset visual + UI), robo (implementasi Godot).
> **Riset:** semua klaim tentang Stardew/Harvest Moon/mod sudah dicek ke sumber aslinya (Stardew Valley Wiki, halaman Nexus Mods, ulasan Story of Seasons). Detail yang tidak bisa diverifikasi ditandai ⚠️ BELUM TERVERIFIKASI.

---

## 0. Ringkasan eksekutif — keputusan kunci (draf)

| Aspek | Keputusan draf | Alasan singkat |
|---|---|---|
| Mesin ATM | 1 mesin fisik di alun-alun kota, buka 24 jam | ATM = akses uang kapan saja; beda dari toko yang tutup |
| Rekening | 1 rekening per pemain, dibuka lewat surat bank (hari ke-3) | Pola RBTS (surat hari ke-3) — terasa seperti institusi beneran |
| Mata uang | Tetap 1 mata uang (emas/`g`) — dompet vs tabungan | Sama seperti Stardew: dua kantong, satu mata uang |
| Setor / tarik | Bebas, tanpa biaya; tombol cepat 100 / 1.000 / 10.000 / Semua | Pola Pelican Valley Bank — terbukti nyaman |
| Bunga tabungan | ✅ **1% per musim** dari **rata-rata saldo harian**, cap saldo berbunga **1.000.000g**, dibayar tiap tanggal 1 musim baru | Angka Pelican Valley Bank (1%/musim, cap 1 jt) — dirancang supaya "menabung terasa worth it tanpa menggantikan penghasilan bertani" |
| Pinjaman | 1 pinjaman aktif; limit bertingkat dari total penghasilan; bunga **5% per musim**; cicilan otomatis 10% pokok awal per malam; biaya layanan 2% di depan | Gabungan ATM mod + Bank of Ferngill, disederhanakan |
| Auto-deposit | Slider 0–100% dari penghasilan shipping tiap malam, default **0% (mati)** | Pola RBTS — pemain yang memilih |
| Shipping bin | Uang panen masuk **dompet** dulu (vanilla), lalu auto-deposit membagi ke bank | Jangan ubah aturan vanilla yang sudah enak |
| Pajak | ❌ **OFF di v1** (fase 2, opsional) | RBTS membuktikan pajak bikin game terasa berat; tunda sampai ekonomi stabil |
| Buku bank | Riwayat 50 transaksi terakhir di dalam UI ATM | Pola Pelican Valley Bank |
| Event acak bank | ON, tapi versi jinak (tanpa event "kena hack −75%") | Ferngill membuktikan event bikin bank hidup; versi brutalnya jangan ditiru |
| Anti-exploit utama | Bunga dari **rata-rata saldo** (bukan saldo akhir musim) + cap + bunga dihitung dari **saldo bersih** (saldo − pinjaman) | Menutup tiga celah: setor di hari terakhir, bunga tak terbatas, dan arbitrase pinjam-lalu-deposit |

---

## 1. Riset — bagaimana game lain mengatur uang

### 1.1 Stardew Valley (vanilla) — TIDAK ada bank/ATM

Terverifikasi dari Stardew Valley Wiki (halaman *Gold* dan *Shipping*):

- **Uang = satu angka dompet.** Emas (`g`) tampil di kanan atas layar, di bawah jam. Tidak ada rekening, tidak ada bank, tidak ada ATM.
- **Pemasukan utama = shipping bin.** Barang dimasukkan ke kotak pengiriman di samping rumah; uang diterima **setelah tidur**, keesokan paginya. Harga jualnya sama persis dengan jual ke pedagang — bedanya cuma waktu bayar.
- **Jual langsung ke toko = dibayar tunai saat itu juga.** Pierre (tanaman/makanan), Willy (ikan), Clint (bijih/batang/permata). Tapi tidak semua barang bisa dijual ke toko (mis. senjata cuma ke Adventurer's Guild), dan barang yang dijual ke toko **tidak** masuk statistik shipping/achievement.
- **Sumber lain:** papan Help Wanted (harian), Special Orders (mingguan), surat hadiah (orang tua, cashback Pierre, dana pertanian dari Lewis), upgrade tempat sampah, trinket Parrot Egg.
- **Mata uang lain ada, tapi bukan uang:** Calico Egg, star token, Qi Gem, dsb. — pedagang tertentu menolak emas.
- **Modal awal pemain kecil** (angka persisnya ⚠️ belum terverifikasi dari cuplikan yang diambil; wiki umumnya menulis 500g).
- Satu-satunya "kotak uang" di vanilla adalah *deposit box* di depan rumah di Story of Seasons (bukan bank — lihat 1.2).

**Pelajaran buat kita:** sistem vanilla sengaja simpel — satu dompet, dua jalur jual (bin = besok, toko = sekarang). ATM/bank di game kita harus **menambah** kedalaman tanpa merusak kesederhanaan itu. Aturan "toko bayar sekarang, bin bayar besok" jangan diubah.

### 1.2 Harvest Moon / Story of Seasons — juga TIDAK ada bank/ATM

Terverifikasi dari ulasan dan manual resmi:

- **Harvest Moon klasik:** jual lewat shipping bin di sudut ladang; barang dikumpulkan sekitar jam 5 sore, uang bertambah. Beberapa judul bisa jual langsung ke toko desa. Tidak ada bank — uang satu angka dompet. (Satu pengecualian unik: *Harvest Moon GB3: Boy Meets Girl* — harus menelepon dulu sebelum jam 6 sore supaya barang terkirim.)
- **Story of Seasons (3DS, 2014): shipping bin DIHAPUS.** Diganti **Trade Depot** — dagang dengan pedagang asing yang datang bergiliran; tiap pedagang membayar beda per komoditas, dan harga jatuh kalau kebanyakan menjual satu barang. Ini bikin tempo awal game lambat (hanya 1 pedagang, 1–2× seminggu) — keluhan yang dicatat banyak ulasan.
- **Story of Seasons: A Wonderful Life:** tiga jalur penghasilan — shipping bin (biasa), pedagang keliling (2× sebulan), dan lapak pasar sendiri (butuh waktu menarik pembeli).
- **Story of Seasons: Grand Bazaar:** simpan semua sampai pasar Sabtu, jual langsung ke pelanggan dengan markup; toko lokal beli kapan saja tapi lebih murah.
- **Story of Seasons: Friends of Mineral Town:** shipping bin klasik — sekali sehari seseorang dari kota mengambil dan membayar.

**Pelajaran buat kita:** seri ini bereksperimen mengganti shipping bin dengan sistem dagang — hasilnya: lebih dalam, tapi lebih lambat dan bikin frustrasi di awal. Jadi game kita **tetap pakai shipping bin vanilla** sebagai jalur utama, ATM hanya mengelola uangnya, bukan mengganti cara menjual.

### 1.3 Mod bank/ATM Stardew Valley — yang sudah dicoba orang

| Mod | Status | Inti sistem | Angka penting (terverifikasi) |
|---|---|---|---|
| **ATM** (Platonymous, Nexus #3135) | v1.1.2, upload 7 Des 2018, update terakhir 10 Mei 2019; **rusak di 1.4+** (error PyTK), update 1.6 dijanjikan Des 2023 ⚠️ belum terverifikasi rilis atau tidak | Mesin ATM fisik di kota (default kiri RS), setor/tarik, money order = auto-deposit harian, kredit (pinjaman) | Bunga pinjaman & simpanan dibayar/ditagih **tiap tanggal 1 musim**; limit kredit menyesuaikan **tiap bulan**; kalau saldo kurang, ditarik dari dompet atau ditunda ke musim berikut; host-only di multiplayer |
| **Bank of Ferngill** (Mizzion, Nexus #13043) | Cocok 1.5.6 | Bank di ruangan Vault Community Center (atau meja JojaMart rute Joja); setor, bunga, pinjaman | Bunga **tiap malam** sebelum save; pinjaman + biaya layanan; cicilan harian (persentase pinjaman per malam, bisa diatur); event acak ~**10% tiap malam**: pasar saham jatuh (uang bank berkurang acak), pasar naik (bertambah acak), **kena hack −75% saldo bank**, apresiasi nasabah (hadiah acak), utang dihapuskan; Hard Mode: mulai dengan utang, tidak bisa setor sebelum lunas |
| **Pelican Valley Bank** (SourceNine Labs, Nexus #50878) | v0.1.0, untuk SV 1.6 / SMAPI 4.1.0+ | Rekening + buku bank (tombol B), riwayat transaksi lengkap | Bunga **1% per musim (28 hari)**, cap saldo berbunga **1.000.000g**, bunga diakru harian tapi dibayar utuh di awal musim berikut; tombol cepat 100/1.000/10.000g + Setor/Tarik Semua; **tanpa pinjaman, tanpa biaya, tanpa denda** di v0.1.0; transfer tidak menciptakan uang |
| **Simple Bank & Charity** (Nexus #54817) | SV 1.6 / SMAPI 4, PC + Android | Bank simpel + donasi amal berhadiah | Nominal 100/1.000/10.000/100.000g (+1 jt/10 jt/100 jt mode besar); donasi min 1.000g, 1×/hari default; hadiah naik per jumlah (1.000g→1 hadiah … 50 jt→60, maks 80); riwayat 100 donasi; **tanpa bunga, tanpa pinjaman**; single-player only |
| **RBTS — Regional Banking & Taxes System** (Losgar38, Nexus #41480) | v2.0.1, update 24 Jan 2026, SV 1.6 / SMAPI 4.4.0+ | Sistem bank + pajak paling lengkap; dua bank: **Joja Premium Bank** (bunga lebih tinggi, aturan ketat) vs **Cooperative Market Bank** (bunga lebih rendah, pemaaf) | Bunga dibayar **tanggal 1 tiap musim** dari **rata-rata saldo musim lalu** (anti-exploit eksplisit); auto-deposit **0–100%** penghasilan shipping; pajak mulai **Musim Panas Tahun 1**, ditagih tiap tanggal 1, dari penghasilan + modal + properti; pajak tak dibayar → utang + denda mingguan + potong dari shipping; Joja bisa blacklist, Koperasi bisa memaafkan utang |
| **Stardew ATM** (Nexus #43744) | — | ATM yang bisa **dibuat (crafting)** — surat + blueprint + battery pack hari ke-1, resep butuh 200 batu; semua ATM terhubung | **Tanpa bunga, tanpa pinjaman** — murni setor/tarik dari mana saja (ladang, tambang). Bukti bahwa ATM "craftable" adalah ide yang disukai pemain |

#### Koreksi terhadap bekal awal (hasil verifikasi)

1. ✅ ATM mod: bunga tiap musim + pinjaman + auto-deposit — **benar**, tapi bunga dihitung tiap tanggal 1 musim dan limit kredit menyesuaikan bulanan; angka rate defaultnya ⚠️ **belum terverifikasi** (tidak tertulis di halaman Nexus; perlu baca config default dari source code).
2. ✅ Bank of Ferngill: bunga tiap malam + pinjaman + event pasar saham/hack — **benar**, termasuk angka −75% untuk event hack dan peluang ~10%/malam.
3. ✅ Pelican Valley Bank: rekening + buku bank, bunga 1%/musim, cap 1 jt — **benar persis**.
4. ✅ Simple Bank & Charity — **benar**, dan penting: mod ini **tidak punya bunga dan tidak punya pinjaman** (cuma simpan + donasi).
5. ⚠️ RBTS: "dua bank Joja vs Koperasi, pajak musiman, utang" — **benar, tapi "utang" di RBTS berasal dari pajak yang tak dibayar, BUKAN dari pinjaman**; RBTS v2.0.1 tidak punya fitur pinjaman sama sekali. Angka rate bunga per bank dan tarif pajaknya ⚠️ **belum terverifikasi** (tidak dipublikasikan di halaman Nexus; bisa diatur lewat GMCM).
6. ➕ Temuan tambahan: **Stardew ATM** (craftable, tanpa bunga) — pola bagus untuk ATM kedua di ladang sebagai hadiah progresi.

---

## 2. Desain ATM untuk game kita (dirancang dari nol)

### 2.1 Konsep & nama

- **Satu bank, satu ATM.** Bank bernama **"Bank Tani Makmur"** (nama draf — Ren boleh ganti; alternatif: "Koperasi Tani Desa", "Bank Desa"). Cabang di kota; ATM di depan bank, alun-alun kota.
- **ATM buka 24 jam**, 7 hari seminggu. Ini pembeda utamanya dari toko: uang bisa diakses kapan saja, bahkan tengah malam. (Konsekuensi desain: tidak ada "teller tutup" — kalau mau ada NPC teller, dia hanya untuk dialog/tutorial, bukan penjaga akses.)
- **ATM kedua (craftable) sebagai hadiah progresi** — mengikuti pola Stardew ATM: setelah pemain mencapai sesuatu (mis. total penghasilan 100.000g, atau berteman baik dengan tukang kayu), dapat blueprint ATM ladang. ATM ladang = fungsi sama, lokasi beda. ⚠️ Keputusan: masuk v1 atau fase 2 — Ren yang putuskan.
- **Bahasa & rasa:** semua teks Indonesia, santai, khas desa. Nama-nama menu: "Setor", "Tarik", "Pinjam", "Buku Tabungan", "Setor Otomatis". Hindari istilah perbankan kaku ("kliring", "debet") kecuali untuk lelucon NPC.

### 2.2 Akun & mata uang

- **Mata uang tetap satu: emas (`g`).** Dua kantong: **Dompet** (uang tunai, satu-satunya yang bisa dibelanjakan di toko) dan **Tabungan** (uang di bank, tidak bisa langsung dibelanjakan — harus ditarik dulu). Aturan ini meniru Pelican Valley Bank ("emas harus ditarik ke dompet dulu baru bisa dibelanjakan") dan menciptakan keputusan bermakna: simpan = aman + berbunga, tapi tidak likuid.
- **Pembukaan rekening:** surat dari Bank Tani Makmur tiba di kotak surat pada **hari ke-3 Musim Semi Tahun 1** (pola RBTS). Isi surat: undangan buka rekening + penjelasan singkat. Buka rekening **gratis**, langsung di ATM (tombol "Buka Rekening"). Setelah buka, pemain mendapat **nomor rekening** (dibuat dari nama pemain + 4 digit acak, mis. `BTM-0142-8831`) yang tampil di UI + buku tabungan.
- **Satu rekening per pemain.** Tidak ada rekening bersama/keluarga di v1.
- **Tidak ada biaya administrasi bulanan.** Biaya hanya muncul dari pinjaman (bunga + biaya layanan). Ini menjaga bank terasa ramah untuk pemain baru.

### 2.3 Setor & tarik

- **Setor:** pindahkan emas Dompet → Tabungan. **Tarik:** Tabungan → Dompet. Keduanya instan, tanpa biaya, tanpa batas jumlah per hari.
- **Tombol cepat:** 100 / 1.000 / 10.000 / **Semua** (pola Pelican). Plus kolom ketik jumlah manual + numpad layar sentuh (penting untuk HP).
- **Aturan:** tidak bisa setor melebihi isi dompet; tidak bisa tarik melebihi saldo tabungan. Jumlah tidak valid → bunyi error + pesan ("Saldo tidak cukup!") — tanpa menghukum pemain.
- **Transfer tidak menciptakan uang.** Setor/tarik murni memindahkan emas antar kantong (prinsip Pelican). Satu-satunya sumber emas baru dari bank adalah bunga tabungan — dan itu dibatasi ketat (lihat 2.4).

### 2.4 Bunga tabungan — ✅ REKOMENDASI: 1% per musim dari rata-rata saldo

**Rekomendasi: 1% per musim (28 hari), dihitung dari rata-rata saldo harian musim itu, cap saldo berbunga 1.000.000g, dibayar tanggal 1 musim baru lewat surat + entri buku bank.**

**Kenapa angka ini:**

| Alasan | Penjelasan |
|---|---|
| Sudah terbukti seimbang | Ini angka default Pelican Valley Bank, yang penulisnya secara eksplisit merancangnya supaya "menabung terasa worth it tanpa menggantikan penghasilan bertani". Kita tidak perlu menemukan ulang angka yang sudah diuji komunitas. |
| Kecil dibanding bertani aktif | 1%/musim ≈ 4%/tahun. Contoh: saldo 100.000g → bunga **1.000g/musim**. Satu hari bertani aktif di mid-game menghasilkan jauh lebih besar. Bunga = bonus manis, bukan strategi utama. |
| Cap mencegah spiral kaya | Saldo di atas 1.000.000g tidak berbunga. Bunga maksimal **10.000g/musim**. Pemain kaya didorong membelanjakan/menginvestasikan, bukan menimbun. |
| Rata-rata saldo = anti-exploit | Mengikuti RBTS: bunga dihitung dari **rata-rata saldo harian selama satu musim**, bukan saldo di hari terakhir. Menyetor 1 jt di hari ke-28 tidak memberi bunga penuh (lihat 2.11). |

**Matematika contoh (buat Ren saat balancing):**

| Saldo rata-rata semusim | Bunga 1% | Artinya |
|---|---|---|
| 10.000g | 100g | Uang jajan — setara 1–2 tanaman murah |
| 50.000g | 500g | Lumayan — setara bibit satu petak |
| 200.000g | 2.000g | Terasa — ~1 alat upgrade kecil |
| 1.000.000g (cap) | 10.000g | Maksimal — hadiah untuk penabung disiplin |

**Opsi yang Ren boleh pertimbangkan (bukan rekomendasi):**
- **2%/musim** kalau Adi/Ren mau bank terasa lebih "dermawan" — bunga maks jadi 20.000g/musim; tetap kecil vs penghasilan bertani late-game.
- **Bunga harian kecil** ala Bank of Ferngill (bunga tiap malam) — terasa lebih hidup, tapi lebih sulit dibalance dan lebih mudah dieksploitasi; tidak direkomendasikan untuk v1.
- **Bunga dibayar per musim, tapi diakru harian** (pola Pelican: pecahan ditahan sampai genap) — detail implementasi, robo yang urus.

**Aturan pembayaran:** bunga dihitung saat tidur di malam pergantian musim (tanggal 28 → tanggal 1), masuk ke saldo tabungan, dicatat di buku bank, dan diumumkan lewat surat keesokan paginya ("Bunga tabunganmu musim ini: +Xg"). Tidak ada bunga di musim pertama bila rekening baru dibuka di tengah musim — bunga dihitung proporsional dari hari sejak rekening dibuka (rata-rata harian otomatis menangani ini).

### 2.5 Pinjaman — ✅ REKOMENDASI

**Satu pinjaman aktif dalam satu waktu. Limit bertingkat dari total penghasilan seumur hidup. Bunga 5% per musim atas sisa pokok. Cicilan otomatis 10% dari pokok awal setiap malam (dari dompet dulu, lalu tabungan). Biaya layanan 2% dari jumlah pinjaman, dipotong di depan. Pelunasan dini kapan saja tanpa penalti.**

**Limit bertingkat (draf — Ren finalisasi angkanya):**

| Tingkat | Syarat (total penghasilan seumur hidup) | Limit pinjaman |
|---|---|---|
| 1 — "Nasabah Baru" | 0g (terbuka sejak rekening dibuat) | 10.000g |
| 2 — "Petani Berkembang" | 100.000g | 50.000g |
| 3 — "Juragan" | 500.000g | 200.000g |

Alasan limit bertingkat: pemain baru tidak bisa langsung utang besar lalu kabur dari konsekuensi; limit naik seiring pemain membuktikan bisa menghasilkan uang. (Pola ATM mod: "credit line adjusts" — kita buat eksplisit dan terlihat di UI.)

**Bunga pinjaman 5% per musim** — lima kali lipat bunga tabungan (1%). Selisih ini (spread) adalah "keuntungan bank" dan sekaligus harga yang wajar untuk uang cepat. Contoh: pinjam 50.000g → bunga 2.500g/musim, ditambahkan ke sisa pinjaman di tiap pergantian musim.

**Cicilan otomatis:** setiap malam, sistem memotong **10% dari pokok awal** pinjaman (bukan dari sisa — supaya lunas dalam 10 malam). Urutan potong: dompet dulu, kekurangan diambil dari tabungan. Contoh: pinjam 50.000g → cicilan 5.000g/malam, lunas dalam 10 malam (selama uang cukup).

**Kalau uang tidak cukup untuk cicilan (tunggakan):**
- Cicilan malam itu **ditunda** (tidak memotong paksa sampai dompet negatif — dompet tidak boleh negatif).
- **Denda keterlambatan: 500g flat** per malam cicilan yang terlewat, ditambahkan ke sisa pinjaman.
- Surat peringatan keesokan paginya ("Cicilanmu terlewat...") — nada ramah, bukan mengancam.
- Selama masih punya tunggakan: **tidak bisa mengambil pinjaman baru**, dan **bunga tabungan dihitung dari saldo bersih** (lihat 2.11).
- Tidak ada debt collector yang menyita barang, tidak ada game over karena utang. Ini game cozy — utang itu beban, bukan hukuman mati. (Ferngill Hard Mode yang melarang setor selama berutang dicatat sebagai opsi "mode sulit", bukan default.)

**Pelunasan dini:** tombol "Lunasi Sekarang" di menu Pinjaman — bayar seluruh sisa pokok + bunga berjalan, tanpa penalti. Pemain yang rajin bisa bebas utang lebih cepat.

**Biaya layanan 2%:** dipotong dari jumlah pinjaman saat pencairan (pinjam 50.000g → terima 49.000g, utang tercatat 50.000g). Angka ini draf; Ferngill menyebut "service fee's apply" tapi persentasenya ⚠️ belum terverifikasi — jadi 2% adalah angka kita sendiri, Ren boleh ubah ke 0% (gratis) atau 5%.

**Alur pinjam (UX):** pilih jumlah (tombol cepat 5.000 / 10.000 / limit) → layar konfirmasi menampilkan jumlah diterima, cicilan/malam, bunga/musim, total yang harus dibayar → konfirmasi → uang masuk dompet + surat perjanjian (untuk flavor + jejak di buku bank).

### 2.6 Auto-deposit (setor otomatis) — ✅ REKOMENDASI

- **Slider 0–100% (kelipatan 10%)** di menu ATM, default **0% (mati)**. Berlaku untuk penghasilan **shipping bin tiap malam** (pola RBTS).
- **Cara kerja:** saat penghasilan shipping dihitung di malam hari, persentase yang dipilih langsung masuk Tabungan; sisanya masuk Dompet. Ringkasan pagi menampilkan pembagiannya ("Hasil panen 12.400g → Dompet 11.160g, Tabungan 1.240g" untuk 10%).
- **Kenapa default mati:** pemain baru tidak boleh kaget uangnya "hilang" ke bank. Auto-deposit adalah fitur untuk pemain yang sudah paham — mereka yang mengaktifkannya sendiri.
- **Bisa diubah kapan saja** di ATM, tanpa biaya. Perubahan berlaku mulai malam itu juga.
- **Tidak berlaku** untuk penjualan langsung ke toko (uang tunai tetap tunai) dan hadiah quest (masuk dompet).

### 2.7 Integrasi dengan shipping bin

- **Jangan ubah shipping bin.** Uang panen tetap masuk **dompet** dulu (aturan vanilla Stardew: bin bayar besok pagi). Auto-deposit hanya membagi aliran itu.
- **Ringkasan pagi** (layar setelah tidur) menampilkan tiga baris bila relevan: hasil shipping, pembagian auto-deposit, dan (di tanggal 1) bunga tabungan + cicilan pinjaman. Satu layar, semua jejak uang terlihat.
- **Catatan untuk Ren:** kalau game kita nanti punya jalur jual lain (pasar mingguan ala Grand Bazaar, lapak sendiri ala A Wonderful Life), tentukan per jalur apakah hasilnya kena auto-deposit atau tidak — rekomendasi robo: hanya shipping bin yang kena auto-deposit, jalur lain tunai.

### 2.8 Pajak — ❌ OFF di v1 (opsional, fase 2)

RBTS membuktikan sistem pajak bisa jalan (pajak penghasilan + modal + properti, ditagih tiap tanggal 1, tak dibayar → utang + denda mingguan + potong shipping). Tapi untuk game baru, pajak menambah beban kognitif dan rasa "dihukum" di saat pemain masih belajar bertani.

**Rekomendasi:** pajak **tidak ada di v1**. Kalau nanti mau ada (fase 2, setelah ekonomi stabil dan butuh money sink), adopsi model RBTS yang disederhanakan:
- Tarif tunggal: **2% dari penghasilan shipping musim lalu**, ditagih tanggal 1.
- Bayar manual di ATM atau otomatis dari tabungan.
- Tak dibayar → jadi utang dengan denda 500g/minggu (bukan blacklist, bukan sita).
- Keputusan ada/tidaknya pajak = **keputusan Adi + Ren**, bukan robo.

### 2.9 Buku bank (riwayat transaksi)

- **Tab "Buku Tabungan"** di dalam UI ATM (pola Pelican: tombol B membuka bankbook).
- Isi per baris: **tanggal (Musim, Hari, Tahun) — jenis — jumlah (+/−) — saldo akhir**.
- Jenis transaksi: Setor, Tarik, Bunga Tabungan, Pinjaman Diterima, Cicilan, Pelunasan, Biaya Layanan, Denda, Auto-Deposit, Pajak (fase 2), Event Bank.
- **Simpan 50 entri terakhir** (Pelican menyimpan riwayat detail; Simple Bank menyimpan 100 donasi — 50 cukup untuk v1, hemat save file).
- Tampilkan juga di header buku: **nomor rekening, nama pemilik, total bunga yang pernah diterima, total pinjaman yang pernah diambil** (statistik flavor yang bikin pemain bangga).
- Saldo di buku bank **selalu sama** dengan saldo di UI ATM — satu sumber data, dua tampilan.

### 2.10 Event acak bank (flavor, jinak)

Ferngill membuktikan event acak bikin bank terasa hidup (peluang ~10%/malam). Tapi event **"kena hack −75% saldo"** adalah contoh yang **jangan ditiru** — menghukum pemain yang sudah rajin menabung, tanpa cara mencegah.

**Rekomendasi v1 — event ON dengan tabel jinak, peluang 5% tiap malam** (lebih jarang dari Ferngill supaya tidak mengganggu):

| Event | Efek | Syarat |
|---|---|---|
| Pasar naik | +2% dari saldo tabungan (maks +5.000g) | Saldo > 0 |
| Apresiasi nasabah | Hadiah acak kecil (benih / +500g) | Selalu bisa |
| Pasar turun | −2% dari saldo tabungan (maks −5.000g) | Saldo > 10.000g |
| Utang dihapuskan (pengampunan) | Sisa pinjaman < 5.000g dihapus | Hanya bila berutang kecil |

- Event diumumkan lewat **surat keesokan paginya** (bukan popup tengah malam) — pola RBTS: "no immersion-breaking popups".
- ❌ **Tidak ada event hack / kehilangan besar.** Kalau Ren mau unsur risiko, ganti dengan "biaya perbaikan ATM" kecil (mis. −200g, sekali-sekali) yang diumumkan NPC teller dengan dialog lucu.
- Event **tidak pernah** membuat saldo negatif atau menambah utang di luar denda yang sudah dijelaskan.

### 2.11 Anti-exploit (wajib dibaca robo + Ren)

| Celah | Cara menutupnya | Status |
|---|---|---|
| **Setor di hari terakhir musim** lalu klaim bunga penuh | Bunga dari **rata-rata saldo harian**, bukan saldo akhir (pola RBTS). Setor 1 jt di hari ke-28 hanya menyumbang 1/28 dari rata-rata. | ✅ Wajib v1 |
| **Bunga tak terbatas** (saldo raksasa → bunga raksasa) | Cap saldo berbunga **1.000.000g** → bunga maks 10.000g/musim (pola Pelican). | ✅ Wajib v1 |
| **Arbitrase pinjam → deposit** (pinjam murah, deposit berbunga) | Bunga tabungan dihitung dari **saldo bersih = saldo − sisa pinjaman**. Berutang 50.000g dengan saldo 60.000g → bunga hanya dari 10.000g. Dan bunga pinjam (5%) > bunga tabungan (1%) — arbitrase selalu rugi. | ✅ Wajib v1 |
| **Tidur berulang untuk farm bunga** (tidur 28 hari tanpa bertani) | (a) Bunga 1%/musim terlalu kecil untuk itu: 100.000g × 28 kali tidur = 1.000g — satu hari bertani aktif menghasilkan berkali lipat. (b) Opsi tambahan untuk Ren: **syarat rekening aktif** — bunga musim ini hanya dibayar bila pemain mengirim ≥1 barang via shipping di musim itu. Tidur tanpa bertani = tidak ada bunga. | (a) ✅ bawaan; (b) opsi Ren |
| **Setor/tarik spam** | Transfer zero-sum — tidak ada celah, tidak perlu dibatasi. | ✅ Bawaan |
| **Pinjam → tarik → kabur** (tidak pernah bayar) | Cicilan otomatis memotong dari dompet + tabungan tiap malam; denda menumpuk; tidak bisa pinjam lagi sampai lunas. Utang tidak bisa dihapus dengan cara apa pun kecuali dibayar atau event pengampunan kecil. | ✅ Wajib v1 |
| **Save-scumming event/bunga** | RNG event memakai **seed dari (tahun, musim, hari)** — reload hari yang sama memberi hasil yang sama. Bunga memakai angka pasti (bukan RNG), jadi tidak ada yang di-scum. | ✅ Wajib v1 |
| **Auto-deposit 100% lalu kelaparan uang tunai** | Itu pilihan pemain, bukan exploit — tapi UI wajib menampilkan peringatan saat slider 100%: "Semua hasil panen masuk bank. Kamu butuh uang tunai? Turunkan dulu." | ✅ Wajib v1 |

### 2.12 Urutan penyelesaian tiap malam (untuk robo — urutan ini penting)

Saat pemain tidur, sistem menyelesaikan uang dalam urutan berikut (jangan dibalik):

1. **Hitung penghasilan shipping** hari itu.
2. **Bagi penghasilan** sesuai auto-deposit % → Dompet + Tabungan. Catat di buku bank.
3. **Potong cicilan pinjaman** (10% pokok awal) dari Dompet, kekurangan dari Tabungan. Kalau tidak cukup → tunggakan + denda 500g.
4. **Catat saldo akhir hari** untuk rata-rata saldo musim ini.
5. **Jika malam pergantian musim (hari 28 → hari 1):** hitung bunga tabungan (rata-rata × 1%, cap) → masuk Tabungan; hitung bunga pinjaman (sisa pokok × 5%) → tambah ke sisa pinjaman; roll event bank (seed harian); siapkan surat-surat pagi (bunga, cicilan terlewat, event).
6. **Tulis semua ke buku bank**, lalu save.

### 2.13 Model data (Godot — untuk robo)

```gdscript
# BankAccount — disimpan di dalam save file pemain (bukan file terpisah)
class_name BankAccount
extends Resource

var is_open: bool = false
var account_number: String = ""        # mis. "BTM-0142-8831"
var balance: int = 0                    # saldo tabungan
var loan_principal: int = 0             # sisa pokok pinjaman (0 = tidak berutang)
var loan_original: int = 0              # pokok awal (untuk cicilan 10%/malam)
var loan_late_fees: int = 0             # denda yang menumpuk (digabung ke pokok saat dihitung)
var auto_deposit_pct: int = 0           # 0–100, kelipatan 10
var daily_balance_sum: int = 0          # akumulasi saldo harian musim ini
var daily_balance_days: int = 0         # jumlah hari tercatat musim ini
var lifetime_earned: int = 0            # total penghasilan seumur hidup (untuk tingkat limit)
var total_interest_earned: int = 0      # statistik flavor
var total_borrowed: int = 0             # statistik flavor
var ledger: Array = []                  # Array[Dictionary], maks 50 entri
# Entri ledger: {"day": "Musim 12 Thn 1", "type": "SETOR", "amount": 1000, "balance": 51000}
```

**Konstanta balancing (satu tempat, gampang diubah Ren/robo):**
```gdscript
const INTEREST_RATE_PER_SEASON := 0.01        # 1%
const INTEREST_CAP_BALANCE := 1_000_000       # cap saldo berbunga
const LOAN_INTEREST_PER_SEASON := 0.05        # 5%
const LOAN_INSTALLMENT_PCT := 0.10            # 10% pokok awal / malam
const LOAN_SERVICE_FEE := 0.02                # 2% di depan
const LOAN_LATE_FEE := 500                    # denda per malam terlewat
const LOAN_LIMITS := [10_000, 50_000, 200_000]
const LOAN_LIMIT_EARNED := [0, 100_000, 500_000]
const LEDGER_MAX := 50
```

### 2.14 Edge cases (jangan sampai crash / bingung)

- Pemain membuka rekening di tengah musim → bunga musim itu dihitung dari hari pembukaan (rata-rata harian otomatis menangani).
- Pemain menarik semua tabungan di hari ke-27 → rata-rata tetap menghitung hari-hari sebelumnya (adil).
- Pemain berutang lalu saldo tabungan 0 dan dompet 0 → cicilan tertunda, denda menumpuk 500g/malam; pemain tetap bisa bertani dan menjual untuk membayar.
- Pemain melunasi pinjaman di hari yang sama dengan cicilan otomatis → cicilan malam itu dilewati (cek `loan_principal == 0` dulu).
- Save/load di tengah musim → `daily_balance_sum` dan `daily_balance_days` ikut tersimpan (jangan di-reset).
- Angka negatif tidak pernah tampil: semua tampilan memakai `max(0, ...)` dan format ribuan Indonesia (1.000.000).

---

## 3. UX/UI — alur interaksi & layar (bagian Blue)

### 3.1 Alur interaksi di mesin ATM

1. Pemain mendekati ATM di alun-alun → prompt "Tekan E — ATM Bank Tani Makmur".
2. Layar ATM terbuka (fullscreen, game dijeda — atau tetap jalan? **Rekomendasi: jeda**, supaya pemain tidak dikejar jam saat menghitung uang).
3. Kalau belum punya rekening → layar sambutan + tombol "Buka Rekening (Gratis)" → animasi kartu keluar + nomor rekening → masuk menu utama.
4. Menu utama (lihat 3.2). Tiap aksi memberi umpan balik suara + animasi (koin, struk keluar, bunyi bip).
5. Tutup dengan Esc / tombol "Selesai" / tombol X — kembali ke dunia, jam lanjut.

### 3.2 Layar UI (draf wireframe teks — Blue yang finalisasi visualnya)

```
┌─────────────────────────────────────────────────┐
│  BANK TANI MAKMUR            Musim Semi, Hari 12 │
│  Rekening: BTM-0142-8831                         │
├─────────────────────────────────────────────────┤
│  DOMPET: 12.400g        TABUNGAN: 58.000g       │
│  Pinjaman: 0g  •  Bunga musim ini: +580g (est.) │
├─────────────────────────────────────────────────┤
│  [Setor] [Tarik] [Pinjam] [Buku Tabungan]       │
│  [Setor Otomatis: 10%]                          │
├─────────────────────────────────────────────────┤
│  ┌─ SETOR ───────────────────────────────────┐  │
│  │  Jumlah: [  5.000g        ]               │  │
│  │  [100] [1.000] [10.000] [Semua]           │  │
│  │  [  1  2  3 ]  numpad layar sentuh         │  │
│  │  [  4  5  6 ]                              │  │
│  │  [  7  8  9 ]  [0] [Hapus]                 │  │
│  │  [✓ Setor 5.000g]   [Batal]               │  │
│  └───────────────────────────────────────────┘  │
│  Saldo sesudah setor: Dompet 7.400g • Tab. 63.000g│
└─────────────────────────────────────────────────┘
```

**Spesifikasi UI (untuk Blue):**
- **Dua angka selalu terlihat:** Dompet dan Tabungan, di header setiap tab — pemain tidak boleh "lupa" berapa uangnya.
- **Pratinjau sebelum konfirmasi:** setiap aksi menampilkan "saldo sesudah" sebelum tombol ✓ ditekan (pola Simple Bank: "review projected balances, then confirm").
- **Tombol cepat + ketik manual + numpad sentuh.** Game kita kemungkinan besar dimainkan di HP (seperti game Godot Adi yang lain) — numpad layar sentuh wajib, bukan opsional.
- **Warna:** masuk = hijau, keluar = merah/oranye, bunga = emas/kuning, utang = merah. Konsisten di semua layar.
- **Format angka Indonesia:** pemisah ribuan titik (12.400g), bukan koma.
- **Buku Tabungan:** daftar scroll, 50 entri, filter per jenis (Semua / Setor / Tarik / Bunga / Pinjaman), tiap baris: tanggal — jenis — jumlah — saldo.
- **Menu Pinjam:** tampilkan limit saat ini + tingkat berikutnya ("Pinjam 50.000g lagi setelah total penghasilanmu 100.000g"), cicilan/malam, bunga/musim, dan layar konfirmasi lengkap sebelum uang cair.
- **Slider Setor Otomatis:** 0%–100% kelipatan 10%, dengan contoh hitungan langsung ("Hasil panen 10.000g → 1.000g ke bank").
- **Suara:** bip tombol, bunyi koin saat setor, bunyi mesin saat tarik, nada error saat saldo tidak cukup. (Blue: daftar kebutuhan suara untuk robo.)
- **Animasi ATM (dunia):** layar ATM menyala (glow) di malam hari, lampu berkedip saat tidak dipakai, struk kertas keluar saat transaksi besar (flavor visual).
- **Aksesibilitas:** tombol besar (min. 48px area sentuh), teks minimal 16px, kontras tinggi, tidak ada teks penting yang hanya mengandalkan warna.
- **Aset yang dibutuhkan dari Blue:** sprite mesin ATM (siang/malam + varian ladang), ikon koin/dompet/buku/pinjaman, mockup 5 layar (utama, setor, tarik, pinjam, buku), suara (bip, koin, error, struk).

---

## 4. Pembagian kerja trio

| Orang | Tugas | Catatan |
|---|---|---|
| **Ren** — desain sistem + naskah | 1) Finalisasi angka: bunga 1% (atau 2%?), limit pinjaman, denda, biaya layanan. 2) Putuskan: ATM ladang craftable v1 atau fase 2? Pajak on/off? Syarat rekening aktif on/off? Event bank on/off? 3) Tulis naskah: surat pembukaan rekening, surat bunga tiap musim, surat peringatan tunggakan, dialog NPC teller (kalau ada), dialog lelucon bank ("Bunganya kecil, tapi yang penting nabung, ya?"). 4) Skenario balancing: mainkan angka 2.4–2.5 dengan simulasi musim (berapa bunga yang didapat pemain hemat vs pemain boros). 5) Daftar edge case dari sisi pemain (apa yang membingungkan?). | Spek ini draf untuk Ren — Ren yang menyempurnakan, bukan robo yang memaksakan angka. |
| **Blue** — aset visual + testing | 1) Sprite mesin ATM (kota + varian ladang), animasi layar + glow malam. 2) Mockup/final UI 5 layar (3.2) + ikon-ikon. 3) Daftar suara ATM. 4) Testing: cek keterbacaan angka di HP kecil, tombol numpad gampang ditekan, warna tidak membingungkan. | Utamakan keterbacaan di layar HP (Redmi Note 11 Adi = patokan). |
| **robo** — implementasi Godot | 1) `BankAccount` resource + save/load (2.13). 2) Interactable ATM + UI scene (berdasarkan mockup Blue). 3) Settlement malam hari sesuai urutan 2.12 + surat pagi. 4) Unit test: hitung bunga (rata-rata + cap), cicilan, denda, auto-deposit, anti-exploit (kasus 2.11). 5) Integrasi ringkasan pagi + shipping bin. | Mulai setelah Ren mengunci angka + Blue mengunci mockup. Jangan implementasi angka yang belum dikunci. |

**Urutan kerja:** Ren kunci angka (1–2 hari) → Blue mockup + aset (paralel) → robo implementasi → bertiga playtest ekonomi 1 musim penuh (cek: bunga terasa? pinjaman membantu atau menjebak?).

---

## 5. Pertanyaan terbuka (untuk Ren + Adi)

1. Nama bank final: "Bank Tani Makmur" atau nama lain? (Ren usul, Adi putuskan.)
2. Mata uang: tetap `g` ala Stardew, atau `Rp` / "Koin" khas Indonesia?
3. Bunga 1% atau 2% per musim?
4. ATM ladang (craftable) masuk v1 atau fase 2?
5. Syarat "rekening aktif" (harus kirim ≥1 barang/musim untuk dapat bunga) — pakai atau tidak?
6. Pajak: tetap OFF, atau mau dicoba di fase 2?
7. NPC teller bank: ada (butuh dialog + sprite) atau ATM saja cukup?
8. Event bank: pakai tabel jinak 2.10, atau matikan total di v1?

---

## 6. Sumber (dicek 24 Nov 2026)

**Stardew Valley vanilla:**
- Stardew Valley Wiki — *Shipping*: https://stardewvalleywiki.com/mediawiki/index.php?title=Shipping&oldid=144033
- Stardew Valley Wiki — *Gold* (via mirror eso-ui): https://wiki.eso-ui.com/index.php/Stardew_Valley:Gold

**Mod bank/ATM (Nexus Mods):**
- ATM (Platonymous, #3135, v1.1.2, 2018): https://www.nexusmods.com/stardewvalley/mods/3135?tab=posts
- Bank of Ferngill (Mizzion, #13043): https://www.nexusmods.com/stardewvalley/mods/13043/
- Pelican Valley Bank (SourceNine Labs, #50878, v0.1.0): https://www.nexusmods.com/stardewvalley/mods/50878
- Simple Bank and Charity (#54817): https://www.nexusmods.com/stardewvalley/mods/54817
- RBTS — Regional Banking & Taxes System (Losgar38, #41480, v2.0.1, Jan 2026): https://www.nexusmods.com/stardewvalley/mods/41480
- Stardew ATM (craftable, #43744): https://www.nexusmods.com/stardewvalley/mods/43744

**Harvest Moon / Story of Seasons:**
- Story of Seasons — ulasan Trade Depot: http://elder-geek.com/2015/04/story-of-seasons-review/
- Story of Seasons: A Wonderful Life — manual resmi (shipping bin + deposit box): https://www.storyofseasons.com/awl/manuals/switch/15/
- Story of Seasons: Grand Bazaar — ulasan pasar Sabtu: https://checkpointgaming.net/reviews/2025/08/story-of-seasons-grand-bazaar-review-now-thats-what-i-call-a-farmers-market/
- Story of Seasons: Friends of Mineral Town — ulasan shipping bin: https://3rd-strike.com/story-of-seasons-friends-of-mineral-town-review/

**⚠️ Yang belum terverifikasi (jangan jadikan angka final sebelum dicek):**
- Rate bunga & limit kredit default mod ATM (tidak tertulis di halaman Nexus — perlu baca config default/source code).
- Rate bunga simpanan & bunga pinjaman default Bank of Ferngill (halaman hanya menyebut nama setting: BaseBankingInterest, LoanBaseInterest).
- Rate bunga per bank + tarif pajak RBTS (tidak dipublikasikan di halaman Nexus — bisa diatur via GMCM).
- Angka modal awal Stardew 500g (umum dikutip; cuplikan wiki yang diambil tidak menampilkan angkanya eksplisit).
- Status update 1.6 mod ATM yang dijanjikan penulis Des 2023 (belum dicek apakah rilis).
