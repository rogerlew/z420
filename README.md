# Z420 — HP Z420 Workstation (Server Case)

## Hardware

| Component | Detail |
|-----------|--------|
| **Chassis** | HP Z420 Workstation in server case |
| **Motherboard** | HP 1589 (BIOS J61 v03.96) |
| **CPU** | Intel Xeon E5-1680 v2 — 8 cores / 16 threads @ 3.0 GHz (turbo 3.9 GHz), Ivy Bridge-EP |
| **RAM** | 128 GB DDR3 non-ECC |
| **GPU** | AMD Radeon RX 580 (Ellesmere) |

## SATA Controller Layout (Intel C602 PCH)

The Z420 has 10 SATA ports across three controllers on the C602 chipset:

| Header | Controller | Speed | Ports | Driver | Planned Use |
|--------|-----------|-------|-------|--------|-------------|
| #15 | AHCI | 6 Gb/s | 2 | ahci | Boot SSD (sdd) |
| #14 | AHCI | 3 Gb/s | 4 | ahci | 3x WD Red 8TB (ZFS) |
| #17 | SCU | 3 Gb/s | 4 | isci | 3x WD Red 8TB (ZFS) |

Notes:
- Only 2 ports support SATA III (6 Gb/s) — reserve for SSD
- 3 Gb/s is not a bottleneck for 5400 RPM HDDs (~180 MB/s max sequential)
- Splitting HDDs 3+3 across controllers balances I/O for ZFS scrubs/resilvers
- Reference: HP Maintenance and Service Guide (c04205252.pdf), p19–21

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

### SMART Summary (2026-03-19)

Drives originally from a QNAP NAS, in service since ~2019. All 6 report **PASSED**.

| Device | Serial | Power-On Hours | Power Cycles | Temp | Realloc | Pending | CRC Errors | SATA Link |
|--------|--------|---------------|-------------|------|---------|---------|------------|-----------|
| sda | VAJ124WL | 38,880 (4.4 yr) | 418 | 40°C | 0 | 0 | 0 | 6.0 Gb/s |
| sdb | VAJ1GV4L | 38,868 (4.4 yr) | 419 | 38°C | 0 | 0 | 0 | 6.0 Gb/s |
| sdc | VAJ16XJL | 38,929 (4.4 yr) | 415 | 37°C | 0 | 0 | 0 | 3.0 Gb/s |
| sde | VAJ1BWSL | 38,948 (4.4 yr) | 416 | 40°C | 0 | 0 | 0 | 3.0 Gb/s |
| sdf | VAJ0ZM9L | 38,915 (4.4 yr) | 416 | 41°C | 0 | 0 | 0 | 3.0 Gb/s |
| sdg | VAHUTYRL | 38,901 (4.4 yr) | 416 | 40°C | 0 | 0 | 0 | 3.0 Gb/s |

Key observations:
- **Zero reallocated sectors** across all drives — no bad blocks
- **Zero pending sectors** — no sectors waiting to be remapped
- **Zero CRC errors** — clean cable/controller connections
- **~38,900 hours** (~4.4 years) of power-on time, consistent with 2019 deployment
- **Load cycle counts ~34,700–35,000** — normal for NAS duty
- Temps 37–41°C — well within operating range
- 4 of 6 drives negotiated at 3.0 Gb/s instead of 6.0 Gb/s (sdc, sde, sdf, sdg) — expected, only 2 of 10 onboard ports are 6 Gb/s

### Planned: ZFS RAIDZ2

Replacing mdadm RAID6 with ZFS RAIDZ2 on the 6x WD Red 8TB drives. ~23 TB usable, tolerates 2 simultaneous drive failures.

## Software

| Component | Detail |
|-----------|--------|
| **OS** | Arch Linux (omarchy) |
| **Kernel** | 6.19.8-arch1-1 (SMP PREEMPT_DYNAMIC) |
| **Hostname** | hpc |
| **Swap** | 63 GB zram + md123 |
