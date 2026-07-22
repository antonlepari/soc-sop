# 00 — Glosarium & Singkatan

Dokumen ini menjadi acuan tunggal istilah yang dipakai di seluruh SOP. Bila ada perbedaan penafsiran istilah antar dokumen, definisi di sini yang berlaku.

## A–E

| Istilah | Definisi |
|---------|----------|
| **Alert** | Notifikasi otomatis dari tools keamanan (SIEM/EDR/IDS) yang menandakan potensi aktivitas mencurigakan. Alert **belum tentu** insiden. |
| **Artefak** | Bukti digital yang dikumpulkan selama investigasi: log, memory dump, file sampel, hash, PCAP. |
| **Chain of Custody** | Rantai pencatatan kepemilikan dan penanganan barang bukti digital sejak dikumpulkan hingga diserahkan. |
| **Containment** | Tindakan membatasi dampak insiden agar tidak menyebar lebih jauh (isolasi host, disable akun, blokir IP). |
| **CSIRT** | *Computer Security Incident Response Team* — tim yang menangani insiden berskala besar di atas kapasitas SOC harian. |
| **EDR** | *Endpoint Detection and Response* — agen keamanan di endpoint dengan kemampuan deteksi dan respons. |
| **Eradication** | Penghapusan akar penyebab dari lingkungan (hapus malware, tutup celah, cabut akses attacker). |
| **Escalation** | Penyerahan penanganan ke level atau pihak yang lebih berwenang/berkompeten. |
| **Event** | Setiap kejadian yang terekam pada sistem. Sebagian kecil event menjadi alert. |

## F–M

| Istilah | Definisi |
|---------|----------|
| **False Positive (FP)** | Alert yang setelah divalidasi ternyata bukan aktivitas berbahaya. |
| **False Negative (FN)** | Aktivitas berbahaya yang tidak terdeteksi oleh sistem. |
| **IOC** | *Indicator of Compromise* — artefak teknis penanda kompromi: hash, IP, domain, URL, registry key, nama file. |
| **IOA** | *Indicator of Attack* — pola perilaku yang menandakan serangan sedang berlangsung, terlepas dari IOC spesifik. |
| **Insiden** | Event atau rangkaian event yang mengancam kerahasiaan, integritas, atau ketersediaan aset informasi. |
| **MITRE ATT&CK** | Basis pengetahuan taktik dan teknik adversary; dipakai sebagai bahasa bersama saat mendeskripsikan serangan. |
| **MTTD** | *Mean Time To Detect* — rata-rata waktu dari serangan terjadi hingga terdeteksi. |
| **MTTR** | *Mean Time To Respond/Resolve* — rata-rata waktu dari deteksi hingga insiden tertangani. |

## N–Z

| Istilah | Definisi |
|---------|----------|
| **PICERL** | Fase penanganan insiden: Preparation, Identification, Containment, Eradication, Recovery, Lessons Learned. |
| **Playbook** | Prosedur langkah demi langkah untuk satu jenis insiden spesifik. |
| **RCA** | *Root Cause Analysis* — analisis akar penyebab insiden. |
| **Recovery** | Pemulihan layanan ke kondisi operasional normal dengan pemantauan ketat. |
| **SIEM** | *Security Information and Event Management* — platform agregasi, korelasi, dan analisis log. |
| **SLA** | *Service Level Agreement* — komitmen waktu respons dan penyelesaian. |
| **SOAR** | *Security Orchestration, Automation and Response* — otomasi alur kerja respons insiden. |
| **True Positive (TP)** | Alert yang terbukti merupakan aktivitas berbahaya atau melanggar kebijakan. |
| **Threat Hunting** | Pencarian proaktif terhadap ancaman yang lolos dari deteksi otomatis, berbasis hipotesis. |
| **Tuning** | Penyesuaian rule deteksi untuk mengurangi noise tanpa menghilangkan visibilitas. |
| **UEBA** | *User and Entity Behavior Analytics* — deteksi anomali berbasis perilaku pengguna dan entitas. |

## Kode Status Tiket

| Status | Arti |
|--------|------|
| `NEW` | Alert baru masuk, belum ditriage |
| `IN_TRIAGE` | Sedang divalidasi oleh L1 |
| `ESCALATED` | Sudah dieskalasi ke L2/L3 |
| `IN_PROGRESS` | Sedang diinvestigasi/ditangani |
| `CONTAINED` | Dampak sudah dibatasi, belum tuntas |
| `RESOLVED` | Insiden selesai ditangani |
| `CLOSED_FP` | Ditutup sebagai false positive |
| `CLOSED_BENIGN` | Aktivitas nyata namun sah/diketahui (benign true positive) |
| `ON_HOLD` | Menunggu pihak eksternal/vendor/pemilik sistem |
