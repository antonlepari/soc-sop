# 04 — SOP Level 2: Investigation & Response

| Field | Nilai |
|-------|-------|
| Kode Dokumen | SOP-SOC-L2-001 |
| Versi | 1.0.0 |
| Pemilik | SOC Manager |
| Berlaku untuk | Seluruh analis Level 2 / Incident Responder |

---

## 1. Tujuan

Menetapkan prosedur investigasi mendalam dan penanganan insiden oleh L2, mulai dari validasi eskalasi hingga pemulihan dan pembelajaran, dengan tetap menjaga integritas barang bukti.

## 2. Prinsip Kerja L2

1. **Tentukan scope sebelum bertindak.** Containment parsial memberi tahu attacker bahwa ia terdeteksi, tanpa benar-benar mengusirnya.
2. **Amankan bukti sebelum mengubah keadaan.** Memory hilang saat reboot.
3. **Asumsikan lebih luas dari yang terlihat.** Satu host terkompromi jarang berdiri sendiri.
4. **Setiap tindakan dicatat dengan waktu.** Timeline adalah produk utama investigasi.
5. **Kecepatan penting, tapi jangan merusak produksi.** Koordinasi dengan pemilik sistem untuk tindakan berdampak.

---

## 3. Fase Penanganan (mengacu NIST SP 800-61 / PICERL)

```
Identification ─► Containment ─► Eradication ─► Recovery ─► Lessons Learned
      ▲                                                            │
      └────────────── Preparation (berkelanjutan) ◄────────────────┘
```

---

## 4. Fase 1 — Identification (Validasi & Investigasi)

### 4.1 Penerimaan Eskalasi

Saat menerima eskalasi dari L1:

- [ ] Konfirmasi penerimaan ke L1 secara eksplisit (jangan diam)
- [ ] Ambil alih tiket, ubah status menjadi `IN_PROGRESS`
- [ ] Catat waktu pengambilalihan (untuk pengukuran MTTR)
- [ ] Review ulang temuan L1 — jangan langsung menerima verdict-nya
- [ ] Konfirmasi atau koreksi severity awal

### 4.2 Rekonstruksi Timeline

Bangun timeline dari **titik paling awal yang bisa dibuktikan**, bukan dari waktu alert.

| Pertanyaan | Sumber Data |
|------------|-------------|
| Kapan aktivitas pertama kali terjadi? | Log autentikasi, proses, koneksi jaringan |
| Bagaimana initial access diperoleh? | Email gateway, log VPN/RDP, log web server, log endpoint |
| Apa yang dieksekusi setelahnya? | Process creation (Sysmon EID 1, auditd execve), command line, parent-child chain |
| Apakah ada persistence? | Scheduled task, service baru, run key, cron, systemd unit, akun baru, SSH authorized_keys |
| Apakah ada privilege escalation? | Perubahan grup, token manipulation, sudo, log UAC |
| Apakah ada lateral movement? | SMB/WinRM/RDP/SSH antar host, Kerberos ticket, PsExec, WMI |
| Apakah ada koneksi C2? | Firewall/proxy/DNS log, beaconing pattern, JA3/JA4 |
| Apakah ada data keluar? | Volume upload tidak wajar, koneksi ke cloud storage, arsip besar dibuat |

Format timeline yang dipakai:

```
| Waktu (WIB) | Waktu (UTC) | Host/Akun | Aktivitas | Sumber Bukti | ATT&CK |
```

### 4.3 Penentuan Scope

Pertanyaan wajib dijawab sebelum masuk fase containment:

1. Berapa host yang terdampak? Cari IOC yang sama di seluruh estate.
2. Berapa akun yang terdampak? Termasuk akun yang kredensialnya mungkin tersimpan di host tersebut.
3. Data apa yang bisa diakses dari posisi attacker saat ini?
4. Apakah ada indikasi akses masih aktif saat ini?
5. Apakah ada persistence yang belum ditemukan?

**Teknik pencarian scope:**
- Sweep IOC (hash, IP, domain, nama file, mutex) ke seluruh SIEM/EDR
- Cari pola perilaku (IOA), bukan hanya IOC — attacker mudah mengganti hash dan IP
- Periksa host lain yang pernah berkomunikasi dengan host terdampak
- Periksa aktivitas akun terdampak di seluruh sistem, bukan hanya host asal

### 4.4 Akuisisi Bukti

Urutan prioritas pengumpulan (dari yang paling mudah hilang — RFC 3227 order of volatility):

1. Memory (RAM) — sebelum host dimatikan atau di-reboot
2. Koneksi jaringan aktif, daftar proses berjalan, sesi login
3. Isi disk: file sistem, log, artefak
4. Log terpusat di SIEM (relatif aman, tetap ambil salinan)
5. Backup dan arsip

