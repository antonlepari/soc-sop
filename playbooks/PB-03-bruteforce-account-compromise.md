# PB-03 — Brute Force & Account Compromise

| Field | Nilai |
|-------|-------|
| Severity default | P3 untuk percobaan gagal, **P2** bila ada login berhasil, **P1** bila akun privileged |
| MITRE ATT&CK | T1110 (Brute Force), T1110.003 (Password Spraying), T1078 (Valid Accounts), T1621 (MFA Request Generation) |
| Sumber deteksi | SIEM (log autentikasi), IdP, VPN, firewall, aplikasi |

## 1. Trigger

- Banyak autentikasi gagal pada satu akun (brute force klasik)
- Satu password dicoba ke banyak akun (password spraying)
- Login berhasil dari lokasi/perangkat tidak wajar
- Impossible travel
- MFA fatigue: permintaan MFA berulang dalam waktu singkat

## 2. Langkah L1

### 2.1 Klasifikasi Pola

| Pola | Ciri | Risiko |
|------|------|--------|
| Brute force | Banyak percobaan, satu akun, satu sumber | Rendah bila ada lockout |
| Password spraying | Sedikit percobaan per akun, banyak akun | **Tinggi** — sering lolos lockout policy |
| Credential stuffing | Kombinasi user+password valid dari kebocoran lain | **Tinggi** |
| MFA fatigue | Push MFA berulang hingga korban menyetujui | **Tinggi** |

### 2.2 Pertanyaan Kunci

- [ ] **Apakah ada yang berhasil login?** Ini pertanyaan paling penting.
- [ ] Berapa akun yang menjadi target?
- [ ] Sumber serangan: internal atau eksternal? Satu IP atau banyak?
- [ ] Layanan apa yang diserang: VPN, OWA, SSH, RDP, aplikasi web?
- [ ] Apakah akun yang ditarget bersifat privileged?
- [ ] Apakah ada pola waktu (di luar jam kerja)?

### 2.3 Tindakan Pre-Approved L1

- [ ] Blokir IP sumber di firewall/WAF (bila jelas eksternal dan bukan IP mitra)
- [ ] Verifikasi apakah akun sudah ter-lockout otomatis
- [ ] Hubungi pengguna untuk konfirmasi bila ada login berhasil yang mencurigakan

### 2.4 Eskalasi bila

- **Ada login berhasil** setelah rangkaian percobaan gagal
- Akun privileged menjadi target
- Password spraying terhadap banyak akun
- Serangan berasal dari IP **internal** (indikasi host sudah terkompromi → lateral movement)
- Ada permintaan MFA yang akhirnya disetujui secara tidak wajar

## 3. Langkah L2

### 3.1 Bila Belum Ada Login Berhasil

- [ ] Verifikasi lockout policy dan rate limiting berfungsi
- [ ] Blokir sumber serangan (IP, ASN, atau geo bila relevan)
- [ ] Cek apakah akun target berasal dari kebocoran data publik
- [ ] Paksa reset password untuk akun dengan password lemah/tereskpos
- [ ] Evaluasi apakah layanan tersebut memang harus terekspos ke internet

### 3.2 Bila Ada Login Berhasil (Account Compromise)

**Containment segera:**

- [ ] Disable akun atau paksa reset password
- [ ] **Revoke seluruh sesi dan refresh token aktif** — reset password saja tidak memutus sesi yang sudah berjalan
- [ ] Cabut MFA device yang tidak dikenal
- [ ] Cabut app password dan OAuth grant yang mencurigakan

**Investigasi:**

- [ ] Tentukan waktu pasti login berhasil pertama
- [ ] Petakan seluruh aktivitas akun sejak saat itu:
  - Sistem apa saja yang diakses
  - File apa yang dibuka atau diunduh
  - Email yang dikirim (phishing internal lanjutan?)
  - Perubahan konfigurasi atau izin
  - Pembuatan akun atau kredensial baru
- [ ] Cek persistence pada level identitas:
  - Mailbox rule baru
  - MFA device tambahan
  - App password / API key baru
  - Delegasi mailbox atau akses berbagi
  - Perubahan info pemulihan akun
- [ ] Cek lateral movement dari akun tersebut
- [ ] Nilai data apa yang bisa diakses attacker dari izin akun tersebut

### 3.3 Eradication & Recovery

- [ ] Hapus seluruh persistence identitas
- [ ] Reset kredensial akun dan seluruh akun terkait yang berisiko
- [ ] Bila akun privileged terkompromi: pertimbangkan reset kredensial tier terkait secara luas `<< approval SOC Manager >>`
- [ ] Tinjau ulang izin akun — apakah terlalu besar (over-privileged)?
- [ ] Monitoring ketat akun selama 30 hari

## 4. Deteksi yang Direkomendasikan

| Deteksi | Logika singkat |
|---------|----------------|
| Password spraying | > N akun berbeda gagal login dari satu IP dalam M menit |
| Brute force sukses | Rangkaian gagal diikuti sukses dari IP yang sama |
| Impossible travel | Dua login berhasil dari lokasi yang mustahil dijangkau dalam selisih waktu tersebut |
| Login di luar kebiasaan | Login pertama kali dari negara/ASN/perangkat baru |
| MFA fatigue | > N permintaan MFA ditolak dalam M menit, diikuti satu persetujuan |
| Perubahan MFA | Pendaftaran MFA device baru di luar proses onboarding |

## 5. Rekomendasi Preventif

- MFA phishing-resistant (FIDO2/passkey) untuk akun privileged
- Number matching pada push MFA untuk mengurangi MFA fatigue
- Conditional access berbasis lokasi dan kepatuhan perangkat
- Pemantauan kredensial bocor terhadap domain organisasi
- Batasi eksposur layanan autentikasi ke internet
