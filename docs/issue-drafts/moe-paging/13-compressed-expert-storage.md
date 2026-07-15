# [Low priority] Prototype compressed expert storage and transport

Parent: `00-epic.md`
Depends on a stable expert pager and telemetry.

## Objective

Evaluate lossless or exact-reconstruction compression for BF16 routed experts to reduce SSD capacity, SSD bandwidth, warm-tier capacity, and eventually host/device transfer bytes.

Reference research: https://github.com/brianbell-x/weight-compression

## Staged implementation

### Stage A: compressed SSD representation

- Store expert tensor payloads in a compressed container.
- Read and decode into the normal packed/BF16 representation before entering the warm tier.
- Measure storage reduction, decode CPU cost, and cold-load latency.

### Stage B: compressed warm tier

- Retain compressed experts in RAM/unified memory.
- Decode only when promoting to a compute-visible slot.
- Measure effective warm-tier capacity and cache-hit improvement.

### Stage C: compressed transport

- Transfer compressed payloads to the compute device when the backend supports reconstruction.
- Avoid decode/re-encode between tiers.

## Constraints

- Expert-granular and block-addressable container format.
- Fixed or bounded metadata overhead.
- Preserve exact tensor values for the lossless path.
- Support checksums/versioning and safe fallback to ordinary GGUF bytes.
- Do not couple the initial expert pager to one experimental codec.

## Experiments

- BF16 expert tensors from at least two model families.
- Compression ratio by tensor role and layer.
- Encode/decode throughput.
- End-to-end cold miss latency.
- RAM cache hit-rate change at fixed capacity.
- FP8 distribution scan to determine whether meaningful exact compression remains.

## Acceptance criteria

- Stage A produces bit-exact expert tensors and a measurable reduction in bytes read from SSD.
- The codec remains optional and does not change the uncompressed execution path.
- Results clearly separate storage compression from runtime speedup claims.
