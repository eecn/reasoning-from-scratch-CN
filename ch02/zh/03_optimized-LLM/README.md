# Optimized Qwen3 优化的 Qwen3

The Qwen3 from-scratch implementation used in this book strikes a balance between being efficient (both on CPU and GPU) and lean while remaining easy to read by a human. 

本书中使用的Qwen3从零开始实现，在CPU和GPU上都具有高效性，同时保持代码的简洁性和可读性。

As an alternative, you can use the optional `Qwen3Model` drop-in replacement, which is slightly more GPU-efficient. The optimized version in [`qwen3_optimized.py`](../../../reasoning_from_scratch/qwen3_optimized.py) (discussed further in Appendix C) differs from the baseline implementation in [`qwen3.py`](../../../reasoning_from_scratch/qwen3.py) in two key ways:

作为替代方案，你可以使用可选的`Qwen3Model`替换，它在GPU上稍微高效一些。附录C中讨论的[`qwen3_optimized.py`](../../../../reasoning_from_scratch/qwen3_optimized.py)中的优化版本与基线实现[`qwen3.py`](../../../reasoning_from_scratch/qwen3.py)在两个方面有所不同：

- It implements attention using PyTorch’s built-in `torch.nn.functional.scaled_dot_product` instead of a custom implementation.
- It introduces a modified `KVCache` that pre-allocates key/value tensors. This increases memory usage but avoids repeatedly allocating new storage during execution.

- 它使用PyTorch的内置`torch.nn.functional.scaled_dot_product`实现注意力，而不是自定义实现。
- 它引入了一个修改后的`KVCache`，该缓存预分配键/值张量。这增加了内存使用量，但在执行过程中避免了反复分配新存储。


To explore the differences, I recommend opening [`qwen3.py`](../../../reasoning_from_scratch/qwen3.py) and [`qwen3_optimized.py`](../../../reasoning_from_scratch/qwen3_optimized.py)  side by side and/or looking at a file-diff:

要探索差异，我建议你并排打开[`qwen3.py`](../../../reasoning_from_scratch/qwen3.py)和[`qwen3_optimized.py`](../../../reasoning_from_scratch/qwen3_optimized.py)，或者查看文件差异：

<br>

