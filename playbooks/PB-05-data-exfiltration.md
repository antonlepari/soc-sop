# PB-05 — Data Exfiltration & Insider Threat

| Field | Nilai |
|-------|-------|
| Severity default | **P2**, naik ke **P1** bila menyangkut data pribadi, rahasia dagang, atau volume besar |
| MITRE ATT&CK | T1041, T1567 (Exfiltration Over Web Service), T1052 (Physical Medium), T1530 (Data from Cloud Storage) |
| Sumber deteksi | DLP, proxy, firewall, CASB, UEBA, EDR, laporan internal |

## 1. Trigger

- Alert DLP: data sensitif keluar lewat email, web upload, atau removable media
- Volume upload tidak wajar ke layanan cloud storage
- Akses massal ke file server oleh satu akun
- Pengunduhan besar dari database atau aplikasi bisnis
- Aktivitas tidak wajar dari karyawan yang akan atau sudah resign

## 2. Pertimbangan Khusus Sebelum Bertindak

Kasus insider memiliki dimensi hukum dan ketenagakerjaan. Karena itu:

> **Jangan menghubungi pengguna yang dicurigai** tanpa koordinasi dengan HR dan Legal. Pemberitahuan dini bisa memicu penghancuran bukti.

- Perlakukan seluruh temuan sebagai **restricted**
- Batasi jumlah orang yang tahu
- Dokumentasikan dengan standar yang bisa dipakai untuk proses ketenagakerjaan atau hukum
- Jaga chain of custody dengan ketat sejak awal

## 3. Langkah L1

- [ ] Identifikasi akun, host, dan waktu aktivitas
- [ ] Catat: apa yang dikirim, ke mana, berapa besar, lewat kanal apa
- [ ] Cek klasifikasi data yang terlibat
- [ ] Cek konteks pekerjaan: apakah akses ini wajar untuk jabatannya?
- [ ] Cek apakah tujuan pengiriman adalah mitra resmi
- [ ] **Jangan menghubungi pengguna.** Eskalasi ke L2.

Seluruh alert kategori ini **wajib dieskalasi** ke L2 — L1 tidak menutup sendiri kecuali jelas merupakan proses bisnis yang terdokumentasi.

## 4. Langkah L2

### 4.1 Tentukan Sifat Kejadian

| Kemungkinan | Ciri |
|-------------|------|
| Proses bisnis sah | Ada change request/persetujuan, tujuan adalah mitra resmi, pola konsisten |
| Kelalaian | Pengguna memakai layanan pribadi karena kepraktisan, tanpa niat jahat |
| Insider berniat jahat | Akses di luar kebutuhan kerja, di luar jam kerja, upaya penyamaran, berdekatan dengan resign |
| Akun terkompromi | Perilaku berubah drastis, login dari lokasi asing → lanjut ke [PB-03](PB-03-bruteforce-account-compromise.md) |

Membedakan **insider jahat** dari **akun terkompromi** adalah langkah paling penting, karena penanganannya berbeda total.

### 4.2 Investigasi

- [ ] Petakan seluruh aktivitas akun 30–90 hari ke belakang
- [ ] Identifikasi data spesifik yang diakses dan dikeluarkan (nama file, tabel, jumlah record)
- [ ] Periksa seluruh kanal exfiltration:
  - Email (termasuk ke alamat pribadi)
  - Upload web dan cloud storage pribadi
  - Removable media (USB)
  - Cetak dokumen
  - Screenshot / foto layar
  - Sinkronisasi ke perangkat pribadi
  - Akses remote di luar jam kerja
- [ ] Periksa upaya penyamaran: enkripsi arsip, penggantian nama file, penghapusan log, kompresi
- [ ] Korelasikan dengan konteks HR (status kepegawaian) — **melalui HR, bukan investigasi sendiri**

### 4.3 Containment

Sesuaikan dengan sifat kejadian:

| Sifat | Tindakan |
|-------|----------|
| Akun terkompromi | Containment penuh dan segera (lihat PB-03) |
| Insider dicurigai | **Koordinasi dulu** dengan HR dan Legal sebelum mencabut akses — timing sangat penting |
| Kelalaian | Tarik/hapus data dari layanan tidak resmi bila memungkinkan, edukasi pengguna |

Tindakan teknis yang tersedia:
- Blokir tujuan exfiltration
- Batasi izin akses akun
- Aktifkan monitoring intensif tanpa memberi tahu subjek (dengan persetujuan Legal)
- Cabut akses remote

### 4.4 Penilaian Dampak

- [ ] Volume dan jenis data yang keluar
- [ ] Apakah termasuk data pribadi? → aktifkan alur UU PDP
- [ ] Apakah termasuk rahasia dagang atau data pelanggan kontraktual?
- [ ] Apakah ada kewajiban notifikasi ke pelanggan atau regulator?
- [ ] Estimasi dampak bisnis

### 4.5 Penyelesaian

- [ ] Serahkan temuan terdokumentasi ke HR/Legal untuk kasus insider
- [ ] Tarik data dari pihak ketiga bila memungkinkan secara hukum
- [ ] Perkuat kontrol DLP untuk kanal yang dipakai
- [ ] Tinjau izin akses — mengapa akun ini bisa mengakses sebanyak itu?
- [ ] Lessons learned tanpa menyebut identitas bila kasus masih berproses

## 5. Deteksi yang Direkomendasikan

| Deteksi | Logika |
|---------|--------|
| Volume upload anomali | Upload harian > X kali baseline pengguna |
| Akses file massal | > N file diakses dalam M menit oleh satu akun |
| Cloud storage tidak resmi | Koneksi ke layanan penyimpanan di luar daftar yang disetujui |
| Aktivitas resign | Akses meningkat pada karyawan dengan status notice period |
| Arsip terenkripsi | Pembuatan arsip berpassword diikuti upload |
| Akses di luar jam kerja | Akses ke data sensitif di luar pola normal pengguna |

## 6. Batasan Etis dan Hukum

- Pemantauan karyawan harus punya dasar kebijakan yang telah dikomunikasikan
- Ikuti ketentuan pelindungan data yang berlaku, termasuk terhadap data pribadi karyawan
- Investigasi harus proporsional dan terbatas pada lingkup yang relevan
- SOC mengumpulkan fakta; **penilaian dan sanksi adalah ranah HR dan Legal**
