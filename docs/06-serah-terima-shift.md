# 06 — Serah Terima Shift (Handover)

## 1. Tujuan

Memastikan tidak ada insiden, tiket, atau konteks penting yang hilang saat pergantian shift.

## 2. Prinsip

- Handover dilakukan **tertulis dan verbal**. Tertulis saja rawan salah tafsir; verbal saja tidak meninggalkan jejak.
- Shift yang menyerahkan **tetap bertanggung jawab** sampai shift penerima mengonfirmasi.
- Overlap minimal **15 menit** antara dua shift.

## 3. Waktu

| Pergantian | Jam Overlap (WIB) |
|------------|-------------------|
| Malam → Pagi | 06:45 – 07:00 |
| Pagi → Sore | 14:45 – 15:00 |
| Sore → Malam | 22:45 – 23:00 |

## 4. Isi Wajib Handover

1. **Insiden aktif** — status, severity, siapa yang menangani, apa langkah berikutnya
2. **Tiket menggantung** — yang menunggu balasan pihak lain, dengan tenggat
3. **Masalah platform** — log source mati, agent disconnected, dashboard error
4. **Aktivitas terjadwal** — maintenance, deployment, pentest yang sedang atau akan berjalan
5. **Konteks ancaman** — kampanye baru, IOC baru yang perlu dipantau
6. **Tren tidak biasa** — lonjakan alert, pola berulang yang belum jelas
7. **Hal administratif** — perubahan personel, akses, atau prosedur

## 5. Prosedur

### Shift yang Menyerahkan

- [ ] Selesaikan atau beri catatan progres pada semua tiket yang dikerjakan
- [ ] Pastikan status tiket akurat (jangan tinggalkan tiket `IN_TRIAGE` tanpa catatan)
- [ ] Isi form handover (`templates/handover-shift.md`)
- [ ] Sampaikan secara verbal, fokus pada yang aktif dan berisiko
- [ ] Jawab pertanyaan shift penerima
- [ ] Jangan meninggalkan posisi sebelum handover dikonfirmasi diterima

### Shift yang Menerima

- [ ] Baca form handover sebelum sesi verbal
- [ ] Tanyakan hal yang tidak jelas — **jangan berasumsi**
- [ ] Verifikasi sendiri status insiden aktif, jangan hanya percaya laporan
- [ ] Jalankan checklist awal shift (lihat `03-sop-l1-monitoring-triage.md`)
- [ ] Konfirmasi penerimaan handover secara eksplisit

## 6. Escalation Saat Handover

Jika insiden P1 sedang berlangsung saat pergantian shift:

1. **Jangan lakukan handover mendadak.** Analis yang menangani tetap melanjutkan sampai titik aman atau ada penggantian terstruktur.
2. Handover insiden P1 dilakukan dengan penjelasan penuh timeline, bukan ringkasan.
3. SOC Manager diberi tahu bahwa terjadi pergantian penanganan di tengah insiden.
4. Incident Commander baru mengonfirmasi pemahaman timeline sebelum mengambil alih.

## 7. Kesalahan Umum

| Kesalahan | Akibat |
|-----------|--------|
| "Tidak ada apa-apa, aman" tanpa cek tiket | Insiden terlewat berjam-jam |
| Handover hanya lewat chat tanpa tatap muka/panggilan | Konteks penting hilang |
| Menunda dokumentasi ke shift berikutnya | Rekonstruksi timeline jadi sulit |
| Menyerahkan insiden aktif tanpa penjelasan lengkap | Penanganan mundur ke titik awal |