![](https://sebastianraschka.com/images/reasoning-from-scratch-images/bonus/optimized-LLM/vscode.webp)

<br>

&nbsp;
## How to use 如何使用

The optimized code can be used as drop-in replacement for the code used in the main chapters as shown below.

优化后的代码可以作为主章节中使用的代码的替代品，如下所示使用：

**Before:** 之前

```python
from reasoning_from_scratch.qwen3 import Qwen3Model
from reasoning_from_scratch.ch02 import generate_text_basic_cache
```


**After:** 之后

```python
from reasoning_from_scratch.qwen3_optimized import Qwen3Model
from reasoning_from_scratch.qwen3_optimized import generate_text_basic_cache
```

&nbsp;
## How to run comparisons 如何运行比较

To evaluate the performance on your system, you can use the [`compare_inference.py`](compare_inference.py) function contained in this folder:

要评估你系统上的性能，你可以使用此文件夹中包含的[`compare_inference.py`](compare_inference.py)函数：
```python
python compare_inference.py
```

or

```python
uv run compare_inference.py
```

Then, add the following flags: 然后，添加以下标志：

- `--device`: Select the device, e.g., `cpu`, `mps`, or `cuda`
- `--cache`: Enables the KV cache
- `--compile`: Uses `torch.compile`
- `--reasoning`: Uses the Qwen3 reasoning variant instead of the base model. The base model generates approximately 50 tokens in response to the given prompt. The reasoning variant generates about 2000 tokens.
- `--optimize`: Uses the optimized model from `qwen3_optimized.py` instead of the standard model from `qwen3.py`.

- `--device`: 选择设备，例如`cpu`、`mps`或`cuda`
- `--cache`: 启用KV缓存
- `--compile`: 使用`torch.compile`
- `--reasoning`: 使用Qwen3推理变体，而不是基础模型。基础模型在给定提示下生成大约50个令牌。推理变体生成大约2000个令牌。
- `--optimize`: 使用`qwen3_optimized.py`中的优化模型，而不是`qwen3.py`中的标准模型。
<br>

&nbsp;
### Standard model 标准模型



| Model    | Mode              | Command                         | Hardware        | Tokens/sec    | GPU Memory (VRAM) |
| -------- | ----------------- | ------------------------------- | --------------- | ------------- | ----------------- |
| qwen3.py | Regular           | --device cpu                    | Mac Mini M4 CPU | 6             | -                 |
| qwen3.py | Regular compiled  | --device cpu --compile          | Mac Mini M4 CPU | 6             | -                 |
| qwen3.py | KV cache          | --device cpu --cache            | Mac Mini M4 CPU | 28            | -                 |
| qwen3.py | KV cache compiled | --device cpu --compile --cache  | Mac Mini M4 CPU | 68            | -                 |
|          |                   |                                 |                 |               |                   |
| qwen3.py | Regular           | --device mps                    | Mac Mini M4 GPU | 17            | -                 |
| qwen3.py | Regular compiled  | --device mps --compile          | Mac Mini M4 GPU | InductorError | -                 |
| qwen3.py | KV cache          | --device mps --cache            | Mac Mini M4 GPU | 18            | -                 |
| qwen3.py | KV cache compiled | --device mps --compile --cache  | Mac Mini M4 GPU | InductorError | -                 |
|          |                   |                                 |                 |               |                   |
| qwen3.py | Regular           | --device cuda                   | NVIDIA H100 GPU | 51            | 1.55 GB           |
| qwen3.py | Regular compiled  | --device cuda --compile         | NVIDIA H100 GPU | 164           | 1.81 GB           |
| qwen3.py | KV cache          | --device cuda --cache           | NVIDIA H100 GPU | 48            | 1.52 GB           |
| qwen3.py | KV cache compiled | --device cuda --compile --cache | NVIDIA H100 GPU | 141           | 1.81 GB           |

<br>

&nbsp;
### Optimized model 优化后模型


| Model              | Mode              | Command                                     | Hardware        | Tokens/sec | GPU Memory (VRAM) |
| ------------------ | ----------------- | ------------------------------------------- | --------------- | ---------- | ----------------- |
| qwen3_optimized.py | Regular           | --optimized --device cpu                    | Mac Mini M4 CPU | 5          | -                 |
| qwen3_optimized.py | Regular compiled  | --optimized --device cpu --compile          | Mac Mini M4 CPU | 7          | -                 |
| qwen3_optimized.py | KV cache          | --optimized --device cpu --cache            | Mac Mini M4 CPU | 49         | -                 |
| qwen3_optimized.py | KV cache compiled | --optimized --device cpu --compile --cache  | Mac Mini M4 CPU | 51         | -                 |
|                    |                   |                                             |                 |            |                   |
| qwen3_optimized.py | Regular           | --optimized --device mps                    | Mac Mini M4 GPU | 21         | -                 |
| qwen3_optimized.py | Regular compiled  | --optimized --device mps --compile          | Mac Mini M4 GPU | NameError  | -                 |
| qwen3_optimized.py | KV cache          | --optimized --device mps --cache            | Mac Mini M4 GPU | 29         | -                 |
| qwen3_optimized.py | KV cache compiled | --optimized --device mps --compile --cache  | Mac Mini M4 GPU | 38         | -                 |
|                    |                   |                                             |                 |            |                   |
| qwen3_optimized.py | Regular           | --optimized --device cuda                   | NVIDIA H100 GPU | 55         | 1.50 GB           |
| qwen3_optimized.py | Regular compiled  | --optimized --device cuda --compile         | NVIDIA H100 GPU | 173        | 1.81 GB           |
| qwen3_optimized.py | KV cache          | --optimized --device cuda --cache           | NVIDIA H100 GPU | 56         | 5.85 GB           |
| qwen3_optimized.py | KV cache compiled | --optimized --device cuda --compile --cache | NVIDIA H100 GPU | 177        | 5.85 GB           |

<br>

Comparing the 2 tables above, we can see that the optimized variant is clearly faster in terms of tokens/second in most cases. 

比较上面的两个表格，我们可以看到在大多数情况下，优化后的变体在每秒令牌数方面明显更快。

However, note that the unoptimized version is faster (68 tok/sec) than the optimized version (51 tok/sec) when using the compiled version with KV cache.

然而，请注意，当使用带有KV缓存的编译版本时，未优化版本（68 tok/sec）比优化版本（51 tok/sec）更快。

The optimized version also uses more base RAM (5.85 GB with KV Cache) than the unoptimized version (1.5 GB). This is because it pre-allocates the tensors holding the KV values for the maximum supported context length. (So, when running the unoptimized version on a prompt with 41k context length, the RAM usage would be approximately similar.)

优化版本也比未优化版本使用更多的基本RAM（带有KV缓存的5.85 GB），因为它预分配了用于最大支持上下文长度的KV值的张量。（因此，当在41k上下文长度的提示上运行未优化版本时，RAM使用量大致相似。）

**Perhaps the best recommendation is to use the unoptimized version (with `--cache` and `--compile`) when using a CPU. When using a GPU, use the optimized version (with `--cache` and `--compile`). **

**也许最好的建议是使用CPU时使用未优化版本（带有`--cache`和`--compile`）。当使用GPU时，使用优化版本（带有`--cache`和`--compile`）。**