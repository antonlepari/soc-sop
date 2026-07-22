# 07 — Pelaporan & Dokumentasi

## 1. Tujuan

Menetapkan standar dokumentasi agar setiap tiket dan laporan dapat dipahami, diverifikasi ulang, dan dipertanggungjawabkan secara audit maupun hukum.

## 2. Prinsip Penulisan

1. **Tulis untuk orang yang belum tahu apa-apa.** Analis shift berikutnya, auditor, atau pengadilan tidak punya konteks Anda.
2. **Pisahkan fakta dari opini.** Tulis "log menunjukkan X", bukan "sepertinya attacker melakukan X" — kecuali ditandai jelas sebagai hipotesis.
3. **Sertakan cara memverifikasi.** Cantumkan query yang dipakai, bukan hanya kesimpulannya.
4. **Gunakan waktu absolut.** "14:32 WIB (07:32 UTC), 22 Juli 2026", bukan "tadi siang".
5. **Konsisten dalam penamaan.** Satu host punya satu nama di seluruh dokumen.

## 3. Standar Waktu

- Zona waktu operasional: **WIB (UTC+7)**
- Setiap timestamp pada timeline ditulis ganda: **WIB dan UTC**
- Format: `DD/MM/YYYY HH:MM:SS`
- Pastikan seluruh sumber log tersinkronisasi NTP — perbedaan waktu antar sumber wajib dicatat

## 4. Tingkatan Dokumen

| Dokumen | Dibuat oleh | Kapan | Pembaca |
|---------|-------------|-------|---------|
| Tiket insiden | L1 / L2 | Setiap alert yang ditriage | Internal SOC |
| Laporan insiden (PIR) | L2 | Setiap insiden P1/P2 | SOC Manager, CISO, pemilik sistem |
| Executive summary | SOC Manager | Insiden P1 | Manajemen, direksi |
| Laporan shift | L1 | Setiap akhir shift | Shift berikutnya |
| Laporan bulanan SOC | SOC Manager | Bulanan | Manajemen |
| Laporan hunting | L2 | Setiap siklus hunting | Internal SOC |

## 5. Isi Minimum Tiket Insiden

- ID tiket dan ID alert sumber
- Waktu deteksi, waktu triage dimulai, waktu eskalasi, waktu penutupan
- Severity awal dan akhir (beserta alasan bila berubah)
- Aset dan akun terdampak (hostname, IP, username, tier aset)
- Deskripsi temuan
- Langkah verifikasi yang dilakukan, termasuk query
- Hasil enrichment beserta sumber dan waktunya
- Verdict dan justifikasi
- Tindakan yang diambil, siapa yang melakukan, kapan
- Lampiran bukti (screenshot, ekspor log, hash artefak)

## 6. Struktur Laporan Insiden (PIR)

Gunakan `templates/laporan-insiden.md`. Struktur bakunya:

1. **Executive Summary** — maksimal 1 halaman, bahasa non-teknis, jawab: apa yang terjadi, seberapa parah, sudah selesai atau belum, apa langkah ke depan
2. **Kronologi** — timeline lengkap dengan bukti
3. **Analisis Teknis** — initial access, eksekusi, persistence, lateral movement, dampak, pemetaan ATT&CK
4. **Dampak** — sistem, data, layanan, estimasi finansial bila ada
5. **Tindakan Penanganan** — containment, eradication, recovery beserta waktunya
6. **Root Cause Analysis** — akar penyebab, bukan gejala
7. **Indicators of Compromise** — daftar IOC dalam format yang bisa dipakai ulang
8. **Rekomendasi** — dengan prioritas, penanggung jawab, dan tenggat
9. **Lampiran** — bukti pendukung, chain of custody

## 7. Root Cause Analysis

Gunakan teknik **5 Why** untuk menembus gejala:

```
Insiden : Endpoint terinfeksi ransomware
Why 1   : Pengguna menjalankan lampiran berbahaya
Why 2   : Lampiran lolos dari email gateway
Why 3   : Filter tidak memblokir arsip terenkripsi berisi executable
Why 4   : Kebijakan lampiran belum diperbarui sejak 2023
Why 5   : Tidak ada proses review berkala kebijakan email gateway
──────────────────────────────────────────────────────────────
Akar penyebab: tidak ada siklus review kontrol keamanan email
```

Perhatikan: akar penyebab yang berhenti di "human error" hampir selalu **belum cukup dalam**. Tanyakan mengapa sistem membiarkan kesalahan manusia berdampak sebesar itu.

## 8. Penanganan Bukti Digital

### Chain of Custody

Setiap artefak dicatat dalam tabel:

| No | Artefak | Sumber | Diambil oleh | Waktu (WIB) | Metode/Tools | SHA-256 | Lokasi Simpan |
|----|---------|--------|--------------|-------------|--------------|---------|---------------|

### Aturan

- Hitung hash segera setelah akuisisi, verifikasi ulang setiap kali dipindahkan
- Bekerja hanya pada **salinan**, tidak pernah pada bukti asli
- Simpan di lokasi dengan akses terbatas dan log akses aktif
- Retensi bukti insiden P1/P2: minimal **1 tahun** `<< SESUAIKAN dengan kebijakan retensi dan kebutuhan hukum >>`
- Setiap serah terima bukti dicatat

## 9. Klasifikasi dan Distribusi Dokumen

| Klasifikasi | Isi | Distribusi |
|-------------|-----|------------|
| Restricted | Detail teknis insiden aktif, kredensial, bukti forensik | Tim penanganan insiden saja |
| Confidential | Laporan insiden lengkap | SOC, CISO, pemilik sistem terkait |
| Internal | Ringkasan, statistik, lessons learned yang dianonimkan | Seluruh karyawan terkait |
| Public | Pengumuman resmi | Hanya lewat Humas |

Gunakan **TLP (Traffic Light Protocol)** saat berbagi informasi ancaman dengan pihak eksternal.

## 10. Yang Tidak Boleh Masuk Repositori atau Dokumen Publik

- Kredensial, token, API key — meski sudah tidak aktif
- Alamat IP internal, hostname produksi, skema jaringan
- Data pribadi karyawan atau pelanggan
- Detail insiden nyata yang belum di-sanitasi
- Screenshot yang belum diredaksi
