# MoE paging and persistent KV cache issue drafts

These files are intended to be converted into GitHub Issues. They were committed as a temporary record because Issues are currently disabled for this repository.

## Roadmap

1. [`00-epic.md`](00-epic.md) — project epic and overall design
2. [`01-baseline-metrics.md`](01-baseline-metrics.md) — baseline benchmark harness
3. [`02-persistent-prefix-kv-store.md`](02-persistent-prefix-kv-store.md) — persistent prefix/KV checkpoints
4. [`03-cache-compatibility-and-exact-replay.md`](03-cache-compatibility-and-exact-replay.md) — safe cache keys and exact replay
5. [`04-expert-tensor-inventory.md`](04-expert-tensor-inventory.md) — routed-expert GGUF range index
6. [`05-router-expert-execution-boundary.md`](05-router-expert-execution-boundary.md) — split routing from expert execution
7. [`06-decode-expert-slot-cache.md`](06-decode-expert-slot-cache.md) — first decode-only expert pager
8. [`07-expert-cache-telemetry.md`](07-expert-cache-telemetry.md) — cache and bandwidth metrics
9. [`08-async-loading-and-prefetch.md`](08-async-loading-and-prefetch.md) — asynchronous I/O and prefetch
10. [`09-adaptive-hot-expert-pinning.md`](09-adaptive-hot-expert-pinning.md) — adaptive pinned hot set
11. [`10-prefill-batch-union.md`](10-prefill-batch-union.md) — prefill expert grouping
12. [`11-multi-sequence-expert-union.md`](11-multi-sequence-expert-union.md) — continuous batching support
13. [`12-moe-architecture-adapter.md`](12-moe-architecture-adapter.md) — architecture abstraction
14. [`13-compressed-expert-storage.md`](13-compressed-expert-storage.md) — optional exact-compressed storage/transport
15. [`14-direct-compressed-weight-kernels.md`](14-direct-compressed-weight-kernels.md) — direct reconstruction kernels
16. [`15-target-hardware-benchmarks.md`](15-target-hardware-benchmarks.md) — DGX Spark and 3950X/1660 Ti validation

## Suggested creation order

Create the epic first, then create child issues in numeric order. Update the epic checklist with the resulting issue links.

## References

- https://github.com/JustVugg/colibri
- https://github.com/antirez/ds4
- https://github.com/brianbell-x/weight-compression
- https://github.com/ggml-org/llama.cpp
