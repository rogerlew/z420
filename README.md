# Z420 — HP Z420 Workstation (Server Case)

## Hardware

| Component | Detail |
|-----------|--------|
| **Chassis** | HP Z420 Workstation in server case |
| **Motherboard** | HP 1589 (BIOS J61 v03.96) |
| **CPU** | Intel Xeon E5-1680 v2 — 8 cores / 16 threads @ 3.0 GHz (turbo 3.9 GHz), Ivy Bridge-EP |
| **RAM** | 128 GB DDR3 non-ECC |
| **GPU** | AMD Radeon RX 580 (Ellesmere) |

## Storage

### Boot Drive
| Device | Model | Size |
|--------|-------|------|
| sdd | Crucial MX500 CT500MX500SSD1 | 500 GB |

- `sdd1` — 2 GB `/boot`
- `sdd2` — 464 GB LUKS → `/` (and `/var/log`)

### HDD Array — 6x WD Red 8TB (WD80EFAX)
| Device | Serial |
|--------|--------|
| sda | VAJ124WL |
| sdb | VAJ1GV4L |
| sdc | VAJ16XJL |
| sde | VAJ1BWSL |
| sdf | VAJ0ZM9L |
| sdg | VAHUTYRL |

### RAID Layout (mdadm)
| Array | Level | Size | Purpose |
|-------|-------|------|---------|
| md124 | RAID6 (6 disks) | ~29.1 TB | Main data storage |
| md125 | RAID1 (6 disks) | 448 MB | Metadata/config |
| md127 | RAID1 (6 disks) | 518 MB | Metadata/config |
| md126 | RAID1 (2 active + 4 spare) | 518 MB | Metadata/config |
| md123 | RAID1 (2 active + 4 spare) | 6.9 GB | Swap/scratch |

All arrays healthy — `[UUUUUU]` / `[UU]`.

## Software

| Component | Detail |
|-----------|--------|
| **OS** | Arch Linux (omarchy) |
| **Kernel** | 6.19.8-arch1-1 (SMP PREEMPT_DYNAMIC) |
| **Hostname** | hpc |
| **Swap** | 63 GB zram + md123 |
