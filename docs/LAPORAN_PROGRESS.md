# LAPORAN PROGRESS — Mockup SIMRS

Tanggal: 26 Oktober 2026 · File utama: `design/mockup.html` (single-file HTML+CSS+JS vanilla)
Client: **PT. Rizia Mitra Sejati** · Status prototype tanpa login & tanpa backend

---

## 1. Home (Launcher)

**Status: FINAL — jangan disentuh**

- **EKG banner** — garis denyut nadi full-width di atas search bar; pola P-QRS-T realistis (7 beat amplitudo bervariasi + 1 PVC menyimpang + gelombang T bervariasi + baseline bergeser); animasi dash 3.4 detik
- **Global search** — hasil push-down (bukan overlay), tergrup Menu/Pasien/Obat, max 5/grup, Enter → hasil pertama, Esc/klik luar tutup; klik pasien → layar RME
- **Panel menu kaca bening** — bg 2–5%, tanpa border, shadow teal; **sheen kilau hanya di home** (sweep tiap 8 detik); staggered entrance 40ms/ikon; tap scale .88
- **12 menu**: Dashboard, Pendaftaran, Antrian Poli (badge 12), Pelayanan Poli (badge 3), Rawat Inap, Farmasi, Kasir, Rekam Medis, Jadwal Dokter, Master Data, IGD (badge 2), Bridging BPJS
- **Background** — aurora blob + 12 ikon medis melayang (8 di mobile), drift 2 arah
- **Responsif menu** — mobile ≤640px 4 kolom · HP kecil ≤380px 3 kolom · tablet 4 kolom ikon 84px · desktop 6 kolom ikon 110px
- Topbar: logo SIMRS "PT. Rizia Mitra Sejati" + jam real-time

---

## 2. Dashboard Monitoring

**Status: POLISHED (disetujui user)** · Layar `#screen-dashboard`, layout `.app.wide` full-width

### KPI Cards (4)
Kunjungan hari ini, Antrian aktif, Pasien rawat inap, Resep masuk — satuan kecil + delta ▲▼ hijau/merah.
*User menolak: pendapatan harian, BOR/okupansi, alert tunggu >30 menit.*

### Alert Row (klik-able → modal)
- **Stok obat menipis** → popup tabel obat + sisa stok + status
- **Bridging BPJS** → buka layar Bridging BPJS (cek peserta / SEP / Antrol / klaim) *(simulasi, bukan VClaim nyata)*
- Modal reusable `openModal`/`modalData`; tutup via ✕ / klik overlay / Esc

### Line Chart — `lineChart()` (reusable, SVG murni)
- Draw-in animation + glow garis + titik pop-in berurutan
- Target line marching-ants sebagai pembanding
- Tooltip + crosshair mengikuti kursor (hari + angka kunjungan)
- viewBox adaptif: 400px mobile / 720px desktop

### Donut Chart Penjamin — `donutChart()`
- Segmen BPJS/Umum/Asuransi — hover segmen menebal keluar
- Label tengah: nama singkat + persen segmen aktif
- Legenda rapat di samping — mobile tetap di samping (donut 130px / desktop 190px)

### Panel pendukung
- Tabel Kunjungan per Poli · Antrian Live (badge Menunggu/Diperiksa/Selesai) · Top 5 Diagnosa ICD-10 (rank badge + sparkline) · Dokter Jaga (poli + jam shift)

### Presisi
Kolom flex-column + `.glass flex:1` → panel tinggi sejajar; `.glass` kaca bening tanpa sheen

---

## 3. Pendaftaran (Admisi)

**Status: POLISHED** · Layar `#screen-pendaftaran` · Alur: identifikasi pasien → admisi → antrian → bayar → kwitansi

### 3.1 Mode Ganda (toggle segmen atas)

**Pasien Lama**
- Kolom Cek Pasien (No. RM / NIK / nama, ≥3 huruf) → klik **PILIH**
- Kartu terpilih: nama + badge RM + NIK · JK · umur · HP + **kunjungan terakhir**
- Biodata disembunyikan (tidak isi ulang identitas)
- Submit tanpa pilih pasien → diblokir + petunjuk

**Pasien Baru**
- Identitas: No. RM auto (`RM-0009++`), NIK*, Nama*, Tgl Lahir*, Umur auto, JK, Agama, Status Perkawinan, Pekerjaan
- Alamat administratif: jalan RT/RW → provinsi + kode pos + No. HP
- Kontak darurat: nama keluarga + hubungan + No. HP
- General consent (persetujuan pelayanan + pengolahan data)

