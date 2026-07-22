# PB-01 — Phishing & Email Berbahaya

| Field | Nilai |
|-------|-------|
| Severity default | P3 (naik ke P2 bila ada yang mengklik / memasukkan kredensial, P1 bila akun privileged terkompromi) |
| MITRE ATT&CK | T1566 (Phishing), T1566.001/.002, T1204 (User Execution), T1078 (Valid Accounts) |
| Sumber deteksi | Email gateway, laporan pengguna, EDR, proxy log |

## 1. Trigger

- Laporan pengguna melalui tombol *Report Phishing* atau kanal `<< SESUAIKAN >>`
- Alert dari email security gateway
- Deteksi akses ke domain phishing dari proxy/DNS log

## 2. Langkah L1

### 2.1 Kumpulkan Bukti Email

- [ ] Ambil email **beserta header lengkap** (jangan forward biasa — gunakan *forward as attachment* atau ekspor `.eml`)
- [ ] Catat: pengirim (envelope dan display), subjek, waktu terima, penerima
- [ ] **Jangan klik tautan apa pun dari workstation kantor**

### 2.2 Analisis Header

| Yang diperiksa | Indikasi mencurigakan |
|----------------|-----------------------|
| SPF / DKIM / DMARC | `fail` atau `softfail` |
| `Return-Path` vs `From` | Tidak cocok |
| Display name vs alamat asli | Nama menyerupai eksekutif, alamat domain asing |
| `Received` chain | Relay dari IP/negara yang tidak biasa |
| `Reply-To` | Berbeda dari `From` |
| Domain pengirim | Typosquatting, domain baru terdaftar |

### 2.3 Analisis Tautan dan Lampiran

- [ ] Ekstrak seluruh URL (termasuk di balik shortener dan tombol HTML)
- [ ] Periksa reputasi URL di URLScan/VirusTotal — **jangan buka langsung**
- [ ] Periksa umur domain (domain berumur < 30 hari sangat mencurigakan)
- [ ] Hitung hash lampiran, cek di VirusTotal/MalwareBazaar
- [ ] Untuk lampiran internal sensitif: gunakan sandbox internal, **jangan diunggah ke layanan publik**

### 2.4 Tentukan Sebaran

- [ ] Cari email serupa di seluruh mailbox (berdasarkan pengirim, subjek, URL, hash lampiran)
- [ ] Catat berapa penerima, berapa yang membuka, berapa yang mengklik (dari proxy/DNS log)
- [ ] Cek apakah ada yang memasukkan kredensial (indikasi: POST ke domain phishing)

### 2.5 Tindakan Pre-Approved L1

- [ ] Karantina/hapus email dari seluruh mailbox penerima
- [ ] Blokir domain/URL di proxy dan DNS filter
- [ ] Blokir pengirim di email gateway
- [ ] Hubungi pengguna yang mengklik untuk konfirmasi apa yang mereka lakukan

### 2.6 Eskalasi ke L2 bila

- Ada pengguna yang memasukkan kredensial
- Ada lampiran yang dieksekusi
- Target adalah akun privileged atau eksekutif (BEC / whaling)
- Ada indikasi kompromi lanjutan pada endpoint

## 3. Langkah L2

- [ ] Konfirmasi scope: berapa akun benar-benar terdampak
- [ ] Untuk kredensial tercuri:
  - Reset password segera
  - Revoke seluruh sesi dan token aktif
  - Cek log autentikasi: adakah login berhasil dari IP asing setelah waktu phishing?
  - Cek perubahan mailbox rule (attacker sering membuat rule auto-forward atau auto-delete)
  - Cek pendaftaran MFA device baru
  - Cek app password dan OAuth consent grant baru
- [ ] Untuk lampiran yang dieksekusi: lanjutkan ke [PB-02](PB-02-malware-endpoint.md)
- [ ] Sweep IOC ke seluruh lingkungan
- [ ] Buat rule deteksi untuk pola kampanye ini
- [ ] Koordinasi dengan tim komunikasi internal untuk peringatan ke seluruh karyawan bila kampanye masif

## 4. Indikator Kompromi Akun Email

Periksa hal berikut pada akun yang dicurigai:

| Indikator | Cara cek |
|-----------|----------|
| Mailbox rule mencurigakan | Cek forwarding rule, rule yang memindahkan email ke folder tersembunyi |
| Login dari lokasi tidak wajar | Log sign-in, impossible travel |
| MFA device baru | Log pendaftaran autentikator |
| OAuth grant baru | Daftar aplikasi pihak ketiga yang diizinkan |
| Email terkirim dari akun tersebut | Sent items, terutama ke kontak internal (phishing lanjutan) |
| Perubahan info pemulihan | Nomor telepon / email alternatif diubah |

## 5. Recovery

- [ ] Pastikan seluruh rule jahat dan akses pihak ketiga dicabut
- [ ] Konfirmasi ke pengguna bahwa akun sudah aman
- [ ] Monitoring ketat akun terdampak selama 14 hari
- [ ] Berikan edukasi singkat ke pengguna terdampak (tanpa menyalahkan)

## 6. Lessons Learned

- Mengapa email lolos dari gateway?
- Apakah kebijakan lampiran perlu diperketat?
- Apakah pengguna melaporkan dengan cepat? Bila tidak, mengapa?
- Apakah perlu simulasi phishing untuk unit kerja terkait?
