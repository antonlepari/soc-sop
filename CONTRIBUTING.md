# Panduan Kontribusi

Terima kasih sudah ikut merawat dokumen ini. SOP yang tidak pernah diperbarui akan cepat menjadi beban, bukan bantuan.

## Aturan Utama

### 1. Jangan pernah commit data nyata

Dilarang keras memasukkan ke repositori ini:

- Kredensial, token, API key, private key — **meski sudah tidak aktif**
- Alamat IP internal, hostname produksi, diagram jaringan asli
- Data pribadi karyawan atau pelanggan
- Detail insiden nyata yang belum disanitasi
- Screenshot yang belum diredaksi
- Nomor telepon dan email personel

Gunakan placeholder: `<< SESUAIKAN >>`, `contoh.internal`, `10.0.0.0/8`, `user@example.com`.

> Riwayat Git bersifat permanen. Menghapus file di commit berikutnya **tidak** menghapus isinya dari history.

### 2. Satu Pull Request untuk satu perubahan logis

Jangan menggabungkan perbaikan typo dengan perubahan prosedur di satu PR yang sama.

### 3. Perubahan prosedur butuh persetujuan

| Jenis perubahan | Approval minimum |
|-----------------|------------------|
| Typo, format, tautan rusak | 1 reviewer |
| Penambahan/klarifikasi konten | 1 reviewer (L2 ke atas) |
| Perubahan prosedur, SLA, atau matriks kewenangan | SOC Manager |
| Perubahan matriks eskalasi | SOC Manager + Head of Security |

## Konvensi Penulisan

- **Bahasa:** Bahasa Indonesia. Istilah teknis yang sudah baku dibiarkan dalam bahasa Inggris (*containment*, *threat hunting*, *false positive*) — jangan dipaksa diterjemahkan.
- **Sudut pandang:** instruksional dan langsung. Gunakan "lakukan", bukan "sebaiknya dilakukan", untuk langkah wajib.
- **Checklist:** gunakan `- [ ]` untuk langkah yang harus dieksekusi berurutan.
- **Placeholder:** tandai dengan `<< SESUAIKAN >>` agar mudah dicari saat adopsi.
- **Waktu:** selalu sebutkan zona waktu.
- **Tabel:** untuk matriks keputusan dan referensi; **prosa/checklist** untuk prosedur.

## Penamaan Berkas

```
docs/NN-nama-dokumen.md          # NN = urutan dua digit
playbooks/PB-NN-nama-kasus.md    # PB = playbook
templates/nama-template.md
```

Gunakan huruf kecil dan tanda hubung. Hindari spasi dan karakter khusus.

## Format Commit Message

```
<tipe>(<scope>): <ringkasan singkat>

<penjelasan bila perlu>
```

Tipe yang dipakai:

| Tipe | Untuk |
|------|-------|
| `feat` | Dokumen atau bagian baru |
| `fix` | Perbaikan isi yang salah |
| `docs` | Perbaikan penulisan, typo, format |
| `refactor` | Restrukturisasi tanpa mengubah makna |
| `chore` | Housekeeping repositori |

Contoh:

```
feat(playbooks): tambah PB-06 untuk insiden cloud misconfiguration
fix(docs/02): koreksi SLA respons P2 dari 60 menit menjadi 30 menit
```

## Checklist Sebelum Membuka PR

- [ ] Tidak ada data nyata, kredensial, atau informasi internal
- [ ] Seluruh tautan internal berfungsi
- [ ] Konsisten dengan istilah di `docs/00-glosarium.md`
- [ ] Placeholder ditandai `<< SESUAIKAN >>`
- [ ] `CHANGELOG.md` sudah diperbarui untuk perubahan substantif
- [ ] Perubahan sudah dibaca ulang dari sudut pandang analis baru yang belum punya konteks

## Siklus Review Rutin

| Dokumen | Frekuensi Review |
|---------|------------------|
| Matriks eskalasi & daftar kontak | Bulanan |
| Playbook | 6 bulan atau setelah insiden terkait |
| SOP L1 & L2 | 6 bulan |
| Severity & SLA | Tahunan atau saat profil risiko berubah |
| Daftar aset kritikal | Kuartalan |

Setiap insiden P1/P2 wajib diikuti pertanyaan: **apakah SOP ini perlu diubah?**
