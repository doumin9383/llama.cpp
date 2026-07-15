# [Phase 4] Add adaptive hot-expert pinning

Parent: `00-epic.md`
Depends on:
- `07-expert-cache-telemetry.md`
- `08-async-loading-and-prefetch.md`

## Objective

Maintain a stable hot set outside or above normal LRU replacement so frequently reused routed experts remain in the fastest tier.

## Proposed policy evolution

1. Static user-configured pinned experts.
2. Offline or warmup routing-frequency profile.
3. Runtime frequency/recency score such as LFRU.
4. Periodic adaptation when workload distribution changes.

## Requirements

- Per-layer hot-set budget.
- Separate accounting for pinned and replaceable slots.
- Minimum residency duration to avoid oscillation.
- Decay or time window so stale workloads do not pin experts forever.
- Safe promotion/demotion without disrupting in-flight work.
- Persist or export learned profiles optionally.

## Policy inputs

- Routing count and recent routing count.
- Miss cost and source tier.
- Expert byte size.
- Reuse distance.
- Optional model- or workload-provided profile.

## Acceptance criteria

- Stable workloads achieve higher hit rate than pure LRU at the same fast-tier capacity.
- A changed workload eventually replaces stale pinned experts.
- Adaptation overhead and churn are observable through telemetry.

Reference: https://github.com/JustVugg/colibri
