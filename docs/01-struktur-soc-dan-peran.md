# 01 — Struktur SOC, Peran, dan Batas Kewenangan

## 1. Tujuan

Menetapkan struktur organisasi SOC, tanggung jawab tiap tier, serta **batas kewenangan** agar tidak terjadi tumpang tindih atau tindakan di luar otoritas saat menangani insiden.

## 2. Ruang Lingkup

Berlaku untuk seluruh personel SOC (L1, L2, L3), SOC Manager, dan pihak yang berinteraksi dengan proses penanganan insiden.

## 3. Model Tier

### 3.1 Level 1 — Security Analyst (Monitoring & Triage)

**Fokus:** kecepatan dan konsistensi. L1 adalah filter pertama.

**Tanggung jawab:**
- Memantau dashboard SIEM/EDR secara terus-menerus selama shift
- Melakukan triage seluruh alert yang masuk sesuai antrean prioritas
- Melakukan enrichment dasar (reputasi IP/domain/hash, cek asset inventory, cek jadwal maintenance)
- Menentukan status awal: false positive, benign true positive, atau perlu eskalasi
- Membuat dan melengkapi tiket insiden sesuai standar dokumentasi
- Melaksanakan tindakan respons yang **sudah pre-approved** saja
- Menyusun serah terima shift

**Kompetensi minimum:**
- Pemahaman dasar TCP/IP, DNS, HTTP, autentikasi
- Membaca log Windows Event, Linux syslog/auditd, log firewall dan proxy
- Menguasai query dasar SIEM
- Familiar dengan MITRE ATT&CK tingkat taktik

### 3.2 Level 2 — Senior Security Analyst / Incident Responder

**Fokus:** kedalaman analisis dan pengambilan keputusan respons.

**Tanggung jawab:**
- Menerima dan memvalidasi ulang eskalasi dari L1
- Melakukan investigasi mendalam: korelasi lintas sumber log, timeline reconstruction, analisis endpoint
- Analisis malware tingkat dasar hingga menengah (static/dynamic pada sandbox)
- Mengambil keputusan dan mengeksekusi containment, eradication, dan recovery
- Menentukan scope insiden (berapa host/akun/data terdampak)
- Menyusun Root Cause Analysis dan laporan insiden
- Melakukan threat hunting terjadwal berbasis hipotesis
- Melakukan tuning rule deteksi dan mengusulkan rule baru
- Membimbing (mentoring) analis L1

**Kompetensi minimum:**
- Semua kompetensi L1
- Forensik dasar (disk, memory, artefak Windows/Linux)
- Analisis trafik jaringan (PCAP)
- Scripting untuk otomasi (Python/PowerShell/Bash)
- Menguasai MITRE ATT&CK hingga level teknik dan sub-teknik
- Mampu menulis dan menyesuaikan rule deteksi (Sigma, Wazuh rule, YARA dasar)

### 3.3 Level 3 — Threat Hunter / Forensic Specialist / Detection Engineer

**Fokus:** kasus kompleks dan peningkatan kapabilitas jangka panjang.

**Tanggung jawab:**
- Menangani insiden major, APT, dan kasus yang perlu forensik mendalam
- Reverse engineering malware
- Merancang arsitektur deteksi dan use case baru
- Threat intelligence dan attribution
- Koordinasi dengan penegak hukum, regulator, dan vendor eksternal

### 3.4 SOC Manager

Bertanggung jawab atas operasional SOC secara keseluruhan, jadwal shift, kualitas output, komunikasi ke manajemen dan stakeholder bisnis, serta keputusan yang berdampak pada layanan produksi.

## 4. Batas Kewenangan Tindakan

Tabel ini mengikat. Tindakan di luar kewenangan **wajib** dieskalasi terlebih dahulu.

| Tindakan | L1 | L2 | L3 | Perlu Approval |
|----------|:--:|:--:|:--:|----------------|
| Membaca dan query log | ✅ | ✅ | ✅ | — |
| Menutup alert sebagai false positive | ✅ | ✅ | ✅ | Sesuai kriteria di SOP L1 |
| Menutup insiden P1/P2 | ❌ | ⚠️ | ✅ | SOC Manager |
| Blokir IP eksternal di firewall/WAF | ⚠️ | ✅ | ✅ | L1 hanya bila masuk daftar pre-approved |
| Isolasi endpoint dari jaringan | ❌ | ✅ | ✅ | Notifikasi ke pemilik sistem |
| Disable akun pengguna | ❌ | ✅ | ✅ | Koordinasi dengan IT/HR |
| Reset kredensial massal | ❌ | ⚠️ | ✅ | SOC Manager + IT |
| Mematikan / restart server produksi | ❌ | ❌ | ⚠️ | SOC Manager + Pemilik Sistem |
| Akuisisi memory/disk image | ❌ | ✅ | ✅ | Catat chain of custody |
| Menghapus/membersihkan file malware | ❌ | ✅ | ✅ | Ambil sampel dulu sebelum dihapus |
| Mengubah rule deteksi produksi | ❌ | ✅ | ✅ | Via change request |
| Komunikasi ke pihak eksternal/media | ❌ | ❌ | ❌ | Manajemen / Humas |
| Notifikasi ke regulator | ❌ | ❌ | ❌ | Legal + Manajemen |

Keterangan: ✅ berwenang · ⚠️ bersyarat · ❌ tidak berwenang

## 5. Matriks RACI Penanganan Insiden

| Aktivitas | L1 | L2 | L3 | SOC Mgr | IT Ops | Legal |
|-----------|:--:|:--:|:--:|:-------:|:------:|:-----:|
| Monitoring & triage alert | **R** | C | I | A | — | — |
| Validasi & investigasi | C | **R** | C | A | I | — |
| Containment | I | **R** | C | A | C | — |
| Eradication & recovery | I | **R** | C | A | **R** | — |
| Root cause analysis | I | **R** | C | A | C | — |
| Laporan insiden | C | **R** | C | **A** | I | I |
| Notifikasi regulator | — | I | I | C | — | **R/A** |
| Lessons learned | C | **R** | C | **A** | C | I |

R = Responsible · A = Accountable · C = Consulted · I = Informed

## 6. Pola Shift

Model default 24/7 dengan tiga shift `<< SESUAIKAN >>`:

| Shift | Jam (WIB) | Komposisi Minimum |
|-------|-----------|-------------------|
| Pagi | 07:00 – 15:00 | 2 × L1, 1 × L2 |
| Sore | 15:00 – 23:00 | 2 × L1, 1 × L2 |
| Malam | 23:00 – 07:00 | 1 × L1, 1 × L2 on-call |

L2 on-call wajib merespons panggilan maksimal **15 menit** untuk insiden P1.
