# BLUEPRINT — SIMRS Prototype

> Status: **Final v1** — disetujui untuk dijadikan acuan implementasi.
> Acuan data: `docs/ERD.md` (26 entitas, Final v1).
> Stack: TBD (blueprint ini stack-agnostic).

## 1. Tujuan & Prinsip

- **Goal**: prototype SIMRS web modern yang **lebih bagus dari Khanza** — cepat, jelas, menyenangkan dipakai staf RS (customer harus suka).
- **Khanza = patokan fitur saja, bukan patokan kualitas.**
- **29 fitur** (lihat pemetaan fitur → entitas di `docs/ERD.md`).
- Tanpa biaya eksternal; tanpa login di prototype (peran disimulasikan lewat menu).

## 2. Struktur Menu (10 bagian)

| Menu | Isi | Pengguna utama |
|---|---|---|
| 1. Dashboard | Ringkasan hari ini: pasien aktif, antrian per poli, pendapatan, okupansi ranap | Semua |
| 2. Pendaftaran | Registrasi pasien baru/baru + registrasi kunjungan + no. antrian | Adm. pendaftaran |
| 3. Antrian Poli | Daftar antri per poli, tombol panggil → status berubah | Adm poli |
| 4. Pelayanan Poli | CPPT SOAP, diagnosis ICD-10, tindakan, resep | Dokter |
| 5. Rawat Inap | Bed board, admit, layanan harian, proses pulang | Perawat/dokter |
| 6. Farmasi | Resep masuk → dispensi, stok & kartu stok, penerimaan obat | Farmasi |
| 7. Kasir | Tagihan pasien, pembayaran, kuitansi PDF | Kasir |
| 8. **Rekam Medis (RME)** | Profil pasien 360° + timeline riwayat kunjungan — rumah modul RME (fitur #29) | Semua |
| 9. Jadwal Dokter | Praktik hari ini (fitur #28) | Publik/loket |
| 10. Master Data | Pegawai, dokter, penjamin, poli, tarif, aset, ICD-10 | Admin |

**Catatan RME**: menu 8 adalah *read view* agregat semua data klinis pasien. Penulisan data klinis baru terjadi di menu 4 (Pelayanan Poli) dan menu 5 (Rawat Inap); perekaman semuanya terlihat di menu 8.

## 3. Alur Kerja Bisnis

### Alur 1 — Rawat Jalan (cerita utuh demo)

1. Pasien datang → petugas **cari pasien** (RM/NIK/nama, autocomplete) atau **registrasi baru** → dapat no. RM
2. Petugas **registrasi kunjungan**: pilih poli + dokter (dari jadwal hari ini) + penjamin → sistem kasih **no. antrian** → status `antri`
3. **Panggil antrian** → status `diperiksa` → dokter buka halaman pemeriksaan pasien itu
4. Dokter isi **CPPT (SOAP)** → **diagnosis ICD-10** (primer + sekunder) → **tindakan** (harga otomatis sesuai penjamin) → **resep** → simpan. Resep status `baru` → muncul di farmasi
5. **Farmasi** proses resep → cek stok → stok berkurang (mutasi `keluar`) → resep `selesai`, item masuk tagihan
6. **Kasir** buka tagihan (tindakan + obat) → umum: bayar tunai/transfer/QRIS → `lunas` + **kuitansi PDF**; BPJS: tagihan jadi `piutang`
7. Kunjungan `selesai` — semua tercatat di **RME**

### Alur 2 — Rawat Inap (cabang dari poli, langkah 4)

1. Dokter rekomendasi ranap → petugas **admit** via bed board → pilih tempat tidur kosong → status bed `terisi`
2. Harian: dokter **visite** → CPPT + tindakan ranap tercatat
3. **Pulang**: tanggal keluar diisi → biaya kamar otomatis (`tarif_harian` × lama rawat) → masuk tagihan
4. Kasir: bayar/piutang (sama Alur 1 langkah 6) → bed kembali `kosong`

### Alur 3 — Keuangan

- Tagihan terbentuk otomatis dari `TAGIHAN_ITEM` (sumber: tindakan, resep_item, kamar)
- Umum → lunas di kasir → kuitansi PDF (fitur #27)
- BPJS/perusahaan → `piutang` (prototype: cukup status `belum_klaim|diajukan|dibayar`, tanpa modul klaim nyata)

## 4. Standar UX (inti goal "customer suka")

- **Autocomplete instan** di semua pencarian: pasien, obat, ICD-10, tarif
- **Badge status berwarna konsisten** di seluruh aplikasi:
  - `antri` kuning, `diperiksa` biru, `selesai` hijau
  - `belum_bayar` merah, `lunas` hijau, `piutang` oranye
- **Toast** konfirmasi tiap aksi penting (simpan CPPT, panggil antrian, bayar, dispensi resep)
- **Timeline** riwayat kunjungan di profil pasien (bukan tabel mentah)
- **Minim klik**: satu layar per langkah kerja, tombol aksi selalu terlihat
- **Responsive**: desktop + tablet
- **Kuitansi PDF** rapi: kop RS, identitas pasien & kunjungan, rincian item, total, metode bayar

## 5. Data Awal (Seed)

- ICD-10 (referensi), penjamin (umum/BPJS/perusahaan), poli, dokter + jadwal praktik, pegawai, obat + stok, tarif + tarif per penjamin, bangsal/kamar/tempat tidur, aset, pasien dummy (nama fiktif — dilarang data pasien asli).

## 6. Di Luar Scope

Mengacu ke daftar final di `docs/ERD.md`: layar antrian publik, notifikasi WA mock, feedback pasien, IGD, operasi, laboratorium, radiologi, pengadaan penuh, laporan kompleks, login/role, booking online, pembayaran nyata.

## 7. Roadmap Setelah Blueprint

1. **Keputusan stack** — rekomendasi: Laravel 12 + MySQL + Filament; alternatif: Next.js + PostgreSQL self-hosted + Drizzle
2. **ERD fisik** → schema database → seed
3. **Implementasi per modul**, urut sesuai alur kerja: Pendaftaran → Antrian → Poli → Farmasi → Kasir → Ranap → RME → Master → Dashboard → Jadwal Dokter
4. Verifikasi per modul sebelum lanjut modul berikutnya

## Checklist Planning

- [x] Scope fitur — 29 fitur (terkunci)
- [x] ERD — `docs/ERD.md` Final v1
- [x] Blueprint — file ini, Final v1
- [ ] Keputusan stack teknologi
- [ ] ERD fisik + schema + seed