**Aturan chain of custody:**
- Hitung hash (SHA-256) setiap artefak segera setelah diakuisisi
- Catat: siapa mengambil, kapan (WIB dan UTC), dari mana, dengan tools apa, hash-nya
- Simpan di penyimpanan bukti dengan akses terbatas
- Jangan bekerja pada bukti asli — gunakan salinan

---

## 5. Fase 2 — Containment

### 5.1 Keputusan Containment

Sebelum eksekusi, pertimbangkan:

| Pertimbangan | Pertanyaan |
|--------------|------------|
| Kelengkapan scope | Sudah tahu semua host terdampak? Kalau belum, containment parsial berisiko |
| Dampak bisnis | Sistem apa yang berhenti? Sudah koordinasi dengan pemilik sistem? |
| Bukti | Sudah ambil memory sebelum isolasi/reboot? |
| Tipping off | Apakah tindakan ini memberi tahu attacker? Bila memungkinkan, lakukan serentak |

### 5.2 Containment Jangka Pendek

| Skenario | Tindakan |
|----------|----------|
| Endpoint terkompromi | Isolasi jaringan via EDR (host tetap hidup agar memory dan bukti terjaga) |
| Akun terkompromi | Disable akun, revoke session/token aktif, reset kredensial, cabut MFA device mencurigakan |
| Server terkompromi | Isolasi segmen, matikan layanan terdampak bila perlu, koordinasi dengan pemilik sistem |
| Malware menyebar | Blokir hash di EDR, blokir C2 di firewall/DNS, isolasi segmen |
| Eksploitasi web | Blokir pola serangan di WAF, batasi akses ke endpoint rentan |
| Data exfiltration | Blokir tujuan, batasi egress, cabut akses akun terkait |

> **Isolasi lebih baik daripada shutdown.** Shutdown menghilangkan memory dan bukti volatile.

### 5.3 Containment Jangka Menengah

- Patch atau workaround untuk kerentanan yang dieksploitasi
- Perketat rule firewall/segmentasi
- Tambahkan rule deteksi khusus untuk IOC/IOA insiden ini
- Reset kredensial yang berpotensi tercuri secara terjadwal dan terkoordinasi
- Untuk kompromi akun privileged: pertimbangkan reset krbtgt (dua kali, dengan jeda) `<< butuh approval SOC Manager + IT >>`

---

## 6. Fase 3 — Eradication

- [ ] Hapus seluruh malware dan tools attacker dari lingkungan
- [ ] Hapus seluruh mekanisme persistence yang ditemukan
- [ ] Hapus akun/kunci/akses yang dibuat attacker (termasuk SSH key, API key, app password, OAuth grant)
- [ ] Tutup akar penyebab: patch kerentanan, perbaiki misconfiguration, perbaiki kontrol akses
- [ ] Verifikasi ulang dengan sweep IOC menyeluruh setelah pembersihan

**Kapan harus rebuild, bukan bersih-bersih:**
- Ada indikasi rootkit, bootkit, atau modifikasi firmware
- Akses attacker berlangsung lama dan scope-nya tidak bisa dipastikan
- Host adalah aset Tier 0
- Ada keraguan sedikit pun terhadap kebersihan sistem

> Prinsip: **rebuild lebih murah daripada kompromi berulang.**

---

## 7. Fase 4 — Recovery

- [ ] Kembalikan sistem ke produksi secara bertahap, bukan sekaligus
- [ ] Pastikan sistem yang dipulihkan sudah di-patch dan di-hardening
- [ ] Restore dari backup yang **diverifikasi bersih** (cek tanggal backup vs tanggal kompromi awal)
- [ ] Pasang monitoring ketat pada aset yang dipulihkan minimal **14 hari** `<< SESUAIKAN >>`
- [ ] Buat rule deteksi khusus untuk mendeteksi kembalinya attacker
- [ ] Konfirmasi ke pemilik sistem bahwa layanan berfungsi normal

**Kriteria penutupan insiden:**
- Semua host terdampak sudah bersih atau di-rebuild dan diverifikasi
- Akar penyebab sudah diperbaiki, bukan sekadar ditutup gejalanya
- Tidak ada aktivitas mencurigakan terkait selama periode monitoring
- Laporan insiden selesai dan disetujui
- Item lessons learned sudah dicatat dengan penanggung jawab dan tenggat

---

## 8. Fase 5 — Lessons Learned

Wajib untuk seluruh insiden **P1 dan P2**, maksimal **5 hari kerja** setelah penutupan.

