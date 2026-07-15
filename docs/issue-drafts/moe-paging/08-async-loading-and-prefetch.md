# [Phase 4] Add asynchronous expert loading and prefetch

Parent: `00-epic.md`
Depends on:
- `06-decode-expert-slot-cache.md`
- `07-expert-cache-telemetry.md`

## Objective

Overlap expert movement with useful computation and remove synchronous storage/transfer stalls from the decode critical path where possible.

## Work items

- Dedicated I/O worker or thread pool.
- Backend-aware staging buffers.
- Double buffering for slot replacement.
- Pinned host memory for discrete-GPU transfer where useful.
- Next-layer route-aware prefetch when routing information is available.
- Speculative prefetch based on recent routing history when exact lookahead is unavailable.
- Cancellation or deprioritization of stale prefetches.
- Backpressure and bounded queue memory.

## Correctness constraints

- A slot cannot be consumed until all required expert tensors are ready.
- Eviction cannot overwrite a slot still referenced by in-flight backend work.
- Failed reads must not leave partially valid expert state.
- Direct I/O alignment requirements must be respected.

## Measurements

- I/O/compute overlap ratio.
- Prefetch accuracy and wasted bytes.
- Queue latency.
- Decode latency distribution, not only average throughput.

## Acceptance criteria

- Warm and predictable workloads spend less time blocked at the router/expert boundary than the synchronous implementation.
- No slot lifetime or use-after-overwrite errors under stress tests.
- Prefetch can be disabled to recover the synchronous reference behavior.
