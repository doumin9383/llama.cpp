# [Generalization] Add an MoE architecture adapter interface

Parent: `00-epic.md`
Depends on a working first-model pager.

## Objective

Separate architecture-specific routing and tensor-layout rules from the generic expert storage, residency, I/O, caching, and scheduling runtime.

## Candidate responsibilities

```cpp
struct moe_arch_adapter {
    bool is_routed_expert_tensor(...);
    bool is_shared_expert_tensor(...);
    expert_tensor_layout get_expert_layout(...);
    router_result build_or_run_router(...);
    void normalize_router_weights(...);
    void combine_expert_outputs(...);
    bool supports_batch_union(...);
};
```

## Architecture differences to isolate

- Softmax versus sigmoid router.
- Router bias and no-auxiliary-loss routing variants.
- Grouped routing.
- Top-k normalization and routed scaling factors.
- Shared experts.
- Dense initial/final MoE layers.
- Fused or separate gate/up projections.
- Activation function.
- Expert tensor naming and packing.
- Architecture-specific attention interactions such as MLA.

## Runtime responsibilities that should remain generic

- GGUF range index.
- Residency tiers and working slots.
- Load/prefetch pipeline.
- Eviction and hot-set policy.
- Batch/sequence expert grouping.
- Telemetry.

## Acceptance criteria

- The first supported model is converted to use the adapter without regression.
- A second MoE architecture can be added without duplicating the pager/cache implementation.
- Unsupported architecture semantics fail explicitly rather than silently using defaults.
