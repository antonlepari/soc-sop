# 03 — SOP Level 1: Monitoring & Triage

| Field | Nilai |
|-------|-------|
| Kode Dokumen | SOP-SOC-L1-001 |
| Versi | 1.0.0 |
| Pemilik | SOC Manager |
| Berlaku untuk | Seluruh analis Level 1 |

---

## 1. Tujuan

Memastikan setiap alert keamanan yang masuk ke SOC **divalidasi secara konsisten, tepat waktu, dan terdokumentasi**, sehingga alert yang benar-benar berbahaya cepat sampai ke tangan yang tepat dan noise tidak membanjiri tim.

## 2. Prinsip Kerja L1

1. **Kalau ragu, eskalasi.** Menutup true positive jauh lebih mahal daripada mengeskalasi false positive.
2. **Tidak ada alert yang dilewati.** Semua alert harus punya keputusan dan jejak dokumentasi.
3. **Dokumentasi menyusul bukan berarti tidak ada.** Tiket diisi sebelum alert ditutup.
4. **Jangan mengubah keadaan sistem** di luar tindakan yang sudah pre-approved.
5. **Verifikasi sebelum bertindak.** Jangan blokir IP hanya berdasarkan satu alert reputasi.

---

## 3. Prosedur Awal Shift (Shift Start Checklist)

Dikerjakan dalam 15 menit pertama shift.

- [ ] Login ke seluruh tools operasional: SIEM (Wazuh Dashboard), EDR, IDS/IPS, email gateway, ticketing
- [ ] Baca catatan serah terima dari shift sebelumnya (lihat `06-serah-terima-shift.md`)
- [ ] Cek kesehatan platform monitoring:
  - Apakah ada agent/collector yang **berhenti mengirim log**? (contoh Wazuh: cek status agent `Disconnected` / `Never connected`)
  - Apakah ada gap ingestion pada 8 jam terakhir?
  - Apakah indexer/storage mendekati kapasitas?
- [ ] Cek daftar tiket terbuka yang masih menjadi tanggung jawab shift ini
- [ ] Cek kalender maintenance / change request yang sedang berjalan (agar aktivitas sah tidak salah diklasifikasi)
- [ ] Cek kanal threat intel untuk kerentanan atau kampanye baru yang relevan

> **Penting:** hilangnya log dari sumber kritikal adalah **insiden**, bukan sekadar masalah teknis. Buka tiket dan eskalasi jika sumber Tier 0 berhenti mengirim log lebih dari 30 menit.

---

## 4. Alur Triage

```
        Alert masuk ke antrean
                 │
                 ▼
    [1] Ambil alert prioritas tertinggi
                 │
                 ▼
    [2] Pahami rule: apa yang sebenarnya dideteksi?
                 │
                 ▼
    [3] Kumpulkan konteks (5W1H)
                 │
                 ▼
    [4] Enrichment: reputasi, aset, pengguna, riwayat
                 │
                 ▼
    [5] Tentukan verdict
        ├── False Positive ──────► Tutup + catat alasan + usul tuning
        ├── Benign True Positive ► Tutup + catat + informasikan pemilik sistem
        └── True Positive / Ragu ► [6] Eskalasi ke L2
                 │
                 ▼
    [7] Dokumentasikan & lanjut ke alert berikutnya
```

### Langkah 1 — Ambil Alert Berdasarkan Prioritas

Urutan pengambilan:
1. Alert P1/P2 yang belum tersentuh
2. Alert yang mendekati batas SLA
3. Alert pada aset Tier 0
4. Sisanya berdasarkan urutan waktu masuk

Klaim tiket (assign ke diri sendiri) sebelum mengerjakan, agar tidak ada pekerjaan ganda.

### Langkah 2 — Pahami Rule yang Memicu

Sebelum menilai, jawab: **rule ini sebenarnya mendeteksi apa?**

- Baca deskripsi dan logika rule
- Cek pemetaan MITRE ATT&CK-nya
- Cek riwayat: berapa kali rule ini menyala 30 hari terakhir dan berapa persen FP?