### 3.2 Simulasi KTP / NIK

**NIK 16 digit diparse otomatis** — posisi 7–12 = `ddmmyy`; hari >40 = perempuan (dd−40); umur dihitung otomatis.

Data uji `patientsDB` (untuk cek duplikat & pasien lama):

| NIK | Nama | Tgl Lahir | JK | Penjamin | Kunjungan Terakhir |
|---|---|---|---|---|---|
| 3201014509870001 | Budi Hartono | 14-09-1987 | L | Umum | 12 Okt 2026 · Poli Umum |
| 3201015503920002 | Sari Dewanti | 15-03-1992 | P | BPJS | 28 Sep 2026 · Poli Kandungan |
| 3201016205750004 | Rina Marlina | 06-05-1975 | P | BPJS | 15 Okt 2026 · Poli Umum |

**Cara uji:**
- Ketik salah satu NIK di atas di mode Pasien Baru → tgl lahir/JK/umur auto-isi + **peringatan oranye "NIK sudah terdaftar — gunakan Pasien Lama"**; klik tautannya → auto pindah mode + pasien terpilih
- Submit dengan NIK duplikat → diblokir (anti double-RM)
- NIK baru (di luar DB) → normal, tgl lahir/JK tetap terisi dari pola digit

### 3.3 Validasi
- Field wajib `*` merah: Nama, NIK (16 digit penuh), Tgl Lahir
- Submit kosong → border merah + "⚠ … wajib diisi lengkap"; hilang saat diketik

### 3.4 Penjamin & Kunjungan
- **Umum** — tanpa kartu · **BPJS** — No. Kartu BPJS + **No. Rujukan FKTP** (untuk SEP) · **Asuransi** — No. Polis
- Poli → dokter menyesuaikan jadwal (Umum 2 dokter, Anak, Gigi, Kandungan)
- Tanggal default hari ini; cara datang (walk-in/rujukan/online); keluhan singkat

### 3.5 Kolom Kanan
- **Tiket antrian** — prefix A/B/C/D per poli + counter naik tiap daftar; dokter; **estimasi dinamis** (jam sekarang + ~7 mnt × orang di depan); barcode strip + kode booking `KJ-2026-XXX`
- Badge SEP ✓ + SatuSehat ✓ *(simulasi tampilan — BUKAN integrasi nyata)*
- **Ringkasan Biaya** — Admin Rp 15.000 + tarif poli (Umum 50rb / Anak 55rb / Gigi 60rb / Kandungan 60rb)

### 3.6 Kasir Simulasi (💳)
- **Tunai** — uang diterima + kembalian auto, tolak kalau kurang
- **QRIS** — QR simulasi, nominal terkunci · **Debit** — konfirmasi gesek
- **BPJS** — otomatis "Ditanggung BPJS — pasien Rp 0" → langsung kwitansi
- Sukses → **kwitansi thermal**: no. KW, item biaya, metode, kembalian, stempel **LUNAS** miring, kasir + jam; tombol jadi `✓ LUNAS` (reset di tiket berikutnya)

### 3.7 Cetak & List
- **🖨 Cetak Antrian** — popup bukti antrian kapan saja
- **Submit → Bukti Registrasi** — popup tiket + tabel (RM, nama, penjamin — BPJS badge "SEP terbit ✓", poli+dokter, tgl) + status SatuSehat + tombol cetak; pasien masuk list + counter naik
- **Baru Saja Terdaftar** — 5 item terlihat pas, scroll mulai item ke-6 (max-height 372px, scrollbar tipis teal)

### 3.8 Revisi UX
- **Header sticky transparan** semua layar — back+judul menempel atas saat scroll, artefak tetap terlihat melintas; tombol back kaca-blur teal (bukan putih), judul glow lembut
- Tombol "Pasien Baru" bawah **dihapus** — redundan dengan toggle
- Responsif: mobile 1 kolom, desktop split-grid 1.6fr/1fr

---

## 4. Antrian Poli

**Status: POLISHED (disetujui user)** · Layar `#screen-antrian` · 2 kolom `.split-grid` (daftar kiri, konsol kanan — sejajar tingginya)

