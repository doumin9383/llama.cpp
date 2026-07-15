# [Phase 1] Add a persistent prefix/KV checkpoint store

Parent: `00-epic.md`

## Objective

Reuse the longest compatible prompt prefix instead of repeating prefill, and persist reusable KV checkpoints to disk for coding-agent and long-session workloads.

## Proposed behavior

- Keep one or more live KV states in memory.
- Find the longest compatible token or rendered-byte prefix for a new request.
- Resume prefill from the matching checkpoint.
- Save selected checkpoints to disk.
- Restore checkpoints across process restarts.
- Enforce a disk-space budget and LRU-style eviction.

## Initial scope

- Single model and server process.
- Exact token-prefix matching first.
- Existing llama.cpp KV types and state serialization.
- No new KV compression codec.

## Suggested checkpoint policy

- Minimum token count before persistence.
- Periodic checkpoints aligned with prefill chunk boundaries.
- Save on explicit session persistence, eviction, and clean shutdown.
- Avoid checkpoints too close to unstable tokenizer/render boundaries.

## Telemetry

- Prefix hit length.
- Bytes restored and written.
- Prefill tokens avoided.
- Restore/write time.
- Disk hit count and eviction count.

## Acceptance criteria

- A repeated long system/tool prefix avoids prefill from token zero.
- A server restart can restore a compatible checkpoint.
- Incompatible checkpoints are rejected safely.
- Cache size remains within the configured disk budget.

Reference: https://github.com/antirez/ds4
