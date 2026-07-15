# [Epic] Generalized MoE expert paging and persistent KV cache

## Goal

Extend llama.cpp with two complementary mechanisms for bandwidth- and capacity-constrained local inference:

1. **Generalized MoE expert paging** inspired by Colibrì: keep dense/shared components resident while routed experts are managed dynamically across compute-visible memory, host/unified memory, and SSD.
2. **Persistent prefix/KV caching** inspired by DwarfStar4: reuse the longest compatible prompt prefix and persist reusable KV checkpoints to disk.

The main objective is to resist RAM, VRAM, PCIe, and SSD bandwidth limits by exploiting MoE sparsity and repeated-prefix workloads, without requiring the user to requantize models.

## Target systems

- Primary: one DGX Spark.
- Secondary: Ryzen 9 3950X + GTX 1660 Ti 6 GB + 64 GB RAM + NVMe.
- Initial scope: one model, one sequence, decode-first implementation.

## Proposed hierarchy

```text
FAST / compute-visible tier
├── embeddings, attention, norms, router
├── shared experts / dense FFN
├── LM head
├── KV cache
└── hot routed experts

WARM tier
└── host or unified-memory routed experts

COLD tier
└── SSD-backed routed experts and KV checkpoints
```

The tiers should be backend residency classes, not hard-coded CPU/GPU categories.

## Phases

- [ ] Baseline metrics and benchmark harness
- [ ] Persistent prefix/KV checkpoint store
- [ ] Cache compatibility keys and exact prompt replay
- [ ] Routed-expert tensor inventory and GGUF range index
- [ ] Router/expert execution boundary
- [ ] Decode-only per-layer expert slot cache
- [ ] Expert cache telemetry
- [ ] Async loading and prefetch
- [ ] Adaptive hot-expert pinning
- [ ] Prefill batch-union scheduling
- [ ] Multi-sequence / continuous-batching support
- [ ] MoE architecture adapter layer
- [ ] Compressed expert storage and transport
- [ ] Direct compressed-weight GEMV/GEMM
- [ ] DGX Spark and 3950X/1660 Ti benchmark matrix

## Initial non-goals

- Distributed inference or expert parallelism.
- Immediate support for every MoE architecture.
- A new quantization algorithm.
- High-throughput multi-user serving in the first implementation.
- A new KV codec before reuse and persistence work.

## References

- Colibrì: https://github.com/JustVugg/colibri
- DwarfStar4 / DS4: https://github.com/antirez/ds4
- BF16 weight compression: https://github.com/brianbell-x/weight-compression
- llama.cpp upstream: https://github.com/ggml-org/llama.cpp

## First PoC success criteria

- Correct logits for one supported MoE model.
- Dense/shared tensors remain resident.
- Routed experts load into fixed per-layer working slots on demand.
- `n_parallel = 1`, decode-only dynamic paging.
- Hit/miss, bytes loaded, eviction, and timing telemetry.
- Lower resident memory than the stock all-resident configuration.

## Long-term success criteria

- Async I/O and next-layer prefetch.
- Adaptive hot-expert pinning.
- Prefill batch-union.
- Persistent KV reuse across server requests and restarts.
- Multiple MoE architectures and continuous batching.
- Optional compressed expert transport and fused reconstruction.
