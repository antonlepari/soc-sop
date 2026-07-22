# 05 — Matriks Eskalasi

## 1. Prinsip

- Eskalasi **bukan tanda kegagalan**. Eskalasi terlambat yang merupakan kegagalan.
- Eskalasi belum selesai sampai **penerima mengonfirmasi** sudah mengambil alih.
- Untuk P1, kanal tertulis saja **tidak cukup** — wajib kontak verbal.

## 2. Matriks Eskalasi Utama

| Severity | Dari → Ke | Batas Waktu | Kanal Utama | Kanal Cadangan |
|----------|-----------|-------------|-------------|----------------|
| **P1** | L1 → L2 | Segera (< 15 menit) | Telepon + tag di kanal `#soc-incident` | WhatsApp/SMS ke nomor on-call |
| **P1** | L2 → SOC Manager + CSIRT | < 30 menit sejak konfirmasi | Telepon | Eskalasi ke atasan berikutnya |
| **P2** | L1 → L2 | < 30 menit | Kanal chat `#soc-l2` + tiket | Telepon bila tidak dibalas 15 menit |
| **P2** | L2 → SOC Manager | < 2 jam bila belum contained | Chat + ringkasan tiket | Telepon |
| **P3** | L1 → L2 | < 2 jam bila tidak selesai | Tiket + chat | — |
| **P4/P5** | L1 → L2 | Akhir shift | Catatan handover | — |

## 3. Aturan No-Response

Jika penerima eskalasi tidak merespons dalam batas waktu:

```
Percobaan 1: kanal utama          → tunggu sesuai batas waktu severity
Percobaan 2: kanal cadangan       → tunggu 50% dari batas waktu
Percobaan 3: naik satu level      → hubungi SOC Manager
Percobaan 4: hubungi Head of Security / CISO
```

Seluruh percobaan dicatat di tiket dengan waktu. **L1 tidak boleh berhenti** hanya karena L2 tidak menjawab.

## 4. Daftar Kontak

> Isi tabel ini pada repositori internal. **Jangan pernah commit nomor telepon atau email pribadi ke repositori publik.**

| Peran | Nama | Kontak Utama | Kontak Cadangan | Jam Ketersediaan |
|-------|------|--------------|-----------------|------------------|
| L2 On-Call | `<< SESUAIKAN >>` | | | 24/7 |
| SOC Manager | | | | 24/7 |
| Head of Security / CISO | | | | Jam kerja + on-call P1 |
| IT Infrastructure Lead | | | | |
| Network Team | | | | |
| Application Owner (per sistem) | | | | |
| Legal & Compliance | | | | Jam kerja |
| HR (kasus insider) | | | | Jam kerja |
| Humas / Corporate Comms | | | | Jam kerja |
| Vendor SIEM/EDR | | | | Sesuai kontrak |
| Penyedia layanan cloud | | | | |

## 5. Eskalasi ke Pihak Eksternal

Hanya boleh dilakukan oleh **Manajemen/Legal**, bukan analis SOC.

| Pihak | Kapan | Penanggung Jawab |
|-------|-------|------------------|
| BSSN / CSIRT Nasional (Gov-CSIRT) | Insiden signifikan pada infrastruktur informasi vital | SOC Manager + Manajemen |
| Otoritas pelindungan data | Kebocoran data pribadi — perhatikan tenggat notifikasi pada UU No. 27 Tahun 2022 | Legal & DPO |
| Kepolisian / penegak hukum | Dugaan tindak pidana | Legal + Manajemen |
| Pelanggan / mitra terdampak | Data mereka terdampak | Manajemen + Humas |
| Vendor / penyedia layanan | Insiden berasal dari produk atau layanan mereka | SOC Manager |
| Media | Selalu dan hanya lewat Humas | Humas |

> Analis SOC **dilarang** memberikan pernyataan ke pihak luar, termasuk di media sosial dan forum profesional.

## 6. Format Pesan Eskalasi

### 6.1 Eskalasi P1 (verbal + tertulis)

```
INSIDEN P1 — <judul singkat>
Waktu deteksi   : DD/MM/YYYY HH:MM WIB
Sistem terdampak: <hostname / layanan / akun>
Ringkasan       : <apa yang terjadi, 2 kalimat>
Status saat ini : <sedang berlangsung / sudah contained>
Dampak          : <layanan/data yang terpengaruh>
Tindakan diambil: <daftar singkat + waktu>
Yang dibutuhkan : <keputusan/approval/bantuan spesifik>
Tiket           : <link>
Penanggung jawab: <nama analis>
```

### 6.2 Update Berkala Selama Insiden P1

Kirim update **setiap 30 menit** ke kanal insiden meskipun tidak ada perkembangan. Format:

```
UPDATE #<n> — <HH:MM WIB>
Perkembangan   : 
Scope terkini  : 
Langkah berikut: 
Blocker        : 
```

## 7. Prosedur War Room (Insiden P1)

1. SOC Manager membuka kanal insiden khusus dan bridge call
2. Tunjuk **satu Incident Commander** — biasanya L2 senior atau SOC Manager
3. Tunjuk **satu Scribe** yang mencatat seluruh keputusan dan waktunya
4. Peran lain: Technical Lead, Communications Lead, Liaison ke pemilik sistem
5. Analis lain tetap menjalankan monitoring rutin — **jangan seluruh tim tersedot ke satu insiden**
6. Semua keputusan dicatat: apa, siapa yang memutuskan, jam berapa, alasannya

> Incident Commander mengoordinasi, tidak mengerjakan analisis teknis sendiri.