### 4.1 Kolom Kiri — Daftar Antrean
- **Tab poli** — Poli Umum / Anak / Gigi / Kandungan + counter menunggu (chip `hot` bila >0)
- **Filter chips = statistik** — Semua / Menunggu / Proses / Selesai dengan angka live
- **Item antrean** — nomor badge, nama + badge prioritas (👵 Lansia / 🤰 Hamil), penjamin, sumber (MJKN/walk-in + Antrol ✓), jam masuk, badge status (Antri/Dipanggil/Diperiksa/Lewati/Selesai/Farmasi)
- **Klik item** Menunggu/Lewati = panggil langsung · item Dipanggil/Dilayani dapat highlight `panggil`
- **🔁 Panggil** — tombol kecil di item status Lewati → aktifkan kembali pasien yang ter-skip
- **Scroll** — `flex:1 + max-height:470px + overflow-y:auto`, scrollbar tipis teal; `.q-item` wajib `flex:none` (tanpa ini item menyusut & scroll tak pernah muncul)

### 4.2 Kolom Kanan — Konsol Petugas
- **Board gelap** `.aq-board` — header poli+dokter, status (SIAP MEMANGGIL / SEDANG DIPANGGIL / SEDANG DIPERIKSA), nomor besar animasi `pop`, nama+meta, chip "Berikutnya" (3 antrean)
- **Tombol utama kontekstual** — 🔊 Panggil Berikutnya → 🩺 Mulai Periksa → ✔ Selesai → Farmasi
- **Sub-tombol** — ↻ Panggil Ulang (ulang suara, status tetap) · ⏭ Lewati · 📺 Layar
- **Suara** — `speechSynthesis` baca nomor + poli (id-ID, rate .92); simulasi browser, bukan Antrol nyata

### 4.3 State & Aturan (`AQ`)
- `aqDoCall` — nomor baru dipanggil ⇒ `Dipanggil` lama otomatis turun ke `Menunggu` (hanya 1 panggilan aktif)
- `aqNext` — Menunggu dulu, lalu Lewati; `aqCur` — yang Dipanggil/Dilayani
- Seed: Poli Umum 8 item (A-10 s/d A-17; A-16 status Lewati utk demo tombol 🔁), Poli Anak 2, Poli Gigi 2 (C-01 Lewati), Kandungan kosong

### 4.4 Koneksi ke Pelayanan Poli / CPPT
- **Mulai Periksa** → status `Dilayani` + `loadCppt(q)` isi konteks pasien + `show('poli')`
- **Daftar `#poli-qlist`** dirender dari `AQ` yang sama (bukan statik)
- **Simpan CPPT** → status `Farmasi` + modal tawaran kembali ke antrean
- Alur lengkap: Antri → Dipanggil → Dilayani (CPPT) → Farmasi → Selesai

---

## 5. Rawat Inap — Command Center

**Status: POLISHED v1** · Layar `#screen-ranap` · 26 Okt 2026

### KPI
BOR · Bed kosong · Dirawat · Rencana pulang · LOS rata-rata (live dari state bed)

### Tab
1. **Bed Board** — bangsal Melati (K2), Mawar (K1), Anggrek VIP, ICU; filter; kartu bed (hari rawat, alergi, isolasi, risiko jatuh, rencana/siap pulang); bed hold; klik kosong → arahkan admit
2. **Rencana Pulang** — kanban Dirawat → Rencana → Siap pulang
3. **Antrian Admit** — dari seed + rujukan Poli (`Rujuk Rawat Inap`); pilih pasien → klik bed kosong

### Panel pasien
Visite/CPPT singkat · set rencana/siap pulang · checklist pulang (4 item) → tagihan kamar+visite ke Kasir · bed dikosongkan

### Integrasi
Poli rujuk → RI_WAIT · Pulang → KQ · Home pill okupansi & badge menu live

## 6. Farmasi

**Status: POLISHED v2** · `#screen-farmasi` · 26 Okt 2026

### Tab
1. **Antrean & Dispensing** — filter Ralan/Ranap; alur Antri → Verifikasi farmasis (alergi/stok) → Racik (cek item) → Serah; etiket + panggil; stok keluar + tagihan Kasir
2. **Stok Obat** — saldo, min, status kritis/habis, kartu stok (mutasi IN/OUT)
3. **Penerimaan** — obat + qty + faktur + supplier → stok naik + histori

### Seed
Paracetamol/Amoxicillin/ORS kritis; resep F-01 Verif, F-02 Antri ralan, F-03 Antri ranap

## 7. Kasir

**Status: POLISHED v2** · `#screen-kasir` · 26 Okt 2026

