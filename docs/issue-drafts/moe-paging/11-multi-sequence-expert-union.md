# [Phase 6] Support multi-sequence and continuous-batching expert union

Parent: `00-epic.md`
Depends on:
- `10-prefill-batch-union.md`
- `08-async-loading-and-prefetch.md`

## Objective

Extend expert paging from `n_parallel = 1` to llama.cpp server workloads where multiple independent sequences contribute tokens to a shared decode batch.

## Proposed scheduling

- Collect selected experts across all active sequence/token rows.
- Build a unique expert union for each layer.
- Group rows by expert.
- Load each required expert once per wave.
- Execute the expert for all assigned rows and combine outputs per sequence.

## Additional policy requirements

- Cache fairness between requests.
- Decode latency protection against large prefill requests.
- Request priority and cancellation handling.
- Avoid one sequence destroying another sequence's useful hot set.
- Track usage by both global expert heat and per-request contribution.

## Risks

- Unique expert count can grow rapidly with parallel sequences.
- Batch union may approach all-expert execution for high concurrency.
- Slot waves can increase tail latency.
- Continuous batching changes the active request set between steps.

## Acceptance criteria

- Correct output for multiple active sequences.
- No duplicate loads for the same expert within a wave.
- Bounded and configurable slot/cache behavior under high expert diversity.
- Metrics expose when paging is no longer beneficial versus all-resident execution.
