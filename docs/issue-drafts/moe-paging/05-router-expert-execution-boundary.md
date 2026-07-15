# [Phase 3] Introduce a router/expert execution boundary

Parent: `00-epic.md`
Depends on: `04-expert-tensor-inventory.md`

## Objective

Create a point in MoE execution where routing results are available before expert computation, allowing the runtime to ensure that selected experts are resident.

## Initial approach

Split each MoE layer into two execution stages:

```text
Stage A
  attention / residual work
  router logits and top-k selection

Host/runtime scheduling point
  inspect selected expert IDs
  resolve slot hits and misses
  load missing expert tensors

Stage B
  expert FFN operations
  routing-weighted combine
```

This is preferred for the first PoC over adding a fully custom backend op that performs routing, paging, and expert compute internally.

## Requirements

- Preserve existing logits within the expected numerical tolerance.
- Support the first target architecture's router semantics.
- Avoid rebuilding unrelated portions of the graph for each token.
- Make synchronization cost visible in telemetry.
- Leave a path toward a fused/custom op later.

## Risks to investigate

- Graph allocation and reuse when expert slot contents change.
- Device-to-host synchronization for route IDs.
- Dynamic batch shape or topology changes.
- Interaction with CUDA graph capture and backend scheduling.

## Acceptance criteria

- Selected expert IDs can be obtained before expert matmuls execute.
- A callback or scheduler can populate slots and resume the layer.
- Stock all-resident execution and split execution produce matching outputs.
