# SOC Standard Operating Procedure — Level 1 & Level 2

Kumpulan Standard Operating Procedure (SOP), playbook, dan template kerja untuk tim **Security Operations Center (SOC)** pada tingkat **Level 1 (Monitoring & Triage)** dan **Level 2 (Investigation & Response)**.

Repositori ini ditulis agar bisa langsung diadopsi oleh organisasi yang menjalankan SOC internal maupun managed SOC, dengan referensi teknis ke stack open source (Wazuh, Suricata, TheHive, MISP) namun tetap tool-agnostic pada bagian prosesnya.

---

## Daftar Isi

### Dokumen Inti (`docs/`)

| No | Dokumen | Ringkasan |
|----|---------|-----------|
| 00 | [Glosarium & Singkatan](docs/00-glosarium.md) | Definisi istilah yang dipakai di seluruh SOP |
| 01 | [Struktur SOC & Peran](docs/01-struktur-soc-dan-peran.md) | Tier model, RACI, batas kewenangan tiap level |
| 02 | [Klasifikasi Severity & SLA](docs/02-klasifikasi-severity-dan-sla.md) | Matriks P1–P5, target waktu respons dan penyelesaian |
| 03 | [SOP Level 1 — Monitoring & Triage](docs/03-sop-l1-monitoring-triage.md) | Prosedur harian analis L1 dari awal hingga akhir shift |
| 04 | [SOP Level 2 — Investigation & Response](docs/04-sop-l2-investigasi-respons.md) | Investigasi mendalam, containment, eradication, recovery |
| 05 | [Matriks Eskalasi](docs/05-matriks-eskalasi.md) | Siapa dihubungi, kapan, lewat kanal apa |
| 06 | [Serah Terima Shift](docs/06-serah-terima-shift.md) | Prosedur handover antar shift 24/7 |
| 07 | [Pelaporan & Dokumentasi](docs/07-pelaporan-dan-dokumentasi.md) | Standar penulisan tiket, laporan, dan chain of custody |
| 08 | [Metrik & KPI](docs/08-metrik-dan-kpi.md) | MTTD, MTTR, false positive rate, dan cara mengukurnya |

### Playbook Insiden (`playbooks/`)

| Kode | Playbook |
|------|----------|
| PB-01 | [Phishing & Email Berbahaya](playbooks/PB-01-phishing.md) |
| PB-02 | [Malware / Ransomware pada Endpoint](playbooks/PB-02-malware-endpoint.md) |
| PB-03 | [Brute Force & Account Compromise](playbooks/PB-03-bruteforce-account-compromise.md) |
| PB-04 | [Serangan Aplikasi Web](playbooks/PB-04-serangan-web.md) |
| PB-05 | [Data Exfiltration & Insider Threat](playbooks/PB-05-data-exfiltration.md) |

### Template (`templates/`)

- [Tiket Insiden](templates/tiket-insiden.md)
- [Laporan Insiden (Post-Incident Report)](templates/laporan-insiden.md)
- [Form Serah Terima Shift](templates/handover-shift.md)

---

## Ringkasan Pembagian Peran

```
                 ┌─────────────────────────────────────────┐
   Alert  ─────► │  L1 — Monitoring & Triage               │
                 │  Validasi, klasifikasi, enrichment dasar │
                 └───────────────┬─────────────────────────┘
                                 │ eskalasi (true positive / ragu)
                                 ▼
                 ┌─────────────────────────────────────────┐
                 │  L2 — Investigation & Response          │
                 │  Analisis mendalam, containment, RCA    │
                 └───────────────┬─────────────────────────┘
                                 │ eskalasi (insiden besar / forensik)
                                 ▼
                 ┌─────────────────────────────────────────┐
                 │  L3 / CSIRT / Manajemen                 │
                 └─────────────────────────────────────────┘
```

**Prinsip dasar:** L1 **tidak menutup** alert yang meragukan — jika ragu, eskalasi. L2 **tidak menunggu** eskalasi — L2 juga melakukan threat hunting proaktif.

---

## Cara Menggunakan Repositori Ini

1. **Fork / clone** repositori ini ke organisasi Anda.
2. Sesuaikan bagian yang ditandai `<< SESUAIKAN >>` — nama tim, kanal komunikasi, nama tools, nomor kontak.
3. Sesuaikan matriks severity dan SLA di `docs/02` dengan risk appetite organisasi.
4. Review ulang minimal **setiap 6 bulan** atau setelah insiden major (lihat bagian Tata Kelola Dokumen).
5. Setiap perubahan SOP dilakukan lewat Pull Request agar ada jejak audit.

```bash
git clone https://github.com/<org>/soc-sop.git
cd soc-sop
```

---

## Tata Kelola Dokumen

| Item | Keterangan |
|------|------------|
| Pemilik dokumen | SOC Manager `<< SESUAIKAN >>` |
| Reviewer | Head of Security / CISO |
| Siklus review | 6 bulan, atau setelah insiden P1/P2 |
| Mekanisme perubahan | Pull Request + approval minimal 1 reviewer |
| Klasifikasi | Internal Use Only |
| Versi | 1.0.0 |

**Riwayat versi** dicatat di [CHANGELOG.md](CHANGELOG.md).

---

## Referensi & Standar Acuan

- NIST SP 800-61 Rev. 2 — *Computer Security Incident Handling Guide*
- NIST Cybersecurity Framework 2.0
- ISO/IEC 27035 — *Information Security Incident Management*
- SANS Incident Handler's Handbook (PICERL)
- MITRE ATT&CK Framework
- ISO/IEC 27001:2022 Annex A (kontrol A.5.24 – A.5.28)
- UU No. 27 Tahun 2022 tentang Pelindungan Data Pribadi (kewajiban notifikasi kebocoran data)
- Peraturan BSSN terkait pengelolaan insiden siber
- SKKNI Nomor 23 Tahun 2022 — Uji Keamanan Siber

---

## Kontribusi

Lihat [CONTRIBUTING.md](CONTRIBUTING.md). Singkatnya: satu PR untuk satu perubahan logis, gunakan bahasa yang konsisten (Bahasa Indonesia dengan istilah teknis dibiarkan dalam bahasa Inggris), dan jangan pernah memasukkan data insiden nyata, IP internal, atau kredensial ke dalam repositori ini.

## Lisensi

Dokumentasi ini dirilis di bawah lisensi [CC BY-SA 4.0](LICENSE) kecuali dinyatakan lain. Silakan diadaptasi dengan tetap mencantumkan atribusi.

## Disclaimer

Dokumen ini bersifat **template dan panduan umum**. Setiap organisasi wajib menyesuaikannya dengan arsitektur, regulasi, dan risk appetite masing-masing. Penulis tidak bertanggung jawab atas penerapan yang tidak sesuai konteks organisasi Anda.
