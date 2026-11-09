# Progress Mockup UI — SIMRS

Tanggal: 26 Oktober 2026 · File: `design/mockup.html` (single-file HTML+CSS+JS vanilla)

## Arah Desain (disetujui user)

- Modern, responsive (mobile / tablet / PC), tema RS, animasi minimalis tapi hidup
- Home ala **launcher Android** — grid ikon menu, bukan sidebar; **Tren Penyakit** ringkas di bawah Menu Utama (3 ICD naik → RME)
- **RME** — tab Pasien & Timeline + **Tren Penyakit** (filter naik/lonjakan/turun, spark 8 minggu, chip obat → Farmasi Prediksi Stok)
- **Tanpa login** untuk prototype
- Palet: teal `#0d9488` + biru `#2563eb`, glassmorphism transparan

## Yang Sudah Dikerjakan

### 1. Layar Home (launcher)

- **EKG banner** — garis denyut nadi full-width di atas search bar. Pola P-QRS-T realistis: 7 beat amplitudo berbeda + 1 kompleks PVC menyimpang + gelombang T bervariasi + baseline bergeser — tidak monoton. Animasi dash mengalir kiri→kanan 3.4 detik.
- **Global search** — input "Cari pasien (no. RM / NIK / nama), menu, obat…". Ketik ≥2 karakter → panel hasil mendorong launcher ke bawah (bukan overlay), hasil tergrup Menu / Pasien / Obat dengan ikon berwarna, max 5 per grup + "+N lainnya — persempit kata kunci", scroll internal 320px, debounce 220ms, Enter = hasil pertama, Esc/klik luar = tutup. Klik pasien → langsung layar RME. Data statis: 12 menu, 9 pasien, 7 obat.
- **Menu panel** — panel **kaca bening murni** (bg 2–5%, tanpa border, shadow teal halus) berisi judul "MENU UTAMA" + grid ikon. Efek: sheen kilau menyapu tiap 8 detik, **staggered entrance** (ikon muncul satu per satu delay 40ms saat home dibuka), efek tap scale .88, hover naik + shadow membesar.
- **Pill-row** — Pasien aktif 24 · Antrian 12 · Okupansi ranap 68%. Transparan senada panel, adaptif clamp(.58rem, 2.4vw, .72rem) di mobile, tidak pernah terpotong.
- **Background** — aurora blob (3 blob blur melayang) + **12 ikon medis melayang** (silaket, hati, kapsul vertikal/horizontal, garis denyut, termometer, tas P3K, tabung reagen; warna teal/biru/mint/rose/ungu; animasi drift 2 arah delay berbeda). Terlihat melintas di belakang panel kaca.
- **Topbar** — logo SIMRS **PT. Rizia Mitra Sejati** (float lembut) + jam real-time Indonesia di kanan.

### 2. Menu launcher — 12 item

Dashboard · Pendaftaran · Antrian Poli (badge 12) · Pelayanan Poli (badge 3) · Rawat Inap · Farmasi · Kasir · Rekam Medis · Jadwal Dokter · Master Data · **IGD** (badge 2) · **Bridging BPJS** — dua terakhir ditambahkan dari referensi menu Khanza.

### 3. Layar lain

- **Dashboard (POLISHED)** — detail: [`LAPORAN_PROGRESS.md`](LAPORAN_PROGRESS.md) §2
- **Pendaftaran (POLISHED)** — detail + simulasi NIK: [`LAPORAN_PROGRESS.md`](LAPORAN_PROGRESS.md) §3
- **Antrian Poli (POLISHED)** — konsol antrean 2 kolom + koneksi CPPT: [`LAPORAN_PROGRESS.md`](LAPORAN_PROGRESS.md) §4
- **Pelayanan Poli** — daftar dinamis dari state antrean + form CPPT/SOAP + chips tindakan; **Rujuk Rawat Inap** → antrian admit
- **Farmasi (POLISHED v2 + Prediksi)** — Antrean & Dispensing (Antri→Verifikasi→Racik→Serah) + filter ralan/ranap + KPI + **Stok Obat** (saldo/kritis/kartu stok mutasi) + **Prediksi Stok** (ETA/reorder/risiko, estimasi simulasi) + **Penerimaan** barang; serah mengurangi stok → Kasir
- **Kasir** — antrean tagihan & pembayaran (Umum/BPJS/Asuransi → Lunas/Piutang)
- **Rawat Inap (POLISHED v1)** — Command Center: bed board (Melati/Mawar/VIP/ICU) + KPI BOR/kosong/LOS + kartu bed (alergi/isolasi/risiko/hari rawat) + antrian admit + rencana pulang (kanban) + visite CPPT singkat + checklist pulang → tagihan kamar ke Kasir
- **RME** — timeline riwayat kunjungan pasien
- **Placeholder generik** — Jadwal Dokter, Master Data, IGD, Bridging BPJS

### 4. Responsif

- **Mobile ≤640px** — menu 4 kolom (sudah disukai user — JANGAN disentuh), EKG 44px, ikon artefak dikurangi jadi 8, pill swipe-able, `text-size-adjust:100%` anti font-boosting Android
- **HP kecil ≤380px** — menu 3 kolom
- **Tablet 641–1023px** — menu 4 kolom, ikon 84px
- **Desktop ≥1024px** — menu 6 kolom × 2 baris, ikon 110px, panel padding lebar

### 5. Yang Dihapus / Ditolak user

- ~~Card hero "Selamat datang"~~ — dihapus
- ~~Dock navigasi bawah~~ — dihapus total (redundan dengan tombol back)
- ~~Orbit solar~~ — diganti ikon medis melayang
- ~~Backdrop dim saat search~~ — diganti panel push-down
- ~~Fitur review/send-element buatan~~ — jangan buat lagi

## Cara Preview

- Lokal: `http://localhost:8080` (server Python sudah jalan)
- HP asli (WiFi sama): `http://192.168.1.7:8080` — IP bisa berubah, cek `ipconfig`
- Kalau server mati: `python -m http.server 8080` di folder `design/`
- Simulasi device: F12 → Ctrl+Shift+M di Chrome/Edge

## Belum Dikerjakan

- Polish visual layar RME
- Transfer bangsal/kelas di Ranap (opsional demo)
- Layar nyata: IGD, Bridging BPJS, Jadwal Dokter, Master Data
- Detail obat hasil pencarian (sementara ke placeholder generik)
- Layar hasil pencarian lengkap (Enter → halaman penuh)
- Integrasi backend nyata — badge SEP/SatuSehat saat ini **hanya simulasi mockup**, bukan bukti koneksi VClaim/Antrol/SatuSehat
