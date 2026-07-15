# [Phase 1] Define KV cache compatibility keys and exact prompt replay

Parent: `00-epic.md`
Depends on: `02-persistent-prefix-kv-store.md`

## Objective

Prevent incorrect KV reuse when model, tokenizer, rendering, context, or backend details differ. Preserve exact rendered prompt bytes where protocol/tool-call round trips would otherwise change formatting.

## Compatibility key inputs

At minimum include:

- Model and GGUF identity, including relevant tensor/metadata hash.
- Tokenizer identity and special-token configuration.
- Chat template and rendered prompt bytes or token sequence.
- RoPE/scaling configuration.
- KV K/V data types and layout version.
- Context and sliding-window settings.
- Backend-sensitive state format version.
- Prefill chunk/micro-batch configuration when it changes state construction.

## Exact replay

For generated tool calls or protocol blocks:

- Store the exact sampled/rendered byte sequence associated with a stable request/tool identifier.
- Reuse those bytes on the next turn rather than reconstructing an approximately equivalent representation.
- Provide deterministic canonicalization only as a fallback.

## Deliverables

- Versioned cache-key schema.
- Compatibility validation before restore.
- Diagnostics explaining why a candidate checkpoint was rejected.
- Tests for model, tokenizer, template, KV type, and prompt mismatch.

## Acceptance criteria

- No checkpoint is restored across incompatible configurations.
- Exact replay preserves prefix identity for supported tool-call flows.
- Cache keys remain stable across process restarts.

Reference: https://github.com/antirez/ds4
