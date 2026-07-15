# [Validation] Benchmark DGX Spark and 3950X/1660 Ti target configurations

Parent: `00-epic.md`
Depends on the relevant implementation phases.

## Objective

Validate the design on both a large unified-memory system and a small discrete-GPU system, exposing where each tiering policy helps or hurts.

## Systems

### Primary

- One DGX Spark.
- Focus on fitting dense/shared components, KV cache, and a configurable hot expert set in the fastest usable memory.
- No user-side requantization requirement.

### Secondary

- Ryzen 9 3950X.
- GTX 1660 Ti 6 GB.
- 64 GB system RAM.
- NVMe SSD.
- Focus on discrete VRAM, host-memory warm tier, PCIe transfer, and SSD cold tier.

## Benchmark dimensions

- Stock all-resident or static-offload baseline.
- Cold and warm expert cache.
- Different fast/warm slot budgets.
- Short and long context.
- Prefix cache miss, RAM hit, and disk hit.
- `n_parallel = 1`, then higher values after support exists.
- Prefill and decode separately.
- Existing quantized expert formats and higher-precision formats where available.

## Metrics

- Tokens/sec and latency percentiles.
- RAM/VRAM residency.
- SSD and host/device bytes per token.
- Expert hit rate and working-set size.
- KV checkpoint bytes and prefill tokens avoided.
- Power/thermal notes where useful.

## Acceptance criteria

- Reproducible results and commands are recorded for both systems.
- The report identifies the dominant bottleneck for each configuration.
- The project documents when paging is beneficial and when static/all-resident placement is faster.
