# [Phase 0] Establish baseline metrics and benchmark harness

Parent: `00-epic.md`

## Objective

Measure the current llama.cpp behavior before adding dynamic expert paging or persistent KV caching. The benchmark must make memory residency and bandwidth movement visible, not only report tokens per second.

## Required metrics

- Prompt-processing and decode tokens/sec.
- Time per layer and time in MoE operations.
- RAM and device-memory residency.
- Host/device transfer bytes and time.
- SSD bytes read and read latency.
- KV cache size and growth per token.
- Per-layer routing histogram.
- Unique experts selected per decode step and prefill micro-batch.
- Cold-start versus warm-start behavior.

## Test matrix

- DGX Spark.
- Ryzen 9 3950X + GTX 1660 Ti 6 GB + 64 GB RAM.
- `n_parallel = 1` initially.
- Short and long context.
- Cold filesystem/page cache and warm cache.
- Existing supported MoE model with stock placement.

## Deliverables

- Reproducible benchmark command/script.
- Machine-readable CSV or JSON output.
- A short baseline report checked into the repository.
- Hooks that later expert-pager work can reuse.

## Acceptance criteria

- A single command produces comparable runs on both target systems.
- The output distinguishes compute time, host/device transfer, and storage I/O where available.
- Routing and unique-expert data are available per layer.