Agenda pertemuan:

1. Kronologi insiden (timeline final)
2. Apa yang berjalan baik?
3. Apa yang gagal atau lambat? (fokus proses, **bukan menyalahkan orang**)
4. Berapa lama MTTD dan MTTR? Mengapa?
5. Deteksi apa yang seharusnya menangkap ini lebih awal?
6. Kontrol preventif apa yang perlu ditambahkan?
7. Apakah SOP/playbook perlu direvisi?

Output: daftar action item dengan **penanggung jawab dan tenggat waktu**, ditinjau di rapat SOC berikutnya.

---

## 9. Threat Hunting Proaktif

L2 mengalokasikan minimal **4 jam per minggu** `<< SESUAIKAN >>` untuk hunting, tidak menunggu alert.

### Siklus Hunting

```
Hipotesis ─► Kumpulkan data ─► Analisis ─► Temuan? ─► Ya: buka insiden
                                              │
                                              └─ Tidak: buat/perbaiki deteksi + dokumentasikan
```

**Hunting tanpa temuan tetap bernilai** — hasilnya adalah rule deteksi baru atau konfirmasi cakupan visibilitas.

### Contoh Hipotesis

| Area | Hipotesis |
|------|-----------|
| Persistence | Ada scheduled task/cron/systemd unit yang dibuat di luar jendela change management |
| Credential Access | Ada proses yang mengakses LSASS atau membaca `/etc/shadow` di luar tools sah |
| C2 | Ada beaconing dengan interval tetap ke domain yang baru terdaftar |
| Lateral Movement | Ada akun yang tiba-tiba login ke host yang belum pernah diaksesnya |
| Exfiltration | Ada upload ke layanan cloud storage yang tidak disetujui perusahaan |
| Defense Evasion | Ada penghapusan log, penonaktifan antivirus, atau clearing event log |
| Living off the Land | `certutil`, `bitsadmin`, `mshta`, `curl` dipakai untuk mengunduh file dari internet |

Dokumentasikan setiap hunt: hipotesis, data source, query, temuan, tindak lanjut.

---

## 10. Detection Engineering & Tuning

L2 bertanggung jawab menjaga kualitas deteksi.

### 10.1 Kriteria Tuning

Rule menjadi kandidat tuning bila:
- FP rate di atas **80%** selama 30 hari, atau
- Volume alert melebihi kapasitas triage tanpa nilai deteksi yang sepadan

### 10.2 Aturan Tuning

1. **Persempit, jangan matikan.** Tambahkan kondisi pengecualian yang spesifik (proses + user + host + path), bukan mematikan rule.
2. Pengecualian harus **sesempit mungkin.** Jangan mengecualikan seluruh subnet atau seluruh grup pengguna.
3. Setiap tuning wajib punya justifikasi tertulis, pemohon, dan approver.
4. Setiap pengecualian punya **tanggal review**; pengecualian permanen adalah lubang deteksi permanen.
5. Uji rule baru dalam mode monitoring sebelum dipakai untuk alerting produksi.

### 10.3 Alur Rule Baru

```
Ide (hunting/insiden/threat intel)
  ─► Tulis rule (Sigma / rule native)
  ─► Uji di lab / mode audit
  ─► Ukur volume & FP selama 7 hari
  ─► Review peer
  ─► Deploy produksi + dokumentasikan
  ─► Buat/aktualisasi playbook untuk rule tersebut
```

Setiap rule produksi wajib punya: deskripsi, pemetaan ATT&CK, severity, false positive yang diketahui, dan langkah triage yang disarankan.

---

## 11. Kapan L2 Mengeskalasi ke L3 / CSIRT

Eskalasi wajib bila:

- Insiden mengenai aset Tier 0 dengan kompromi terkonfirmasi
- Terindikasi APT atau adversary dengan kemampuan tinggi
- Diperlukan reverse engineering malware
- Diperlukan forensik mendalam untuk kebutuhan hukum
- Data pribadi terdampak (memicu kewajiban UU PDP)
- Scope insiden melebihi kapasitas penanganan shift berjalan
- Insiden berlangsung lebih dari **8 jam** tanpa containment yang berhasil
- Diperlukan komunikasi ke regulator, penegak hukum, pelanggan, atau publik

---

## 12. Mentoring dan Quality Assurance

L2 melakukan review sampel tiket L1 secara berkala:

- Minimal **10%** tiket L1 per minggu direview
- Seluruh tiket yang ditutup sebagai FP pada aset Tier 0 direview
- Umpan balik disampaikan secara konstruktif dan spesifik pada tiket
- Pola kesalahan berulang menjadi bahan sesi pelatihan bulanan
