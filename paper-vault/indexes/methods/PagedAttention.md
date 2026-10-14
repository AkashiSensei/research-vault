# PagedAttention

## 2026-08

- [[paper-vault/papers/kwon2023pagedattention]]
  - Title: Efficient Memory Management for Large Language Model Serving with PagedAttention
  - Role in paper: Attention primitive that makes vLLM's non-contiguous KV layout executable.
  - Mechanism: Computes exact attention by following logical-to-physical block tables over fixed-size KV blocks.
  - Why listed here: Makes on-demand KV allocation and sharing usable without first compacting state into contiguous memory.
