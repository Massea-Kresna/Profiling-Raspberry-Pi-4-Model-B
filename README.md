# 📊 OS Profiling & Resource Scheduling Analysis on Raspberry Pi 4

Repositori ini berisi hasil *profiling* performa sistem operasi Linux (Debian 13) pada Raspberry Pi 4 Model B. Proyek ini membandingkan metrik sistem saat dalam kondisi *idle* versus kondisi beban maksimal (*stress test*), serta menganalisis bagaimana kernel Linux (CFS) melakukan penjadwalan CPU dan manajemen memori.

## 💻 Hardware & OS Specification
* **Board:** Raspberry Pi 4 Model B Rev 1.4
* **CPU:** Quad-Core BCM2711 (aarch64)
* **RAM:** 4 GB (3.7 GiB Usable)
* **OS:** Debian GNU/Linux 13 (Trixie) - Kernel 6.18.33

## 🛠️ Methodology & Tools
Pengujian dilakukan menggunakan alat *monitoring* bawaan Linux:
* `mpstat` untuk utilisasi CPU
* `free` & `sar` untuk manajemen memori/Swap
* `iostat` untuk beban disk I/O
* `htop` untuk visualisasi performa *real-time*
* `stress-ng` sebagai *load generator* (`--cpu 4 --vm 2 --vm-bytes 256M --hdd 1`)

## 📋 Tabel Komparasi (Idle vs Load)
Berikut adalah ringkasan metrik performa berdasarkan hasil ekstraksi data pada kondisi sistem didiamkan (*idle*) dibandingkan saat diberi beban maksimal (*load*):

| Metrik Performa | Kondisi Idle | Kondisi Load |
| :--- | :---: | :---: |
| **Rata-rata CPU (%)** | ~3.53% | ~99.07% |
| **CPU Usage Core 0 (%usr)** | 2.56% | 86.16% |
| **CPU Usage Core 1 (%usr)** | 3.39% | 90.41% |
| **CPU Usage Core 2 (%usr)** | 3.55% | 92.42% |
| **CPU Usage Core 3 (%usr)** | 2.34% | 89.65% |
| **Jumlah Processes** | 196 | 218 |
| **Jumlah Threads** | 340 | 364 |
| **RAM Terpakai** | ~520 MB | ~885 MB |
| **Swap Terpakai** | 0 B | 0 B |
| **Disk Read Speed** | 0.00 kB/s | 0.00 kB/s |
| **Disk Write Speed** | ~2.40 kB/s | ~21,728 kB/s |

## 📈 Key Findings
1. **CPU Scheduling:** Algoritma *Completely Fair Scheduler* (CFS) terbukti mendistribusikan beban secara merata pada ke-4 *core* (utilisasi 86% - 92% per *core* saat ditekan maksimal). Tidak ada *core* yang *bottleneck*.
2. **Memory Management:** Tidak terjadi *paging* ke *Swap* (tetap 0 B). Kernel secara proaktif menggunakan prinsip *"free memory is wasted memory"* dengan mengubah RAM bebas menjadi *Page Cache* (Buff/Cache melonjak dari 1.7 GB ke 2.8 GB) untuk mengoptimalkan antrean disk I/O.
3. **I/O Bottleneck:** MicroSD menjadi *bottleneck* utama saat terjadi beban tulis yang masif, mencapai 99.1% - 99.5% utilisasi dengan latensi penulisan yang meroket hingga lebih dari 2.700 ms.

## 📂 Repository Structure
* `/docs` : Direktori yang berisi dokumen laporan praktikum (PDF/Docx) beserta bukti visual (screenshot).
* `/idle` : Kumpulan *file* raw output terminal (`.txt`) metrik sistem saat kondisi tanpa beban (*idle*).
* `/load` : Kumpulan *file* raw output terminal (`.txt`) metrik sistem saat kondisi beban maksimal (*load*).
* `spesifikasi.txt` : *File* berisi informasi spesifikasi detail perangkat keras dan *kernel* OS.

---
*Proyek ini dikerjakan untuk memenuhi tugas praktikum mata kuliah Sistem Operasi.*