Rule dengan FP rate tinggi tetap harus divalidasi, tetapi butuh konteks tambahan sebelum dieskalasi.

### Langkah 3 — Kumpulkan Konteks (5W1H)

| Pertanyaan | Yang dicari |
|------------|-------------|
| **What** | Apa aktivitas yang terdeteksi? Rule apa, teknik ATT&CK apa? |
| **Who** | Akun/pengguna mana? Apakah privileged? Apakah service account? |
| **Where** | Host mana, IP mana, segmen jaringan apa, aset tier berapa? |
| **When** | Kapan terjadi? Di dalam atau di luar jam kerja pengguna tersebut? |
| **Why** | Adakah penjelasan sah? Maintenance, pentest terjadwal, deployment? |
| **How** | Bagaimana aktivitas dilakukan? Proses induk apa? Command line apa? |

### Langkah 4 — Enrichment Dasar

| Objek | Sumber Pemeriksaan |
|-------|--------------------|
| IP publik | VirusTotal, AbuseIPDB, GreyNoise, WHOIS, geolokasi |
| Domain / URL | VirusTotal, URLScan, umur domain, kategori |
| File hash | VirusTotal, MalwareBazaar, database hash internal |
| Host internal | Asset inventory: pemilik, fungsi, tier, OS, patch level |
| Akun | Direktori HR/AD: jabatan, unit kerja, status aktif, hak akses |
| Riwayat | Apakah host/akun/IP ini pernah muncul di tiket sebelumnya? |

**Aturan penting:**
- Jangan pernah mengunggah file atau dokumen internal ke layanan analisis publik. Gunakan hash, atau sandbox internal.
- Jangan mengakses langsung URL berbahaya dari workstation kantor. Gunakan sandbox atau URLScan.
- Detection dari 1–2 vendor di VirusTotal **bukan** bukti kuat; lihat juga perilaku dan konteks.

### Langkah 5 — Tentukan Verdict

#### A. False Positive

Kriteria menutup sebagai FP (semua harus terpenuhi):
- Aktivitas terbukti berasal dari sumber sah yang teridentifikasi
- Ada bukti pendukung yang bisa diverifikasi ulang oleh analis lain
- Tidak ada indikator lain yang mencurigakan pada host/akun yang sama dalam 24 jam terakhir

Wajib dicatat: alasan, bukti, dan usulan tuning (jika rule terlalu berisik).

#### B. Benign True Positive

Aktivitas benar terjadi dan terdeteksi dengan tepat, namun sah. Contoh: scan kerentanan oleh tim internal, admin menjalankan PowerShell administratif.

Wajib: konfirmasi ke pemilik aktivitas melalui kanal resmi sebelum ditutup. **Jangan menutup hanya berdasarkan asumsi.**

#### C. True Positive / Ragu → Eskalasi

Eskalasi **wajib** bila memenuhi salah satu:
- Terkonfirmasi berbahaya atau kuat dugaannya
- Menyentuh aset Tier 0 atau akun privileged
- Ada indikasi eksekusi kode, persistence, atau koneksi keluar mencurigakan
- Muncul beberapa alert berbeda pada host/akun yang sama dalam waktu berdekatan
- L1 tidak mampu menyimpulkan dalam **30 menit** kerja aktif
- Alert P1/P2 apa pun bentuknya

### Langkah 6 — Prosedur Eskalasi

Lihat `05-matriks-eskalasi.md`. Ringkasnya:

1. Naikkan status tiket menjadi `ESCALATED` dan isi field wajib
2. Kirim notifikasi ke kanal L2 sesuai severity
3. Untuk P1: **telepon**, jangan hanya chat. Chat tidak dianggap terkirim sampai ada balasan.
4. Sampaikan ringkasan terstruktur:

```
[P?] <Judul singkat>
Host/Akun  : 
Waktu      : (WIB, dan rentang aktivitas)
Temuan     : (2–3 kalimat, apa yang terjadi)
Bukti      : (link tiket, query, ID alert)
Enrichment : (hasil reputasi & konteks aset)
Sudah dilakukan : (tindakan yang sudah diambil L1)
Kekhawatiran    : (kenapa ini dieskalasi)
```

