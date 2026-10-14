# AI Infrastructure

## Scope And Taxonomy

AI Infrastructure is best understood as a matrix rather than four mutually exclusive buckets. **Data, training, and inference** describe workload domains in the model lifecycle, while **operators, runtimes, and platforms** describe layers of the systems stack. A paper may therefore occupy more than one cell: vLLM, for example, is an inference system whose contribution spans a custom attention kernel and a serving runtime.

| Workload domain | Operators, kernels, and compilers | Runtime and execution engine | Distributed platform and control plane |
|---|---|---|---|
| Data infrastructure | Parsing, decoding, tokenization, compression, and preprocessing operators | Input pipelines, caching, shuffling, and data-loading runtimes | Dataset storage, catalogs, versioning, quality validation, and lineage |
| Training and adaptation | Attention, GEMM, communication, optimizer, and fused training kernels | Model/data/pipeline parallel execution, memory management, and checkpoint runtimes | Job scheduling, elasticity, fault recovery, experiment management, and training fleets |
| Inference and serving | Attention, quantization, sampling, and decoding kernels | Request scheduling, batching, KV-cache management, and serving engines such as Orca, vLLM, and Sarathi-Serve | Deployment, routing, autoscaling, phase/resource placement, and multi-instance systems such as DistServe and Llumnix |

Hardware and interconnect form the foundation beneath the matrix: accelerators, HBM, PCIe, NVLink, InfiniBand, RoCE, and cluster topology constrain every workload and system layer.

The following concerns cut across the whole matrix:

- Scheduling and resource management.
- Reliability, fault tolerance, and elasticity.
- Observability, profiling, testing, and debugging.
- Cost, utilization, and energy efficiency.
- Security, privacy, and multi-tenant isolation.

For navigation, classify each paper along both axes whenever useful: at least one workload domain and one systems layer. The familiar categories “training,” “inference,” “operators,” and “platform” remain useful reading lanes, but overlap between them is expected rather than treated as a taxonomy error.

## 2026-05

- [[paper-vault/papers/wang2025jenga]]
  - Title: JENGA: Enhancing LLM Long-Context Fine-tuning with Contextual Token Sparsity
  - Workload domain: Training and adaptation.
  - System layer: Runtime and execution engine; operator and kernel optimization.
  - Why listed here: Co-designs contextual token sparsity, pattern prediction, and permutation-free kernels to reduce activation memory and computation in long-context fine-tuning.

## 2026-08

- [[paper-vault/papers/yu2022orca]]
  - Title: Orca: A Distributed Serving System for Transformer-Based Generative Models
  - Workload domain: Inference and serving.
  - System layer: Runtime and distributed execution engine.
  - Why listed here: Moves scheduling to token iterations and pairs it with selective batching to continuously refill model-parallel inference batches.
- [[paper-vault/papers/kwon2023pagedattention]]
  - Title: Efficient Memory Management for Large Language Model Serving with PagedAttention
  - Workload domain: Inference and serving.
  - System layer: KV-memory runtime and scheduler; attention kernel.
  - Why listed here: Co-designs paged KV-cache allocation, PagedAttention, sharing, and preemption so fixed GPU memory supports more concurrent sequences.
