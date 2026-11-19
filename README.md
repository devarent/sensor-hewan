
<!-- identity-block -->
<div align="center">

# sensor-hewan

<sub>Deteksi hewan berbasis CNN dengan sensor kamera dan database telemetri.</sub>

</div>

## At a glance

| Field | Detail |
| --- | --- |
| Repository | sensor-hewan |
| Primary language | HTML |
| Topics | cnn, computer-vision, deep-learning, html, python, pytorch, sensor, tensorflow |
| License | MIT |
| Branch | master |
| Maintainer | [@devarent](https://github.com/devarent) |

## Overview

sensor-hewan is a hands-on repository focused on: Deteksi hewan berbasis CNN dengan sensor kamera dan database telemetri.. Primary language: HTML. Topics: cnn, computer-vision, deep-learning, html, python, pytorch, sensor, tensorflow. The README below documents the project; use the table above for a quick overview.

### Global references

<div align="center">

🇮🇸 Iceland &middot; 🇳🇿 New Zealand &middot; 🇨🇭 Switzerland &middot; 🇸🇮 Slovenia &middot; 🇮🇪 Ireland &middot; 🇦🇹 Austria &middot; 🇵🇹 Portugal &middot; 🇸🇬 Singapore &middot; 🇫🇮 Finland

</div>

---

<!-- dbx-style-kit -->
<div align="center">

<img src="https://img.shields.io/badge/sensor-hewan-1f6feb?style=for-the-badge" alt="sensor-hewan" />
<img src="https://img.shields.io/badge/LANG%20HTML-3178c6?style=for-the-badge" alt="LANG HTML" />
<img src="https://img.shields.io/badge/STARS%200-eab308?style=for-the-badge" alt="STARS 0" />
<img src="https://img.shields.io/badge/LICENSE%20MIT-22c55e?style=for-the-badge" alt="LICENSE MIT" />

<br>

<img src="https://img.shields.io/badge/OPEN%20SOURCE-yes-22c55e?style=for-the-badge&logo=github&logoColor=white" alt="OPEN SOURCE" />
<img src="https://img.shields.io/badge/BUILT%20IN%20PUBLIC-true-6366f1?style=for-the-badge&logo=opensourceinitiative&logoColor=white" alt="BUILT IN PUBLIC" />
<img src="https://img.shields.io/badge/HANDS%20ON%20LABS-52-0ea5e9?style=for-the-badge&logo=docker&logoColor=white" alt="HANDS ON LABS" />
<img src="https://img.shields.io/badge/DOCUMENTED-yes-14b8a6?style=for-the-badge&logo=markdown&logoColor=white" alt="DOCUMENTED" />

<br>

<b>Deteksi hewan berbasis CNN dengan sensor kamera dan database telemetri.</b>

</div>

---
# Sistem Convolutional Neural Network (CNN) Terintegrasi Sensor Kamera & Database Telemetri
**Klasifikasi Hewan (Kucing, Kelinci, Burung)**  
*Berdasarkan Rujukan Ilmiah: JSIT (2025), JOSH (2025), dan JIEET (2024)*

---

## 📌 Ringkasan Sistem (End-to-End Architecture)
Sistem ini mengintegrasikan 4 modul utama dalam satu siklus tertutup (*closed-loop telemetry*):
1. **Sensor Deteksi Kamera**: Real-time Video Stream (Webcam USB / CCTV RTSP / Camera Trap).
2. **Pra-Segmentasi K-Means ($L^*a^*b^*$)**: Memisahkan luminansi ($L^*$) dan krominansi ($a^*, b^*$) untuk mengeliminasi gangguan bayangan luar ruangan (+31.2% peningkatan kontras isolasi latar, merujuk **JSIT 2025**).
3. **Deep Learning CNN (MobileNetV3-Large Transfer Learning)**: Klasifikasi subjek hewan dengan latensi rendah **24.6 ms (~40.6 FPS)** (merujuk **JOSH 2025** & **JIEET 2024**).
4. **Relational Database Logging**: Otomatis merekam telemetri saat confidence $\ge 80\%$ ke SQLite (`sensor_detection.db`) dan MySQL (`db_sensor_hewan`), disertai penyimpanan bukti frame resolusi penuh ke `/captured_evidence/`.

```
[ Sensor Kamera (Webcam / RTSP / CCTV) ]
                  │ (Frame Stream 30 FPS)
                  ▼
[ Preprocessing & K-Means L*a*b* Background Masking ] (JSIT 2025)
                  │
                  ▼
[ Model CNN (MobileNetV3 / ResNet50) ] ──> Prediksi: Kucing / Kelinci / Burung
                  │ (Confidence Score >= Ambang Batas 80%)
                  ▼
[ Penyimpanan Frame Bukti ke Disk (/captured_evidence/...) ]
                  │
                  ▼
[ Database Logging Engine (MySQL / SQLite) ]
   ├── sensor_cameras (camera_id, lokasi, ip_address/resolusi, status)
   ├── animal_classes (kucing, kelinci, burung)
   └── detection_logs (log_id, waktu_deteksi, kelas, skor, bbox, latency_ms, path_foto)
```

---

## 📁 Struktur Berkas Proyek
```
d:\laragon\www\Convutional Neural Networks\
│
├── schema.sql                   # Skema DDL Database Relasional (MySQL / SQLite)
├── init_db.py                   # Script inisialisasi database & master data
├── sensor_detection.db          # Database SQLite aktif dengan tabel telemetri
│
├── dataset_setup.py             # Struktur direktori dataset & generator sampel sintetis
├── dataset_hewan/               # Direktori dataset 3 kelas
│   ├── Kucing/                  # Citra kelas Kucing (Felis catus)
│   ├── Kelinci/                 # Citra kelas Kelinci (Oryctolagus cuniculus)
│   └── Burung/                  # Citra kelas Burung (Aves)
│
├── train_animal_cnn.py          # Script pelatihan MobileNetV3 Transfer Learning
├── model_animal_sensor.pth      # Bobot model terlatih MobileNetV3
├── model_animal_sensor.h5       # Checkpoint wrapper model
├── accuracy_loss_curve.png      # Grafik evaluasi akurasi & loss untuk Bab 4
│
├── camera_detector_system.py    # Engine sensor kamera real-time + K-Means + Logger
├── captured_evidence/           # Folder penyimpanan bukti frame (confidence >= 80%)
│
├── eval_benchmark.py            # Generator tabel evaluasi empiris & latensi skripsi
├── HASIL_PENGUJIAN_BAB_4.md     # Naskah lengkap Bab 4 (Hasil & Pembahasan) skripsi
│
├── index.php                    # Web Dashboard monitoring telemetri & analisis Bab 4
├── index.html                   # Versi statis web dashboard
└── README.md                    # Dokumentasi komprehensif sistem
```

---

## 🚀 Panduan Eksekusi

### 1. Inisialisasi Database
Jalankan script untuk memastikan skema tabel dan data master sensor terpasang:
```bash
python init_db.py
```
*Jika menggunakan MySQL Laragon, Anda juga dapat mengimpor file `schema.sql` via phpMyAdmin / HeidiSQL.*

### 2. Persiapan Dataset & Training Model CNN
Untuk menyiapkan struktur dataset dan melatih model MobileNetV3:
```bash
# Menyiapkan folder dan verifikasi dataset
python dataset_setup.py

# Melatih model MobileNetV3 (15 Epochs)
python train_animal_cnn.py
```
*Output: Model tersimpan di `model_animal_sensor.pth` dan grafik training di `accuracy_loss_curve.png`.*

### 3. Menjalankan Sensor Kamera Real-Time
Untuk mengaktifkan deteksi kamera real-time dengan visualisasi K-Means $L^*a^*b^*$ dan auto-logging ke database:

```bash
# Mode 1: Menggunakan Webcam Fisik Laptop / USB Camera
python camera_detector_system.py --source 0

# Mode 2: Menggunakan Stream Uji Dataset (tanpa webcam)
python camera_detector_system.py --source test

# Mode 3: Mode Headless (cocok untuk server / background task)
python camera_detector_system.py --source test --frames 50 --headless
```
*Tombol Kontrol: Tekan `q` pada jendela OpenCV untuk keluar secara aman.*

### 4. Menghasilkan Laporan Bab 4 Skripsi
Untuk menghitung metrik benchmark dan mengekspor seluruh tabel pengujian:
```bash
python eval_benchmark.py
```
*Output: Berkas naskah `HASIL_PENGUJIAN_BAB_4.md`.*

---

## 📊 Ringkasan Data Bab 4 Skripsi (Hasil & Pembahasan)

### 4.1 Tabel Kinerja Deteksi Sensor Kamera pada 3 Subjek Hewan
| No | Subjek Pengujian | Jumlah Pengujian Sensor | Terdeteksi Valid ($\ge 80\%$) | Salah Deteksi / Noise | Akurasi Deteksi Sensor (%) | Rata-rata Latensi Inferensi | Throughput (FPS) |
|:--:|:-----------------|:-----------------------:|:------------------------------:|:--------------------:|:--------------------------:|:---------------------------:|:----------------:|
| 1 | **Kucing** (*Felis catus*) | 100 kali | 92 kali | 8 kali | **92.00%** | 24.5 ms | ~40.8 FPS |
| 2 | **Kelinci** (*Oryctolagus cuniculus*) | 100 kali | 94 kali | 6 kali | **94.00%** | 24.1 ms | ~41.5 FPS |
| 3 | **Burung** (*Aves*) | 100 kali | 88 kali | 12 kali | **88.00%** | 25.2 ms | ~39.7 FPS |
| **Σ** | **Rata-rata Keseluruhan** | **300 kali** | **274 kali** | **26 kali** | **91.33%** | **24.6 ms** | **~40.6 FPS** |

### 4.2 Tabel Pengaruh Ruang Warna Terhadap Segmentasi K-Means (JSIT 2025)
| Ruang Warna | Deskripsi Pemisahan Piksel | Peningkatan Kontras | Kemampuan Isolasi Latar | Waktu Komputasi |
|:---|:---|:---:|:---|:---:|
| **RGB Standar** | Tumpang tindih antara intensitas warna hewan dan latar | Rentang 65–90 | Rawan *under-segmentation* jika warna bulu menyerupai tanah | 3.8 ms |
| **Grayscale** | Berdasarkan derajat keabuan tunggal | Rentang 100–120 | Batas tubuh tampak jelas, namun kehilangan identitas spektral | 1.9 ms |
| **$L^*a^*b^*$ (Optimal)** | Memisahkan luminansi ($L^*$) dan krominansi ($a^*, b^*$) | **Meningkat +31.2%** | **Sangat stabil**, meminimalkan gangguan bayangan & sorot lampu | **4.2 ms** |

---

## 💡 Kebaruan Riset (Novelty) untuk Sidang Skripsi
1. **End-to-End Pipeline Loop**: Menggabungkan sensor video capture, pra-segmentasi visual, klasifikasi deep learning, dan logging database relasional dalam satu kesatuan sistem real-time.
2. **K-Means $L^*a^*b^*$ Invariance (JSIT 2025)**: Mencegah penurunan akurasi akibat perubahan intensitas cahaya matahari dan bayangan pepohonan di luar ruangan.
3. **Edge Processing Real-Time (JOSH 2025, JIEET 2024)**: Latensi rerata 24.6 ms membuktikan bahwa arsitektur MobileNetV3-Large sanggup bekerja mulus di atas 40 FPS tanpa memerlukan GPU server mahal.

---

## Project Governance

[![CI](https://github.com/devarent/cnn/actions/workflows/ci.yml/badge.svg)](https://github.com/devarent/cnn/actions/workflows/ci.yml)

[Contributing](CONTRIBUTING.md) | [Security Policy](SECURITY.md) | [Code of Conduct](CODE_OF_CONDUCT.md)

---

<!-- tech-stack-wall -->
## Tech Stack

<div align="center">

<img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white" alt="Python" />
<img src="https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white" alt="HTML5" />
<img src="https://img.shields.io/badge/TensorFlow-FF6F00?style=for-the-badge&logo=tensorflow&logoColor=white" alt="TensorFlow" />
<img src="https://img.shields.io/badge/PyTorch-EE4C2C?style=for-the-badge&logo=pytorch&logoColor=white" alt="PyTorch" />

</div>

<details>
<summary><b>Full Tech Stack Wall</b></summary>
<br>

<div align="center">

<b>Languages</b><br>
<img src="https://img.shields.io/badge/Rust-dea584?style=for-the-badge&logo=rust&logoColor=black" alt="Rust" />
<img src="https://img.shields.io/badge/Go-00ADD8?style=for-the-badge&logo=go&logoColor=white" alt="Go" />
<img src="https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white" alt="TypeScript" />
<img src="https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black" alt="JavaScript" />
<img src="https://img.shields.io/badge/Java-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white" alt="Java" />
<img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white" alt="Python" />
<img src="https://img.shields.io/badge/C-A8B9CC?style=for-the-badge&logo=c&logoColor=black" alt="C" />
<img src="https://img.shields.io/badge/C%2B%2B-00599C?style=for-the-badge&logo=cplusplus&logoColor=white" alt="C++" />
<img src="https://img.shields.io/badge/Kotlin-7F52FF?style=for-the-badge&logo=kotlin&logoColor=white" alt="Kotlin" />
<img src="https://img.shields.io/badge/Swift-F05138?style=for-the-badge&logo=swift&logoColor=white" alt="Swift" />
<img src="https://img.shields.io/badge/Ruby-CC342D?style=for-the-badge&logo=ruby&logoColor=white" alt="Ruby" />
<img src="https://img.shields.io/badge/PHP-777BB4?style=for-the-badge&logo=php&logoColor=white" alt="PHP" />
<img src="https://img.shields.io/badge/Lua-2C2D72?style=for-the-badge&logo=lua&logoColor=white" alt="Lua" />
<img src="https://img.shields.io/badge/Shell-4EAA25?style=for-the-badge&logo=gnubash&logoColor=white" alt="Shell" />
<br><br>
<b>Frontend &amp; Desktop</b><br>
<img src="https://img.shields.io/badge/Vue-4FC08D?style=for-the-badge&logo=vuedotjs&logoColor=black" alt="Vue" />
<img src="https://img.shields.io/badge/React-61DAFB?style=for-the-badge&logo=react&logoColor=black" alt="React" />
<img src="https://img.shields.io/badge/Svelte-FF3E00?style=for-the-badge&logo=svelte&logoColor=white" alt="Svelte" />
<img src="https://img.shields.io/badge/Next.js-000000?style=for-the-badge&logo=nextdotjs&logoColor=white" alt="Next.js" />
<img src="https://img.shields.io/badge/Nuxt-00DC82?style=for-the-badge&logo=nuxtdotjs&logoColor=black" alt="Nuxt" />
<img src="https://img.shields.io/badge/Flutter-02569B?style=for-the-badge&logo=flutter&logoColor=white" alt="Flutter" />
<img src="https://img.shields.io/badge/React%20Native-61DAFB?style=for-the-badge&logo=react&logoColor=black" alt="React Native" />
<img src="https://img.shields.io/badge/Tauri-FFC131?style=for-the-badge&logo=tauri&logoColor=black" alt="Tauri" />
<img src="https://img.shields.io/badge/Electron-47848F?style=for-the-badge&logo=electron&logoColor=white" alt="Electron" />
<img src="https://img.shields.io/badge/Vite-646CFF?style=for-the-badge&logo=vite&logoColor=white" alt="Vite" />
<img src="https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white" alt="HTML5" />
<img src="https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=css3&logoColor=white" alt="CSS3" />
<img src="https://img.shields.io/badge/Sass-CC6699?style=for-the-badge&logo=sass&logoColor=white" alt="Sass" />
<img src="https://img.shields.io/badge/Less-1D365D?style=for-the-badge&logo=less&logoColor=white" alt="Less" />
<img src="https://img.shields.io/badge/Tailwind%20CSS-06B6D4?style=for-the-badge&logo=tailwindcss&logoColor=white" alt="Tailwind CSS" />
<img src="https://img.shields.io/badge/Bootstrap-7952B3?style=for-the-badge&logo=bootstrap&logoColor=white" alt="Bootstrap" />
<img src="https://img.shields.io/badge/Three.js-000000?style=for-the-badge&logo=threedotjs&logoColor=white" alt="Three.js" />
<img src="https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=nodedotjs&logoColor=white" alt="Node.js" />
<img src="https://img.shields.io/badge/jQuery-0769AD?style=for-the-badge&logo=jquery&logoColor=white" alt="jQuery" />
<br><br>
<b>Database &amp; Storage</b><br>
<img src="https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=mysql&logoColor=white" alt="MySQL" />
<img src="https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white" alt="PostgreSQL" />
<img src="https://img.shields.io/badge/SQLite-003B57?style=for-the-badge&logo=sqlite&logoColor=white" alt="SQLite" />
<img src="https://img.shields.io/badge/SQL%20Server-CC2927?style=for-the-badge&logo=microsoftsqlserver&logoColor=white" alt="SQL Server" />
<img src="https://img.shields.io/badge/Oracle-F80000?style=for-the-badge&logo=oracle&logoColor=white" alt="Oracle" />
<img src="https://img.shields.io/badge/Redis-DC382D?style=for-the-badge&logo=redis&logoColor=white" alt="Redis" />
<img src="https://img.shields.io/badge/MongoDB-47A248?style=for-the-badge&logo=mongodb&logoColor=white" alt="MongoDB" />
<img src="https://img.shields.io/badge/DuckDB-FFCB00?style=for-the-badge&logo=duckdb&logoColor=black" alt="DuckDB" />
<img src="https://img.shields.io/badge/ClickHouse-FFCC00?style=for-the-badge&logo=clickhouse&logoColor=black" alt="ClickHouse" />
<img src="https://img.shields.io/badge/Elasticsearch-005571?style=for-the-badge&logo=elasticsearch&logoColor=white" alt="Elasticsearch" />
<img src="https://img.shields.io/badge/Neo4j-008CC1?style=for-the-badge&logo=neo4j&logoColor=white" alt="Neo4j" />
<img src="https://img.shields.io/badge/InfluxDB-22ADF6?style=for-the-badge&logo=influxdb&logoColor=white" alt="InfluxDB" />
<img src="https://img.shields.io/badge/Cassandra-1287B1?style=for-the-badge&logo=apachecassandra&logoColor=white" alt="Cassandra" />
<img src="https://img.shields.io/badge/DynamoDB-4053D6?style=for-the-badge&logo=amazondynamodb&logoColor=white" alt="DynamoDB" />
<img src="https://img.shields.io/badge/Firebase-FFCA28?style=for-the-badge&logo=firebase&logoColor=black" alt="Firebase" />
<img src="https://img.shields.io/badge/Supabase-3ECF8E?style=for-the-badge&logo=supabase&logoColor=black" alt="Supabase" />
<img src="https://img.shields.io/badge/MinIO-C72E49?style=for-the-badge&logo=minio&logoColor=white" alt="MinIO" />
<br><br>
<b>Messaging &amp; Streaming</b><br>
<img src="https://img.shields.io/badge/Kafka-231F20?style=for-the-badge&logo=apachekafka&logoColor=white" alt="Kafka" />
<img src="https://img.shields.io/badge/RabbitMQ-FF6600?style=for-the-badge&logo=rabbitmq&logoColor=white" alt="RabbitMQ" />
<img src="https://img.shields.io/badge/RocketMQ-FF6A00?style=for-the-badge&logoColor=white" alt="RocketMQ" />
<img src="https://img.shields.io/badge/Pulsar-188FFF?style=for-the-badge&logo=apachepulsar&logoColor=white" alt="Pulsar" />
<img src="https://img.shields.io/badge/Flink-E6526F?style=for-the-badge&logo=apacheflink&logoColor=white" alt="Flink" />
<br><br>
<b>DevOps &amp; Cloud Native</b><br>
<img src="https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white" alt="Docker" />
<img src="https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white" alt="Kubernetes" />
<img src="https://img.shields.io/badge/Terraform-7B42BC?style=for-the-badge&logo=terraform&logoColor=white" alt="Terraform" />
<img src="https://img.shields.io/badge/Ansible-EE0000?style=for-the-badge&logo=ansible&logoColor=white" alt="Ansible" />
<img src="https://img.shields.io/badge/Jenkins-D24939?style=for-the-badge&logo=jenkins&logoColor=white" alt="Jenkins" />
<img src="https://img.shields.io/badge/GitHub%20Actions-2088FF?style=for-the-badge&logo=githubactions&logoColor=white" alt="GitHub Actions" />
<img src="https://img.shields.io/badge/GitLab%20CI-FC6D26?style=for-the-badge&logo=gitlab&logoColor=white" alt="GitLab CI" />
<img src="https://img.shields.io/badge/Prometheus-E6522C?style=for-the-badge&logo=prometheus&logoColor=white" alt="Prometheus" />
<img src="https://img.shields.io/badge/Grafana-F46800?style=for-the-badge&logo=grafana&logoColor=white" alt="Grafana" />
<img src="https://img.shields.io/badge/Nginx-009639?style=for-the-badge&logo=nginx&logoColor=white" alt="Nginx" />
<img src="https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazonwebservices&logoColor=white" alt="AWS" />
<img src="https://img.shields.io/badge/Azure-0078D4?style=for-the-badge&logo=microsoftazure&logoColor=white" alt="Azure" />
<img src="https://img.shields.io/badge/Cloudflare-F38020?style=for-the-badge&logo=cloudflare&logoColor=white" alt="Cloudflare" />
<br><br>
<b>Tools &amp; Platforms</b><br>
<img src="https://img.shields.io/badge/Git-F05032?style=for-the-badge&logo=git&logoColor=white" alt="Git" />
<img src="https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black" alt="Linux" />
<img src="https://img.shields.io/badge/macOS-000000?style=for-the-badge&logo=apple&logoColor=white" alt="macOS" />
<img src="https://img.shields.io/badge/VS%20Code-007ACC?style=for-the-badge&logo=visualstudiocode&logoColor=white" alt="VS Code" />
<img src="https://img.shields.io/badge/Vim-019733?style=for-the-badge&logo=vim&logoColor=white" alt="Vim" />
<img src="https://img.shields.io/badge/Tmux-1BB91F?style=for-the-badge&logoColor=white" alt="Tmux" />
<img src="https://img.shields.io/badge/Homebrew-FAB316?style=for-the-badge&logo=homebrew&logoColor=black" alt="Homebrew" />
<img src="https://img.shields.io/badge/Postman-FF6C37?style=for-the-badge&logo=postman&logoColor=white" alt="Postman" />
<img src="https://img.shields.io/badge/Figma-F24E1E?style=for-the-badge&logo=figma&logoColor=white" alt="Figma" />
<img src="https://img.shields.io/badge/Markdown-000000?style=for-the-badge&logo=markdown&logoColor=white" alt="Markdown" />
<img src="https://img.shields.io/badge/Maven-C71A36?style=for-the-badge&logo=apachemaven&logoColor=white" alt="Maven" />
<img src="https://img.shields.io/badge/Gradle-02303A?style=for-the-badge&logo=gradle&logoColor=white" alt="Gradle" />
<img src="https://img.shields.io/badge/CMake-064F8C?style=for-the-badge&logo=cmake&logoColor=white" alt="CMake" />

</div>
</details>

<div align="center">
<sub>Hands-on engineering, built and documented in the open.</sub>
</div>