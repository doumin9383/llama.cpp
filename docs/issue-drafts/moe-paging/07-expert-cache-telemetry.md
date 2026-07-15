# [Phase 3] Add expert-cache telemetry and benchmark reporting

Parent: `00-epic.md`
Depends on: `06-decode-expert-slot-cache.md`

## Objective

Make expert paging behavior observable enough to distinguish compute, memory bandwidth, storage I/O, and policy failures.

## Metrics

Per run and, where practical, per layer:

- Expert requests, hits, misses, and hit rate.
- Bytes loaded by source tier.
- Load latency and queueing latency.
- Evictions and pinned-slot conflicts.
- Expert residence time and reuse distance.
- Router frequency histogram.
- Unique experts per token/micro-batch.
- Time spent waiting at the router/expert boundary.
- Expert compute time versus load time.
- Effective tokens/sec for cold and warm states.

## Interfaces

- Human-readable periodic log.
- Machine-readable JSON/CSV output.
- Optional trace-level event stream for profiling.
- Counters exposed through llama-server monitoring endpoints where appropriate.

## Acceptance criteria

- A benchmark can explain why a run is storage-, host-memory-, transfer-, or compute-bound.
- Cache-policy regressions are detectable without adding ad hoc logging.
- Telemetry overhead is negligible when disabled and bounded when enabled.
