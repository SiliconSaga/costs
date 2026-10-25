# Homelab box

The one machine in the design that has to be bought: the box behind the tailnet that pulls the insurer files, parses them, holds the document store and the private key, and runs whatever else the house wants from a home server. This page is the planning envelope for acquiring it. The numbers are desk estimates from the file sizes on [Layer B](../layers/b-insurer-files.md) and typical parser throughput; the Layer B measurement (one carrier file parsed end to end with throughput and peak memory recorded) replaces them before anything is ordered.

## What it has to do

| Job | Shape | What it leans on |
|---|---|---|
| Layer A, monthly | Seven hospital files, hundreds of megabytes each, into Parquet | Nothing; any node in the house |
| Layer B, monthly per carrier | Download 1–2 TB compressed; stream-decompress and parse 10–20 TB of expanded JSON; keep the Essex rows; write a few gigabytes of Parquet | Cores first, then sequential read of the raw files |
| Cross-check and aggregates | DuckDB over the Parquet, tens of gigabytes of working set | Memory, then fast scratch when it spills |
| Layer D | OCR and the identifier pass on a handful of documents a week, then human review | Almost nothing; a CPU is plenty, no GPU |
| Always on | The receiver's pull side, the deletion schedule, a Postgres replica or claim, Tailscale | A few cores and a few gigabytes, continuously |
| The rest of the house | Home storage, backups, virtual machines, k8s nodes | Disk bays and memory |

