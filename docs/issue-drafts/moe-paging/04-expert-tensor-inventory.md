# [Phase 2] Add routed-expert tensor inventory and GGUF range index

Parent: `00-epic.md`

## Objective

Identify routed-expert tensors at model load time and expose enough metadata to read, stage, and replace experts independently without loading the entire expert set into compute-visible memory.

## Metadata

Record at least:

- Architecture identifier.
- Layer index and expert index.
- Tensor role: gate, up, down, fused gate/up, or architecture-specific role.
- GGUF shard/file identity.
- Byte offset, byte size, alignment, and checksum where useful.
- GGML type, shape, strides, and quantization block requirements.
- Current residency tier and working-slot assignment.

## Design constraints

- Dense FFN, shared experts, router, attention, embeddings, and LM head continue through the normal loader.
- Existing packed/quantized tensor bytes should remain opaque; no runtime requantization.
- Support sharded GGUF files.
- Avoid assumptions tied to a single tensor naming convention outside the architecture adapter.

## Deliverables

- Expert inventory data structure.
- Architecture-specific tensor classifier for the first target model.
- API to read an expert tensor range into a caller-provided backend buffer.
- Validation tool that prints a complete per-layer expert map.

## Acceptance criteria

- Every routed expert tensor in the target GGUF is indexed exactly once.
- Shared/dense tensors are not misclassified.
- Indexed ranges reproduce the same tensor bytes as the stock loader.
