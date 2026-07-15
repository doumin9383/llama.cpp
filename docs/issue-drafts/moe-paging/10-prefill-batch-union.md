# [Phase 5] Implement prefill batch-union expert scheduling

Parent: `00-epic.md`
Depends on:
- `05-router-expert-execution-boundary.md`
- `06-decode-expert-slot-cache.md`

## Objective

Avoid loading the same expert repeatedly during prefill by collecting routing decisions for a token batch, grouping tokens by expert, and processing each unique expert once per layer/wave.

## Proposed flow

1. Run router selection for the layer's prefill micro-batch.
2. Build the unique expert set.
3. Build `expert -> token positions and routing weights` groups.
4. Load each expert once.
5. Run its FFN for all assigned token positions.
6. Scatter/accumulate weighted outputs.
7. Process in waves if the unique set exceeds available slots.

## Design questions

- Whether to sort/reorder activations or use indexed gather/scatter.
- Slot count and wave size selection.
- Interaction with `--batch-size` and `--ubatch-size`.
- Whether large prefill should bypass or protect the decode hot set.
- Temporary memory required for routing groups and expert outputs.

## Telemetry

- Unique experts per layer and micro-batch.
- Expert reuse factor.
- Number of waves.
- Bytes loaded per prefill token.
- Time in grouping, load, expert compute, and scatter.

## Acceptance criteria

- Prefill output matches the stock path.
- Repeated expert selections within a micro-batch cause only one expert load per wave.
- Working memory and slot use remain bounded.

Reference: https://github.com/JustVugg/colibri