Parsing is the bottleneck and it is CPU-bound: JSON has to be read byte by byte, and the files are read from a gzip stream, so nothing is ever expanded to disk unless the pre-splitter route on [Pipeline and data](pipeline.md#reading-the-files) is taken. Download is bandwidth-bound and happens before parsing, so it overlaps nothing on the box.

## Estimates

| Quantity | Estimate | Note |
|---|---|---|
| gzip decode, per core | 400–600 MB/s of output | Rarely the limit |
| JSON streaming parse, Python (`ijson` with the yajl backend) | 30–100 MB/s of expanded JSON per core | The slow path, and the one most of the existing tools use |
| JSON streaming parse, Go | 300–1,000 MB/s per core | `tic-mrf-parser` territory |
| One large network file (122 GB expanded) | Python: 20–70 min on one core; Go: 2–7 min | |
| One carrier-month (10–20 TB expanded) | Python: 30–180 core-hours; Go: 3–20 core-hours | On 16 cores: Python overnight, Go an hour or two |
| Download of 2 TB | ~4.5 h at 1 Gbps; ~9 h at 500 Mbps; ~2 h at 2.5 Gbps | Scales with the fiber plan, not the box |
| Memory per parser process | 1–4 GB | The provider-reference index for the file plus buffers |
| DuckDB over the Parquet | 16–32 GB with `memory_limit` set; spills to scratch beyond it | |

!!! warning "Unverified"
    Every row in this table is an estimate from published file sizes and typical parser throughput, not a measurement on these files. The post-April-2027 network-level files without ghost rates should be much smaller than the 2.x files, but by how much is unknown until they post.

## Storage, by tier

The workload has three very different access patterns, and matching medium to pattern is where the money goes right or wrong.

| Tier | Holds | Access pattern | Medium | Size |
|---|---|---|---|---|
| System and services | OS, containers and VMs, Postgres, DuckDB database files, the document store (ciphertext and, briefly, plaintext), the private key | Small random reads and writes, always on | NVMe, mirrored | 2 × 2 TB |
| Working | DuckDB spill and temp, Parquet before it is published, NDJSON intermediates if the pre-splitter route is taken | Fast sequential writes, parallel reads, high churn | NVMe, endurance rating matters more than peak speed | 2–4 TB |
| Raw archive | The compressed insurer files, last two months; the hospital raw files | Written once, read once sequentially, then idle | HDD pool in mirror pairs or RAIDZ, not SSD | 30–60 TB usable |
| Home storage | Photos, backups, media, whatever the house keeps | Mixed, mostly idle | The same HDD pool, its own dataset and quota | As wanted |

**Why not all NVMe.** The raw archive is the only large tier and it is read once, in order, at the speed the parser can take it, which a single hard disk at 200–250 MB/s already exceeds for the Python path. Hard disks cost a small fraction per terabyte of what SSDs do; SSD spent on files that sit idle for a month buys nothing. The two things to respect on spinning disk: do not stream several files from the same spindles at once (schedule one or two parses per mirror pair, or stage a file to the working tier first), and expect a resilver of a 20 TB disk to take a day.

**Bulk SATA SSD** sits between the two. It is the right answer if the box must be silent or if the raw tier shrinks (see the Open item on keeping raw files at all): if only hashes and Parquet are kept, the raw tier is one month's files in flight, a few terabytes, and a pair of large SATA SSDs covers it. Otherwise it is the hard-disk pool.

**Sizing the raw tier.** Carriers in scope × two months × 1–2 TB compressed, plus margin: five carriers lands at 10–20 TB with 30–60 TB usable as the comfortable pool. Do not buy for the 2.x format's worst case; the format changes in April 2027. Buy bays, not disks: a chassis with eight or more 3.5-inch bays half-filled is cheaper to grow than a full small one.

## Memory and cores

**Memory.** Sixteen parser processes at 1–4 GB each is 16–64 GB; DuckDB wants 16–32 GB; a ZFS cache wants 8–16 GB to be useful; the house's VMs want whatever they want. 128 GB is comfortable and leaves room for everything to run at once; 64 GB works with fewer parallel parses and DuckDB capped lower. ECC, because a ZFS pool holding the house's storage deserves it and because a bit flip in a price table is a quiet lie.

**Cores.** Sixteen modern cores (32 threads) is the sweet spot: a Python carrier-month fits in a night, a Go one in an hour, and a run never blocks the rest of the house. Eight cores is workable at two nights per carrier on the Python path. More than 24 cores buys nothing until there are more carriers than cores to give them.

**GPU.** None for the pipeline. Tesseract, Presidio, and spaCy run on CPU at Layer D's volumes. A modest card is worth adding only if a local model later joins the redaction review.

**Network.** The WAN link sets download time and nothing else does. On the LAN, 2.5 GbE is the floor and 10 GbE is worth it if storage and compute end up in separate boxes. Tailscale's WireGuard runs at a gigabit or two on a modern core, more than the GKE pull side will ever need.

## Virtualization and bare metal

The pipeline is CPU-bound streaming over sequential disk, which is the workload virtualization costs least: a KVM guest gives up a few percent of CPU. What costs real throughput is a virtual disk file on a copy-on-write store, or storage reached over a virtual network. So the host OS owns the disks (Proxmox, or plain Debian with ZFS), and the pipeline runs in an LXC container with the datasets bind-mounted, or in a VM with the NVMe passed through as a PCIe device and its cores pinned for the duration of a run. The k8s node is a VM or runs on the host; everything else is a VM. Bare metal for the parse step buys a few percent and costs the flexibility of running it beside everything else; it is the right call only as a second, dedicated box.

Three shapes that fit:

- **One consumer-platform desktop.** A current Ryzen 9 class part, 16 cores, up to 192 GB of memory with ECC on the boards that support it, quiet, 50–80 W idle. The constraint is PCIe lanes: two or three NVMe drives plus a host bus adapter for the disks plus a 10 GbE card is where these boards run out. Cheapest per core and the easiest to live with in a house.
- **One used server or workstation.** EPYC or Xeon Scalable from a couple of generations back, or a Threadripper Pro: registered ECC by the terabyte, lanes for everything, a chassis full of bays. Costs 100–200 W at idle, which at New Jersey residential rates (roughly $0.20–0.25 per kWh) is $175–450 a year before it does any work, and it wants a basement or a closet.
- **A NAS and a compute box.** The hard-disk pool in a quiet NAS; the parsing in a smaller, faster box that reads from it over 10 GbE. Each is simpler than the combined machine, each is a Tailscale node, and either can be replaced on its own. The working and system tiers live on the compute box.

The first is the recommendation for a box that is also the house's general server; the third if the house already has a NAS.

## If buying this year

A concrete envelope to price against, in the first shape:

- 16-core CPU on a board with ECC support; 128 GB ECC.
- 2 × 2 TB NVMe in a mirror for system and services.
- 1 × 4 TB NVMe, high endurance, for the working tier.
- 4 × 20 TB HDD as two mirror pairs (40 TB usable) in a chassis with at least eight bays, through a host bus adapter.
- 10 GbE NIC, a UPS, and a case the household can stand to hear.
- Linux host with ZFS, Tailscale on the host, the pipeline in a container or passthrough VM.

!!! warning "Unverified"
    Rough late-2026 costs: the compute, memory, and NVMe together on the order of a well-specified gaming PC; hard disks at roughly $15–20 per terabyte. Prices move; check before budgeting.

## Open

- Run the Layer B measurement on one Cigna network file and replace the estimates above with the numbers; then confirm cores and memory.
- Keep raw insurer files after extraction (two months, the current proposal) or keep only hashes and Parquet; this alone decides between a hard-disk pool and a pair of SSDs.
- One box or NAS plus compute, depending on what the house already runs.
- Backups: Parquet and configuration live in Git and releases, raw files are re-downloadable, the document store is deleted on schedule; whether anything on the box needs an off-box backup at all, and if so how it is encrypted.
- Where the private key lives: on disk, in a TPM, or on a hardware token plugged in for each run. See the custody item on [Intake path](intake.md#open).
