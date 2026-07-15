# [Phase 3] Implement decode-only per-layer expert slot cache

Parent: `00-epic.md`
Depends on:
- `04-expert-tensor-inventory.md`
- `05-router-expert-execution-boundary.md`

## Objective

Implement the first working expert pager for single-token, single-sequence decode.

## Initial constraints

- One supported MoE architecture.
- `n_parallel = 1`.
- Decode only; prefill may use the stock path or require all experts resident.
- Synchronous expert loads.
- Fixed working-slot count per layer.
- Per-layer LRU replacement.

## Slot model

Each layer owns a fixed set of backend tensors whose addresses remain stable:

```text
layer 10
  slot 0 -> expert 37
  slot 1 -> expert 122
  slot 2 -> expert 4
```

On a miss, evict an unused slot and replace its packed tensor bytes. Graphs reference stable slot tensors rather than model-wide expert tensors.

## Required behavior

- Pin experts needed by the in-flight token until layer completion.
- Load all tensor components of an expert atomically from the scheduler's perspective.
- Preserve original GGML type and packed representation.
- Fail clearly if top-k exceeds available simultaneously usable slots.

## Acceptance criteria

- Correct decode for the target model with only a subset of routed experts resident.
- Resident memory scales with slot count rather than total expert count.
- Warm-cache decode demonstrates fewer bytes loaded than cold-cache decode.
- No stale-slot or wrong-expert output under repeated eviction.

Reference: https://github.com/JustVugg/colibri
