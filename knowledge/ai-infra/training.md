# Model Training: Concepts and Techniques

Concise entries on training state and memory. Parallel execution is covered in [[knowledge/ai-infra/parallelism|Parallelism and state sharding]].

## I. Training State and Memory

### 1. Parameters

**Core idea: The learned values that define the model's transformations.**

Examples include embedding tables, attention projection weights (`W_Q`, `W_K`, `W_V`, `W_O`), MLP weights and biases, and normalization scales and biases where present. Input-dependent Q/K/V tensors are activations, not learned parameters. [[knowledge/ai-infra/parallelism#5. ZeRO-3|ZeRO-3]] shards parameter storage across data-parallel workers.

### 2. Gradients

**Core idea: Derivatives of the loss with respect to trainable parameters, used to compute updates.**

Backward propagation computes `g_i = ∂loss / ∂θ_i`. In dense full-parameter training, each parameter has a corresponding gradient element; its storage precision need not match the parameter's precision. [[knowledge/ai-infra/training#4. Gradient Accumulation|Gradient accumulation]] combines gradients across microbatches before an update. [[knowledge/ai-infra/parallelism#4. ZeRO-2|ZeRO-2]] shards the reduced gradients retained for updates. [Source: PyTorch DDP tutorial](https://docs.pytorch.org/tutorials/intermediate/ddp_tutorial.html).

### 3. Optimizer State

**Core idea: Persistent update information, such as Adam's moving averages of gradients and squared gradients.**

Adam maintains a first moment `m` and a second moment `v` per parameter. Mixed-precision training may also keep FP32 master weights for accurate updates; [[knowledge/ai-infra/parallelism#2. ZeRO|ZeRO]]'s accounting includes these in optimizer state. [[knowledge/ai-infra/parallelism#3. ZeRO-1|ZeRO-1]] shards this state across data-parallel workers. [Source: DeepSpeed ZeRO documentation](https://deepspeed.readthedocs.io/en/latest/zero3.html).

**Memory example — mixed-precision Adam, before sharding.** Assume all parameters are trainable, with FP16 compute weights and gradients, plus FP32 master weights and Adam moments. The percentages below cover model state only.

| Component | Bytes per parameter | Share of model-state memory |
| --- | ---: | ---: |
| FP16 compute parameter | 2 | 12.5% |
| FP16 gradient | 2 | 12.5% |
| FP32 master parameter (optimizer state) | 4 | 25% |
| FP32 first moment `m` (optimizer state) | 4 | 25% |
| FP32 second moment `v` (optimizer state) | 4 | 25% |
| **Total** | **16** | **100%** |

In ZeRO's three categories, this is **2 bytes of parameters + 2 bytes of gradients + 12 bytes of optimizer state** per parameter. Thus, optimizer state accounts for 75% in this example. One billion parameters require about 16 GB (decimal) for these states per unsharded DP replica. [Source: ZeRO, Section 3.1](https://arxiv.org/html/1910.02054v3).

Total training memory also includes saved activations, operator workspaces, communication/gather buffers, and allocator overhead. Activations can be substantial, especially with long sequences or large microbatches. The 75% figure is not a share of total GPU memory; different precisions, optimizers, and master-weight policies change the accounting. [Source: ZeRO, Section 3.2](https://arxiv.org/html/1910.02054v3).

### 4. Gradient Accumulation

**Core idea: Accumulate gradients over multiple microbatches before updating parameters once.**

Each [[knowledge/ai-infra/parallelism#1. Microbatch|microbatch]] runs forward and backward using unchanged parameters. Its gradients accumulate in gradient buffers; after the configured number of microbatches, the optimizer updates parameters once, then gradients are cleared for the next accumulation window. Adam's moment estimates persist across updates.

For equal-sized microbatches and synchronized data-parallel replicas:

`effective global batch size = microbatch size per replica × accumulation steps × DP degree`

For example, 16 samples per microbatch × 4 accumulation steps × 2 DP replicas gives 128 samples per optimizer update. TP and PP degrees do not multiply the sample count. Loss or gradient scaling must produce the intended batch average; unequal microbatch sizes or valid-token counts require appropriate weighting.

This permits a larger effective batch without processing all samples simultaneously. It does not remove parameter or optimizer-state memory, and increasing the effective batch changes update frequency. In GPipe, gradients from the pipeline's microbatches accumulate before a synchronous update. [Sources: PyTorch DDP tutorial](https://docs.pytorch.org/tutorials/intermediate/ddp_tutorial.html); [GPipe](https://arxiv.org/abs/1811.06965).
