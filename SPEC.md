# SPEC.md: "The Human How"; Exact technical requirements, environment setup, implementation details, etc.

## Execution Description

Compiles an embedded C OpenMP GUPS kernel with gcc and runs it under perf stat for dTLB and LLC misses when perf is available. NumPy random updates run only if gcc fails. table_size, num_updates, num_iterations, and seed come from yaml. This is not a separate gups_benchmark.cpp binary. Sweep dimensions: numa_node, num_threads, thread_affinity, page_size, table_size, num_updates, num_iterations, seed.

## Parameters

| Parameter | CLI Flag | Tested Values | Default | Description |
| --- | --- | --- | --- | --- |
| numa_node | `--numa-node` | smoke=0, baseline=0, extended=0 | 0 | From Parameter list; see Execution Description With Parameters. |
| num_threads | `--num-threads` | smoke=1, baseline=4, extended=8 | 4 | From Parameter list; see Execution Description With Parameters. |
| thread_affinity | `--thread-affinity` | smoke=node:0, baseline=node:0, extended=node:0 | node:0 | From Parameter list; see Execution Description With Parameters. |
| page_size | `--page-size` | smoke=4KB, baseline=4KB, extended=4KB | 4KB | From Parameter list; see Execution Description With Parameters. |
| table_size | `--table-size` | smoke=4194304, baseline=268435456, extended=1073741824 | 268435456 | From Parameter list; see Execution Description With Parameters. |
| num_updates | `--num-updates` | smoke=1000000, baseline=50000000, extended=200000000 | 50000000 | From Parameter list; see Execution Description With Parameters. |
| num_iterations | `--num-iterations` | smoke=1, baseline=1600, extended=1900 | 1600 | From Parameter list; see Execution Description With Parameters. |
| seed | `--seed` | smoke=42, baseline=42, extended=42 | 42 | From Parameter list; see Execution Description With Parameters. |

## Invocation

```bash
Compile an embedded C OpenMP GUPS kernel with gcc and run it under perf stat when available. NumPy is only the gcc fallback
```

## Raw Output Format

CSV with one kind=sample row and one kind=summary row

kind,seconds,updates,total_updates,gups,giga_updates_score,errors,updates_sec,table_size,num_updates,elapsed_sec,tlb_miss_rate,l3_miss_rate,payload_update_gb_s
sample,1.5,1000000,1000000,0.000667,0.000667,0,666666,4194304,1000000,1.5,0.02,0.4,0.0053

## Metrics

- **#1: Giga-updates score** — stored as `giga_updates_score`.
- **#2: Random 64-bit update rate** — stored as `updates_sec`.
- **#3: 8-byte payload update rate, GB/s** — stored as `payload_update_gb_s`.
- **#4: TLB miss rate** — stored as `tlb_miss_rate`.
- **#5: L3 cache miss rate** — stored as `l3_miss_rate`.

## Framework

Compiles an embedded C OpenMP GUPS kernel with gcc and runs it under perf stat for dTLB and LLC misses when perf is available. NumPy random updates run only if gcc fails. table_size, num_updates, num_iterations, and seed come from yaml.

## Installation and Execution Summary

Compile an embedded C OpenMP GUPS kernel with gcc and run it under perf stat for dTLB and LLC misses when perf is available. NumPy random updates run only if gcc fails. Derive gups from the elapsed update count, to measure random update rate

## Platform Portability

- **AMD (primary):** ```bash
Compile an embedded C OpenMP GUPS kernel with gcc and run it under perf stat when available. NumPy is only the gcc fallback
```
- **NVIDIA:** Primary target is AMD ROCm. NVIDIA notes in this section are reference only and are not the execution path.

## Model Context Protocols

- **Active:** None

## Execution-Loop Validation Contract

EXECUTION CHAIN: `run_benchmark.sh` ➔ raw output ➔ `scripts/parse_results.py` ➔ `results/benchmark.db` ➔ `scripts/validate_results.py`

This benchmark uses a lightweight, SQLite-integrated execution loop for result validation. All validation is performed by `scripts/validate_results.py`.

### Validation script usage

```bash
export BENCHMARK_PYTHON=/usr/bin/python3.13  # optional; select the installed interpreter

# After a live run:
".venv/bin/python" scripts/validate_results.py --db results/benchmark.db

# CI / no-GPU path (seeds fixture and validates it):
".venv/bin/python" scripts/validate_results.py --seed-fixture --quiet

# Override DB path via environment variable:
BENCHMARK_DB=tests/fixtures/benchmark.db \
  ".venv/bin/python" scripts/validate_results.py
```

### Run artifact contract

CSV with one kind=sample row and one kind=summary row

kind,seconds,updates,total_updates,gups,giga_updates_score,errors,updates_sec,table_size,num_updates,elapsed_sec,tlb_miss_rate,l3_miss_rate,payload_update_gb_s
sample,1.5,1000000,1000000,0.000667,0.000667,0,666666,4194304,1000000,1.5,0.02,0.4,0.0053

```bash
bash run_benchmark.sh --help
bash run_benchmark.sh --profile smoke --validate
bash run_benchmark.sh --profile baseline --validate
bash run_benchmark.sh --profile extended --validate
```
`run_benchmark.sh --help` prints usage and exits. The harness calls `scripts/ensure_setup.sh` when `.setup_state` is absent.

### Required integrity checks (built into `validate_results.py`)

1. Latest run exists and `runs.status = 'ok'`.
2. `run.error_message` is NULL.
3. `started_at` and `finished_at` are valid ISO-8601 UTC strings.
4. All required aggregate metrics in `runs` are non-NULL and finite.
5. All required aggregate metrics are physically sensible (positive values). Compiles an embedded C OpenMP GUPS kernel with gcc and runs it under perf stat for dTLB and LLC misses when perf is available. NumPy random updates run only if gcc fails. table_size, num_updates, num_iterations, and seed come from yaml.
6. At least 2 sample rows exist for the latest `run_id` (sweep coverage).
7. No sample has `status = 'error'`.
8. Compiles an embedded C OpenMP GUPS kernel with gcc and runs it under perf stat for dTLB and LLC misses when perf is available. NumPy random updates run only if gcc fails. table_size, num_updates, num_iterations, and seed come from yaml.

### Baseline / Threshold configuration (`config/benchmark_config.yaml`)

Expected ranges and gates live in `config/benchmark_config.yaml` under `baselines:` or `thresholds:`. To update them, edit that file — never edit validation code directly.

Threshold key suffixes encode comparison direction when `thresholds:` is present: `_min` → observed value must be ≥ threshold. `_max` → observed value must be ≤ threshold. Informational `baselines:` ranges are not pass/fail gates.