5. Tetap standby sampai L2 mengonfirmasi pengambilalihan. **L1 tidak melepas tanggung jawab sebelum ada konfirmasi.**

### Langkah 7 — Dokumentasi

Setiap tiket wajib memuat minimal:
- ID alert dan rule yang memicu
- Timeline aktivitas (waktu WIB dan UTC)
- Aset/akun terdampak
- Langkah verifikasi yang dilakukan, termasuk **query yang dipakai**
- Hasil enrichment beserta sumbernya
- Verdict dan justifikasinya
- Tindakan yang dilakukan dan waktunya

Gunakan `templates/tiket-insiden.md`.

---

## 5. Tindakan Respons Pre-Approved untuk L1

Tindakan berikut boleh dilakukan L1 **tanpa menunggu persetujuan**, dan wajib dicatat di tiket:

| Tindakan | Syarat |
|----------|--------|
| Blokir IP publik yang jelas berbahaya di firewall perimeter | IP tidak masuk allowlist, bukan IP mitra/vendor, bukan CDN atau cloud provider besar |
| Karantina email phishing dari seluruh mailbox | Sudah terkonfirmasi phishing lewat analisis header dan URL |
| Blokir URL/domain berbahaya di proxy/DNS filter | Terkonfirmasi malicious oleh minimal 2 sumber independen |
| Menghubungi pengguna untuk konfirmasi aktivitas | Gunakan kanal resmi; jangan minta kredensial dalam bentuk apa pun |

**Yang dilarang keras untuk L1:**
- Mengisolasi atau mematikan endpoint/server
- Menonaktifkan atau mereset akun
- Menghapus file yang dicurigai malware (bukti bisa hilang)
- Login ke host terdampak menggunakan kredensial administratif (berisiko credential theft)
- Mengubah atau menonaktifkan rule deteksi
- Berkomunikasi dengan pihak eksternal tentang insiden

> Larangan login administratif penting: jika host sudah dikompromi, kredensial yang Anda ketikkan di sana ikut tercuri.

---

## 6. Monitoring Berkelanjutan Selama Shift

Selain triage antrean, L1 melakukan pemeriksaan berkala:

| Interval | Aktivitas |
|----------|-----------|
| Tiap 30 menit | Refresh dashboard alert, cek antrean baru, cek SLA yang mendekati batas |
| Tiap 2 jam | Cek kesehatan agent/log source, cek trafik anomali, cek autentikasi gagal massal |
| Tiap 4 jam | Cek tren volume alert, laporkan lonjakan tidak wajar ke L2 |
| Akhir shift | Susun handover, pastikan semua tiket punya status yang benar |

---

## 7. Kesalahan Umum yang Harus Dihindari

| Kesalahan | Dampak | Yang benar |
|-----------|--------|------------|
| Menutup alert karena "biasanya FP" | True positive lolos | Validasi ulang setiap kali, riwayat hanya konteks |
| Menilai hanya dari satu alert | Kehilangan gambaran serangan | Cek aktivitas lain pada host/akun yang sama |
| Mengandalkan skor VirusTotal saja | FP dan FN keduanya tinggi | Kombinasikan dengan konteks perilaku |
| Dokumentasi seadanya | Investigasi L2 melambat, audit gagal | Isi tiket sesuai template |
| Menunda eskalasi karena takut salah | SLA terlampaui, dampak meluas | Eskalasi dini tidak pernah dinilai sebagai kesalahan |
| Bilang "sudah saya chat" tanpa cek balasan | Insiden P1 tidak tertangani | Untuk P1 wajib konfirmasi verbal |

---

## 8. Prosedur Akhir Shift

- [ ] Semua tiket yang dikerjakan sudah punya status akhir yang benar
- [ ] Tiket yang belum selesai diberi catatan progres yang cukup untuk dilanjutkan orang lain
- [ ] Isi form serah terima
- [ ] Sampaikan handover secara verbal ke analis shift berikutnya
- [ ] Laporkan kendala tools atau kebutuhan tuning ke L2/SOC Manager
