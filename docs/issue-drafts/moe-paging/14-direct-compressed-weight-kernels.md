# [Research] Evaluate direct compressed-weight GEMV/GEMM kernels

Parent: `00-epic.md`
Depends on: `13-compressed-expert-storage.md`

## Objective

Investigate keeping exact-compressed weights in compute-visible memory and reconstructing values in registers or immediately before multiply-accumulate, so compression reduces VRAM capacity, device-memory bandwidth, and host/device transfer at the same time.

Reference research: https://github.com/brianbell-x/weight-compression

## Scope

- Start with decode-oriented GEMV for one tensor shape/type.
- Implement dense reconstruction path first.
- Add sparse exact-correction handling and include it in timed kernels.
- Compare with native BF16 and existing quantized kernels.
- Keep the format optional and isolated behind a backend/type interface.

## Questions

- Can reconstruction be fused without reducing occupancy excessively?
- How should rare escape/correction values be represented and applied?
- Is one pass with predicated correction better than a second sparse pass?
- Does the benefit survive small expert matrices, not only bandwidth-saturating synthetic sizes?
- Can the same compressed bytes move unchanged from SSD to RAM to device memory?

## Required measurements

- End-to-end exact output validation.
- Effective bytes read per weight.
- Kernel time and achieved bandwidth.
- Register pressure, occupancy, and divergence.
- Correction-path overhead.
- Decode performance inside a real MoE layer.

## Acceptance criteria

- Timed results include the complete exact reconstruction/correction path.
- Numerical output is bit-exact where the codec promises bit-exact reconstruction.
- Results distinguish microbenchmark gains from end-to-end serving gains.
