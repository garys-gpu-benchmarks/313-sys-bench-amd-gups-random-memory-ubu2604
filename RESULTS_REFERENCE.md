# Results: 313 GUPS Random Memory

**Main goal:** Measure random 64-bit memory updates with a compiled C OpenMP kernel  
**Tools:** Compile the C OpenMP GUPS kernel with gcc and run it under perf stat when available

Reference results measured by the maintainer on the system shown in each section (generator TEMPLATE_00_138 · recorded 2026-11-12). Your own runs write `results/RESULTS_ACTUAL.md` in the same format.

> Smoke runs use deliberately small sizes. They check that the workload installs and runs end to end; their numbers are not performance results. Baseline and extended are.

<!-- profile:smoke -->
## smoke

- **System:** 1× AMD Instinct MI300X VF (196288 MB) · INTEL(R) XEON(R) PLATINUM 8568Y+ · Ubuntu 26.04.1 LTS · kernel 7.0.0-38-generic · driver 7.1.9.31600000
- **Run:** 2026-11-12T23:02:22Z · status **N/A-EXPECTED** (exit 0)
- **Time (mm:ss):** setup 00:06 · benchmark 00:00 · whole run 00:15
- **Source:** run 313_20261112_230222
- **Command:** `bash run_benchmark.sh --smoke --numa-node 0 --num-threads 1 --thread-affinity node:0 --table-size 4194304 --num-updates 1000000 --num-iterations 1 --seed 42`

| # | Metric | Result |
|---|---|---|
| 1 | Giga-updates score (giga_updates_score) | 0.01093195614881921 |
| 2 | Random 64-bit update rate (updates_sec) | 10931956.14881921 |
| 3 | 8-byte payload update rate, GB/s (payload_update_gb_s) | 0.08745564919055368 |
| 4 | TLB miss rate (tlb_miss_rate) | 0.013257296685186288 |
| 5 | L3 cache miss rate (l3_miss_rate) | na (expected: LLC load counters not exposed on this host) |
<!-- /profile:smoke -->
