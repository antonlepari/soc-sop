# PB-04 — Serangan Aplikasi Web

| Field | Nilai |
|-------|-------|
| Severity default | P3 untuk percobaan, **P2** bila eksploitasi berhasil, **P1** bila RCE / kebocoran data / defacement |
| MITRE ATT&CK | T1190 (Exploit Public-Facing Application), T1505.003 (Web Shell), T1078 |
| Sumber deteksi | WAF, IDS/IPS, log web server, SIEM, EDR pada server web |

## 1. Trigger

- Alert WAF: SQLi, XSS, path traversal, command injection, deserialization
- Anomali pada log web server: lonjakan 4xx/5xx, request ke path tidak wajar
- Deteksi file baru pada direktori web (indikasi web shell)
- Laporan pihak ketiga atau bug bounty

## 2. Langkah L1

### 2.1 Bedakan Percobaan vs Keberhasilan

Ini penentu severity. Pertanyaannya bukan "ada serangan?" tapi **"apakah berhasil?"**

| Indikator gagal | Indikator berhasil |
|-----------------|--------------------|
| WAF memblokir (status blocked) | Response 200 dengan payload dikembalikan |
| Response 403/400 | Response time anomali (indikasi time-based SQLi) |
| Error generik | Error database bocor ke response |
| Tidak ada aktivitas lanjutan | Ada proses baru dijalankan oleh user web server |
| — | Ada file baru di direktori web |
| — | Ada koneksi keluar dari server web |

### 2.2 Kumpulkan Konteks

- [ ] Identifikasi aplikasi, server, dan pemilik sistem
- [ ] Catat IP sumber, user agent, pola request, dan rentang waktu
- [ ] Cek reputasi IP sumber
- [ ] Cek apakah request terautentikasi — akun mana?
- [ ] Cek volume: satu-dua request (scanner otomatis) atau serangan terarah?
- [ ] **Cek jadwal pentest / bug bounty** — apakah ini aktivitas yang sah?

### 2.3 Tindakan Pre-Approved L1

- [ ] Blokir IP sumber di WAF/firewall untuk serangan yang jelas berbahaya
- [ ] Konfirmasi ke tim aplikasi bila ada aktivitas testing terjadwal

### 2.4 Eskalasi bila

- Ada indikasi eksploitasi berhasil
- Ditemukan file mencurigakan di direktori web
- Ada proses tidak wajar dijalankan oleh service account web server
- Ada koneksi keluar dari server web ke IP asing
- Serangan terarah dan persisten terhadap satu aplikasi

## 3. Langkah L2

### 3.1 Verifikasi Eksploitasi

- [ ] Rekonstruksi rangkaian request dari IP sumber secara lengkap
- [ ] Bandingkan request yang diblokir dengan yang lolos
- [ ] Periksa response body untuk data yang bocor
- [ ] Periksa log aplikasi dan log database untuk query anomali
- [ ] Periksa proses pada server: adakah shell dijalankan oleh `www-data` / `IIS APPPOOL`?

### 3.2 Pencarian Web Shell

- [ ] Cari file yang dibuat/dimodifikasi pada direktori web setelah waktu serangan
- [ ] Cari file dengan ekstensi yang bisa dieksekusi di direktori upload
- [ ] Cari pola web shell: fungsi eval, base64 decode, request parameter yang langsung dieksekusi
- [ ] Bandingkan isi direktori web dengan repositori kode / hash referensi
- [ ] Periksa `.htaccess` atau konfigurasi web server yang dimodifikasi

### 3.3 Containment

- [ ] Blokir sumber serangan
- [ ] Terapkan virtual patching di WAF untuk pola eksploitasi
- [ ] Bila web shell ditemukan: **ambil sampel dulu**, lalu hapus, lalu cari akses yang dibuatnya
- [ ] Bila kompromi server terkonfirmasi: isolasi server, koordinasi dengan pemilik sistem untuk failover
- [ ] Reset kredensial yang tersimpan di server (koneksi database, API key, service account)

### 3.4 Investigasi Lanjutan

- [ ] Tentukan sejak kapan aplikasi rentan — periksa log historis untuk eksploitasi lebih awal
- [ ] Periksa akses ke database: query apa yang dijalankan, data apa yang diambil
- [ ] Periksa lateral movement dari server web ke jaringan internal
- [ ] Periksa apakah ada backdoor selain web shell (akun baru, cron, systemd)

### 3.5 Eradication & Recovery

- [ ] Perbaiki kerentanan di level kode, bukan hanya WAF rule
- [ ] Rebuild server dari image bersih bila kompromi terkonfirmasi
- [ ] Deploy versi aplikasi yang sudah diperbaiki
- [ ] Reset seluruh secret yang tersimpan di server
- [ ] Verifikasi integritas file aplikasi
- [ ] Monitoring ketat 14 hari

## 4. Kasus Khusus

| Kasus | Penanganan tambahan |
|-------|---------------------|
| Defacement | Dokumentasikan tampilan sebelum pemulihan, cari vektor akses, cek apakah ada backdoor tertinggal |
| Kebocoran data pelanggan | Aktifkan alur notifikasi UU PDP lewat Legal, tentukan jumlah dan jenis data terdampak |
| Laporan bug bounty | Verifikasi dulu, jangan langsung dianggap serangan; koordinasi dengan program owner |
| Serangan supply chain (library) | Periksa SBOM, cek versi dependensi rentan di seluruh aplikasi |

## 5. Rekomendasi Preventif

- WAF dalam mode blocking dengan tuning berkala
- Pemindaian kerentanan aplikasi terjadwal (SAST/DAST/SCA)
- File integrity monitoring pada direktori web
- Prinsip least privilege untuk service account web
- Segmentasi: server web tidak boleh punya akses bebas ke jaringan internal
- Logging aplikasi yang memadai — banyak investigasi gagal karena log tidak cukup