- KPI: Menunggu · Lunas (+pendapatan tunai) · Piutang (+nilai klaim) · Total
- Filter: Semua / Menunggu / Umum / BPJS / Asuransi / Lunas / Piutang
- Umum: metode **Tunai / QRIS / Debit** → Lunas + kuitansi
- BPJS/Asuransi: **Catat Piutang** (pasien Rp 0) — bukti klaim `belum_klaim` simulasi
- Label sumber: Farmasi / Poli / Ranap

## 8. Rekam Medis (RME)

**Status: POLISHED v1** · `#screen-rme` · 27 Okt 2026

- Cari pasien RM / NIK / nama (+ shortcut demo Rina/Budi/Sari)
- Header: identitas, alergi, penjamin, kunjungan aktif
- Filter: Semua / Ralan / Ranap / IGD
- Timeline klikable → detail SOAP, ICD primer/sekunder, resep, status tagihan
- **Display-only standar:** asesmen terkait (perawat/gizi/farmasi) · penunjang (modal) · berkas/scan (preview mock) · persetujuan · audit trail · ringkasan pulang (ranap/IGD)
- Ringkasan pasien + cuplikan ringkasan pulang terakhir
- Search global home → buka RME pasien terpilih
- Read-only (penulisan klinis tetap di Poli/Ranap)

## 9. Jadwal Dokter (POLISHED v1)

- Board praktik harian (kartu): dokter · poli · ruang · jam · kuota terisi/max · sisa · status (Praktik/Penuh/Libur)
- Navigasi tanggal ±3 hari + strip minggu + tombol **Hari ini**
- Filter poli + status; KPI praktik / kuota terpakai / penuh / libur
- Aksi simulasi: tandai libur (+pengganti) · buka praktik · ubah kuota
- Terhubung Pendaftaran: pilihan dokter hari ini mengikuti slot yang tidak libur
- Bukan Antrol/booking online nyata

## 10. Master Data (POLISHED v1)

- Tab: Pegawai · Dokter · Pasien · Poli · Penjamin · Obat · Tarif · ICD-10 · Bangsal
- Tabel + cari + tambah/ubah; nonaktif/hapus (simulasi memori sesi)
- Sinkron ringan: pasien↔`patientsDB`, obat↔stok farmasi, ICD↔picker poli, dokter/poli↔Pendaftaran & Jadwal, bangsal↔nama/kelas Ranap
- Bukan persistensi DB / RBAC admin nyata

## 11. IGD — Gawat Darurat (POLISHED v1)

- Alur standar: **prehospital/ambulans (simulasi)** → registrasi → triase ESI → zona bed → TTV & SOAP → disposisi
- Tab Ambulans: notifikasi · di jalan · tiba · serah terima → antrian triase; jejak pra-RS di detail pasien
- Board zona + antrian prioritas + KPI; re-triase; siap disposisi
- Disposisi: **Pulang** · **Admit Ranap** (→ antrian Ranap) · **Rujuk**
- Badge menu = pasien IGD aktif + ambulans aktif
- Bukan koneksi PSC/API ambulans nyata

## 12. Bridging BPJS (POLISHED v1)

- Alur POC: **cek peserta → SEP → Antrol → klaim** (bukan koneksi VClaim/Antrean RS/Applicare nyata)
- Status koneksi mock: VClaim · Antrean RS · Applicare
- Tab **Peserta** — cari NIK/No.Kartu/RM; tampil status aktif/kelas/FKTP; lanjut buat SEP / buka RME
- Tab **SEP** — daftar SEP + terbitkan RJTL/RITL/IGD; update pulang / batalkan (simulasi)
- Tab **Antrol** — sinkron pasien BPJS dari Antrian Poli; kirim ulang taskid
- Tab **Klaim** — pending → validasi → terkirim → dibayar; badge menu = jumlah pending/validasi
- Chip Dashboard Bridging BPJS membuka layar ini

## 13. Hal yang Ditolak / Dihapus

- ~~Card hero "Selamat datang"~~ · ~~Dock navigasi bawah~~ · ~~Orbit solar~~ (→ ikon medis melayang) · ~~Backdrop dim saat search~~ (→ push-down) · ~~Tombol Pasien Baru bawah~~ · ~~Tombol back putih~~ (→ teal kaca)

## Cara Preview

- Lokal `http://localhost:8080` · HP `http://192.168.1.7:8080` (IP bisa berubah)
- Server mati → `python -m http.server 8080` di folder `design/`
- Simulasi device: F12 → Ctrl+Shift+M
