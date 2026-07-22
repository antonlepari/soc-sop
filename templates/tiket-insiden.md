# Template — Tiket Insiden

> Salin isi di bawah ini ke sistem ticketing. Kolom bertanda **(wajib)** tidak boleh dikosongkan sebelum tiket ditutup.

---

## Identitas Tiket

| Field | Isi |
|-------|-----|
| ID Tiket **(wajib)** | |
| ID Alert / Rule | |
| Judul singkat **(wajib)** | |
| Severity awal **(wajib)** | P__ |
| Severity akhir | P__ |
| Kategori | Phishing / Malware / Account Compromise / Web Attack / Exfiltration / Lainnya |
| Playbook yang dipakai | PB-__ |
| Analis L1 **(wajib)** | |
| Analis L2 | |
| Status **(wajib)** | NEW / IN_TRIAGE / ESCALATED / IN_PROGRESS / CONTAINED / RESOLVED / CLOSED_FP / CLOSED_BENIGN / ON_HOLD |

## Waktu

| Peristiwa | Waktu (WIB) | Waktu (UTC) |
|-----------|-------------|-------------|
| Aktivitas pertama (dari timeline) | | |
| Alert terdeteksi **(wajib)** | | |
| Triage dimulai **(wajib)** | | |
| Dieskalasi ke L2 | | |
| Containment | | |
| Ditutup **(wajib)** | | |

## Aset & Akun Terdampak

| Hostname | IP | Tier | Pemilik Sistem | OS |
|----------|----|------|----------------|----|
| | | | | |

| Username | Jabatan / Unit | Privileged? | Status |
|----------|----------------|-------------|--------|
| | | Ya / Tidak | |

## Deskripsi Temuan **(wajib)**

*Jelaskan apa yang terjadi dalam 3–5 kalimat. Fakta dulu, hipotesis belakangan dan ditandai jelas.*

## Timeline

| Waktu (WIB) | Waktu (UTC) | Host/Akun | Aktivitas | Sumber Bukti | ATT&CK |
|-------------|-------------|-----------|-----------|--------------|--------|
| | | | | | |

## Langkah Verifikasi **(wajib)**

*Tuliskan query yang dipakai agar analis lain bisa mereproduksi hasil.*

```
# Query SIEM:


# Hasil:

```

## Hasil Enrichment

| Objek | Jenis | Sumber Pemeriksaan | Hasil | Waktu Cek |
|-------|-------|--------------------|-------|-----------|
| | IP / Domain / Hash / URL | VirusTotal / AbuseIPDB / dll | | |

## Indicators of Compromise

```
# IP:

# Domain:

# Hash (SHA-256):

# URL:

# Lainnya:
```

## Verdict **(wajib)**

- [ ] True Positive — berbahaya
- [ ] Benign True Positive — aktivitas nyata namun sah
- [ ] False Positive
- [ ] Belum dapat disimpulkan → dieskalasi

**Justifikasi (wajib):**


## Tindakan yang Diambil

| Waktu (WIB) | Tindakan | Pelaksana | Approval oleh |
|-------------|----------|-----------|---------------|
| | | | |

## Tindak Lanjut

- [ ] Usulan tuning rule: 
- [ ] Usulan deteksi baru: 
- [ ] Rekomendasi ke pemilik sistem: 
- [ ] Perlu laporan insiden (PIR)? Ya / Tidak

## Lampiran

| No | Nama Berkas | SHA-256 | Keterangan |
|----|-------------|---------|------------|
| | | | |
