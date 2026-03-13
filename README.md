# ⚡ Process Forker
A python-based stress-testing utility designed to evaluate system stability and window manager resilience. It executes high-frequency recursive UI spawning to analyze resources allocation limits and process scheduling under extreme load

> **A Python-based resource exhaustion tool** that continuously forks processes and spams system notifications to consume CPU, memory, and OS resources.

##

> [!WARNING]  
> **System Crash Potential:** This script will consume all available system resources (RAM/CPU) until the OS becomes unresponsive. Use only in a controlled environment (VM/Sandbox).

---

## 🧠 Overview

**Process Forker** adalah tool Python yang secara agresif menghabiskan resource perangkat dengan cara:
- Terus-menerus melakukan **fork proses baru** secara rekursif / paralel
- Memunculkan **notifikasi sistem** secara masif dan tidak henti
- Mengkonsumsi **CPU, RAM, dan file descriptor** secara progresif
- Berjalan di **background** dengan kemampuan self-replication

> ⚠️ **WARNING**: Tool ini dirancang untuk keperluan **stress testing**, **security research**, atau **CTF/educational purposes**. Penggunaan di sistem tanpa izin adalah ilegal.

---

## 🗺️ Roadmap

```
v0.1  ──►  v0.2  ──►  v0.3  ──►  v1.0
 │           │           │          │
Core Fork  Notif     Resource    Full
Engine     Spam      Monitor    Release
```

### Phase 1 — Core Fork Engine `v0.1`
- [ ] Implementasi `os.fork()` dasar di Linux / `multiprocessing` cross-platform
- [ ] Loop rekursif fork dengan delay yang bisa dikonfigurasi
- [ ] Graceful error handling saat limit proses tercapai
- [ ] Logging setiap fork ke stdout

### Phase 2 — Notification Spammer `v0.2`
- [ ] Integrasi **`plyer`** untuk notifikasi cross-platform (Linux/Mac/Windows)
- [ ] Mode spam: notif terus muncul tanpa jeda
- [ ] Custom pesan notifikasi (title, body, icon)
- [ ] Thread terpisah untuk notif agar tidak block fork engine

### Phase 3 — Resource Consumer `v0.3`
- [ ] **CPU burner**: infinite loop computation di tiap child process
- [ ] **Memory filler**: alokasi buffer besar secara bertahap
- [ ] **File descriptor exhauster**: buka banyak file/socket tanpa tutup
- [ ] Monitor resource usage real-time dengan `psutil`

### Phase 4 — Polish & Release `v1.0`
- [ ] CLI flags (`--mode`, `--delay`, `--target`, `--notif-msg`)
- [ ] Config file support (YAML/JSON)
- [ ] Daemonize process (berjalan di background)
- [ ] Cross-platform testing (Linux, macOS, Windows)
- [ ] Dokumentasi lengkap

---

## 🔄 Workflow

```
┌─────────────────────────────────────────────────────────┐
│                    PROCESS FORKER                        │
│                                                          │
│   main.py                                                │
│      │                                                   │
│      ├──► ForkEngine ──► fork() ──► Child Process       │
│      │        │               └──► Child Process        │
│      │        │               └──► Child Process ...    │
│      │        │                                          │
│      │        └──► [setiap child ulang dari awal]        │
│      │                                                   │
│      ├──► NotifSpammer ──► Thread Loop                  │
│      │        └──────────► notif() setiap X ms           │
│      │                                                   │
│      └──► ResourceBurner                                 │
│               ├──► CPU Thread (infinite loop)            │
│               ├──► Memory Thread (alokasi terus)         │
│               └──► FD Thread (buka file terus)           │
└─────────────────────────────────────────────────────────┘
```

### Execution Flow

```
START
  │
  ▼
Parse args / Load config
  │
  ▼
Spawn NotifSpammer thread ──────────────────────────────┐
  │                                                      │
  ▼                                                   (loop)
Spawn ResourceBurner threads                         notif()
  │                                                      │
  ▼                                                      │
ForkEngine.run()                                         │
  │                                                      │
  ├── fork() ──► child ──► ForkEngine.run() [rekursif]  │
  │                                                      │
  └── parent ──► fork() lagi ──► ...                    │
                                                         │
(semua proses terus jalan sampai resource habis) ◄───────┘
```

---

## 📁 Struktur Project

```
process-forker/
│
├── main.py                  # Entry point, parse args
├── config.yaml              # Konfigurasi default
├── requirements.txt         # Dependencies
│
├── core/
│   ├── __init__.py
│   ├── fork_engine.py       # Logic fork proses
│   ├── notif_spammer.py     # Spam notifikasi sistem
│   └── resource_burner.py   # CPU/RAM/FD consumer
│
├── utils/
│   ├── __init__.py
│   ├── logger.py            # Logging helper
│   └── platform_check.py   # Deteksi OS
│
└── tests/
    ├── test_fork.py
    ├── test_notif.py
    └── test_resource.py
```

---

## 🛠️ Tech Stack

| Komponen | Library | Fungsi |
|---|---|---|
| Fork Engine | `os`, `multiprocessing` | Fork & spawn proses |
| Notifikasi | `plyer` | Cross-platform notif |
| Resource Monitor | `psutil` | CPU/RAM monitoring |
| Threading | `threading` | Paralel task |
| CLI | `argparse` | Argument parsing |
| Config | `PyYAML` | Load config file |

---

## 🚀 Quick Start

```bash
# Install dependencies
pip install -r requirements.txt

# Jalankan dengan mode default
python main.py

# Jalankan dengan semua mode aktif
python main.py --mode all --delay 0.1 --notif-msg "OWNED"

# Hanya spam notifikasi
python main.py --mode notif --interval 500
```

---

## ⚙️ Config

```yaml
# config.yaml
fork:
  enabled: true
  delay: 0.05          # detik antar fork
  max_depth: 999       # kedalaman rekursi fork

notification:
  enabled: true
  interval_ms: 200     # interval notifikasi (ms)
  title: "Alert"
  message: "System overloaded"

resource:
  cpu: true
  memory: true
  file_descriptors: true
  memory_chunk_mb: 50  # alokasi per iterasi
```

---

## 📦 Requirements

```
plyer>=2.1.0
psutil>=5.9.0
PyYAML>=6.0
```

---

## ⚠️ Disclaimer

Tool ini dibuat **hanya untuk tujuan edukasi, stress testing sistem sendiri, dan penelitian keamanan**. Penggunaan tool ini untuk menyerang atau mengganggu sistem orang lain tanpa izin adalah **tindakan ilegal** dan dapat dikenai sanksi hukum. Gunakan dengan tanggung jawab.
