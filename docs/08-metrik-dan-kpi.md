# 08 — Metrik & KPI SOC

## 1. Tujuan

Mengukur efektivitas SOC secara objektif, bukan sekadar menghitung volume aktivitas. Metrik dipakai untuk **perbaikan proses**, bukan untuk menghukum analis.

> Peringatan: metrik yang salah pilih akan mendorong perilaku yang salah. Menjadikan "jumlah tiket ditutup" sebagai KPI individu akan mendorong analis menutup tiket terburu-buru.

## 2. Metrik Inti

| Metrik | Definisi | Target | Cara Hitung |
|--------|----------|--------|-------------|
| **MTTD** | Rata-rata waktu dari aktivitas berbahaya terjadi hingga terdeteksi | `<< < 1 jam >>` | Waktu deteksi − waktu aktivitas pertama pada timeline |
| **MTTA** | Rata-rata waktu dari alert muncul hingga analis mulai mengerjakan | P1 < 15 mnt | Waktu triage dimulai − waktu alert |
| **MTTR (Respond)** | Rata-rata waktu dari deteksi hingga containment | P1 < 1 jam | Waktu containment − waktu deteksi |
| **MTTR (Resolve)** | Rata-rata waktu dari deteksi hingga insiden ditutup | Sesuai SLA severity | Waktu penutupan − waktu deteksi |
| **False Positive Rate** | Persentase alert yang ternyata FP | Turun tiap kuartal | FP ÷ total alert |
| **Escalation Accuracy** | Persentase eskalasi L1 yang dikonfirmasi valid oleh L2 | 70–90% | Eskalasi valid ÷ total eskalasi |
| **SLA Compliance** | Persentase insiden yang memenuhi SLA | ≥ 95% | Insiden sesuai SLA ÷ total insiden |
| **Coverage ATT&CK** | Persentase teknik relevan yang punya deteksi | Naik tiap kuartal | Teknik terdeteksi ÷ teknik dalam threat model |
| **Log Source Health** | Persentase sumber log yang aktif mengirim | ≥ 99% | Source aktif ÷ total source terdaftar |
| **Dwell Time** | Lama attacker berada di lingkungan sebelum terdeteksi | Sekecil mungkin | Waktu deteksi − waktu initial access |

### Catatan tentang Escalation Accuracy

Target **bukan 100%**. Akurasi 100% berarti L1 terlalu konservatif dan kemungkinan besar sedang menutup true positive. Akurasi di bawah 50% menandakan L1 butuh pelatihan atau rule terlalu berisik.

## 3. Metrik Kualitas

| Metrik | Cara Ukur |
|--------|-----------|
| Kelengkapan dokumentasi tiket | Sampling QA mingguan terhadap 10% tiket |
| Kualitas eskalasi | Apakah L2 perlu bertanya ulang informasi dasar? |
| Rasio hunting terhadap alert-driven | Berapa insiden ditemukan lewat hunting, bukan alert? |
| Recurrence rate | Berapa insiden yang berulang dengan akar penyebab yang sama? |
| Action item closure | Persentase rekomendasi lessons learned yang benar-benar selesai |

**Recurrence rate** adalah metrik paling jujur: insiden yang berulang menandakan RCA tidak pernah benar-benar diselesaikan.

## 4. Metrik yang Perlu Dihindari sebagai KPI Individu

| Metrik | Mengapa berbahaya |
|--------|-------------------|
| Jumlah tiket ditutup per analis | Mendorong penutupan terburu-buru |
| Waktu penanganan rata-rata per analis | Mendorong investigasi dangkal |
| Jumlah alert diproses | Kuantitas menggantikan kualitas |
| Jumlah eskalasi (semakin sedikit semakin baik) | Mendorong analis menahan eskalasi |

Metrik-metrik ini tetap boleh dipantau di level **tim** untuk kapasitas, bukan untuk penilaian individu.

## 5. Pelaporan

| Laporan | Frekuensi | Isi |
|---------|-----------|-----|
| Dashboard operasional | Real time | Antrean alert, SLA berjalan, insiden aktif |
| Laporan mingguan | Mingguan | Volume alert, FP rate, insiden per kategori, tuning yang dilakukan |
| Laporan bulanan | Bulanan | Seluruh metrik inti, tren, insiden signifikan, status action item |
| Laporan kuartalan | Kuartalan | Coverage deteksi, maturity assessment, kebutuhan sumber daya |

## 6. Review Berkala

- **Mingguan** — review antrean, backlog, dan tuning bersama tim
- **Bulanan** — review metrik dan tren bersama SOC Manager
- **Kuartalan** — review target metrik, apakah masih relevan dengan risiko organisasi
- **Tahunan** — maturity assessment SOC (misalnya menggunakan SOC-CMM)
