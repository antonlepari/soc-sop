# 02 — Klasifikasi Severity & SLA

## 1. Tujuan

Menyediakan kriteria objektif untuk menentukan tingkat keparahan insiden, sehingga prioritas penanganan konsisten antar analis dan antar shift.

## 2. Rumus Prioritas

Severity ditentukan oleh kombinasi **Dampak** dan **Urgensi**:

```
Severity = f(Dampak Bisnis, Urgensi/Kecepatan Penyebaran)
```

### 2.1 Skala Dampak

| Level | Dampak |
|-------|--------|
| Tinggi | Sistem kritikal/produksi, data pribadi atau rahasia, dampak finansial atau reputasi signifikan, layanan pelanggan terhenti |
| Sedang | Sistem pendukung, data internal non-sensitif, sebagian pengguna terdampak |
| Rendah | Sistem non-kritikal, tidak ada data sensitif, dampak terbatas pada satu pengguna |

### 2.2 Skala Urgensi

| Level | Urgensi |
|-------|---------|
| Tinggi | Serangan aktif sedang berlangsung, penyebaran cepat, adversary punya akses interaktif |
| Sedang | Aktivitas mencurigakan terkonfirmasi namun belum meluas |
| Rendah | Percobaan gagal, aktivitas historis, atau temuan pasif |

### 2.3 Matriks Penentuan

| Dampak \ Urgensi | Tinggi | Sedang | Rendah |
|------------------|--------|--------|--------|
| **Tinggi** | P1 | P1 | P2 |
| **Sedang** | P2 | P3 | P3 |
| **Rendah** | P3 | P4 | P4 |

## 3. Definisi Severity & SLA

| Kode | Nama | Contoh Kasus | Waktu Respons | Target Penyelesaian | Eskalasi Otomatis |
|------|------|--------------|---------------|---------------------|-------------------|
| **P1** | Critical | Ransomware aktif, kompromi domain controller, kebocoran data pelanggan, defacement situs publik, C2 aktif ke server produksi | **15 menit** | 4 jam | Langsung ke L2 + SOC Manager + CSIRT |
| **P2** | High | Malware pada endpoint pengguna, account compromise terkonfirmasi, eksploitasi kerentanan berhasil, privilege escalation | **30 menit** | 8 jam | L2 dalam 30 menit |
| **P3** | Medium | Brute force gagal berulang, policy violation, port scan terarah, phishing yang belum diklik | **2 jam** | 24 jam | L2 bila L1 tidak selesai dalam 2 jam |
| **P4** | Low | Scanning otomatis dari internet, alert informatif, anomali minor | **8 jam** | 72 jam | Tidak otomatis |
| **P5** | Informational | Log audit, perubahan konfigurasi terjadwal, alert untuk tuning | Best effort | — | Tidak |

> Waktu respons dihitung sejak alert muncul di antrean SOC, bukan sejak analis membukanya.

## 4. Aturan Naik/Turun Severity

Severity **wajib dinaikkan** bila ditemukan salah satu kondisi berikut:

- Aset terdampak adalah crown jewel (lihat daftar aset kritikal `<< SESUAIKAN >>`)
- Terbukti ada akses interaktif adversary (shell, RDP, RAT)
- Ditemukan lateral movement
- Ada indikasi data keluar dari lingkungan (exfiltration)
- Melibatkan akun dengan privilege tinggi (domain admin, root, service account kritikal)
- Terdapat kewajiban notifikasi regulator (data pribadi terdampak — UU PDP)

Severity boleh **diturunkan** hanya oleh L2 ke atas, dengan justifikasi tertulis pada tiket.

## 5. Daftar Aset Kritikal

Daftar ini menentukan bobot dampak. Wajib direview tiap kuartal bersama pemilik sistem.

| Kategori | Contoh Sistem | Tier |
|----------|---------------|------|
| Identity | Active Directory, IdP/SSO, PAM | Tier 0 |
| Data | Database pelanggan, data warehouse, file server HR/Finance | Tier 0 |
| Core Business | `<< SESUAIKAN >>` | Tier 1 |
| Infrastruktur | Hypervisor, backup server, SIEM itu sendiri | Tier 0 |
| Publik | Website, mobile API gateway | Tier 1 |
| Pendukung | Intranet, tools internal | Tier 2 |

> Insiden yang menyentuh **Tier 0** minimal berstatus **P2**, tidak boleh lebih rendah.

## 6. Jam Kerja dan SLA

SLA berlaku **24/7** untuk P1 dan P2. Untuk P3 ke bawah, perhitungan SLA menggunakan jam kerja `<< SESUAIKAN: 08:00–17:00 WIB, Senin–Jumat >>`.
