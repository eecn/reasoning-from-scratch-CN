# 优化版 Qwen3

本书中使用的 Qwen3 从零实现版本在高效（CPU 和 GPU 上均是）和精简之间取得了平衡，同时保持了人类易读性。

作为替代方案，你可以使用可选的 `Qwen3Model` 即插即用替换版本，它在 GPU 上略微更高效。[`qwen3_optimized.py`](../../reasoning_from_scratch/qwen3_optimized.py) 中的优化版本（在附录 C 中进一步讨论）与 [`qwen3.py`](../../reasoning_from_scratch/qwen3.py) 中的基线实现有两个关键区别：

- 使用 PyTorch 内置的 `torch.nn.functional.scaled_dot_product` 实现 attention，而非自定义实现。
- 引入了改进的 `KVCache`，预分配 key/value tensor。这会增加内存使用，但避免了执行期间反复分配新存储。


要查看差异，我建议同时打开 [`qwen3.py`](../../reasoning_from_scratch/qwen3.py) 和 [`qwen3_optimized.py`](../../reasoning_from_scratch/qwen3_optimized.py) 进行并排比较，和/或查看文件差异：

<br>

![VSCode 并排比较](https://sebastianraschka.com/images/reasoning-from-scratch-images/bonus/optimized-LLM/vscode.webp)

<br>

&nbsp;
## 使用方法

优化版代码可以作为主要章节中所用代码的即插即用替换，如下所示。

**替换前：**

```python
from reasoning_from_scratch.qwen3 import Qwen3Model
from reasoning_from_scratch.ch02 import generate_text_basic_stream_cache
```


**替换后：**

```python
from reasoning_from_scratch.qwen3_optimized import Qwen3Model
from reasoning_from_scratch.ch02 import generate_text_basic_stream_cache
```

&nbsp;
## 如何运行比较

要在你的系统上评估性能，可以使用本文件夹中的 [`compare_inference.py`](compare_inference.py) 脚本：

```python
python compare_inference.py
```

或

```python
uv run compare_inference.py
```

然后，添加以下标志：

- `--device`：选择设备，例如 `cpu`、`mps` 或 `cuda`
- `--cache`：启用 KV cache
- `--compile`：使用 `torch.compile`
- `--reasoning`：使用 Qwen3 推理变体而非基座模型。基座模型对给定 prompt 大约生成 50 个 token，推理变体大约生成 2000 个 token。
- `--optimize`：使用 `qwen3_optimized.py` 中的优化模型替代 `qwen3.py` 中的标准模型。

<br>

&nbsp;
### 标准模型



| 模型     | 模式              | 命令                            | 硬件            | Tokens/秒     | GPU 显存 (VRAM)   |
| -------- | ----------------- | ------------------------------- | --------------- | ------------- | ----------------- |
| qwen3.py | 常规              | --device cpu                    | Mac Mini M4 CPU | 6             | -                 |
| qwen3.py | 常规编译          | --device cpu --compile          | Mac Mini M4 CPU | 6             | -                 |
| qwen3.py | KV cache          | --device cpu --cache            | Mac Mini M4 CPU | 28            | -                 |
| qwen3.py | KV cache 编译     | --device cpu --compile --cache  | Mac Mini M4 CPU | 68            | -                 |
|          |                   |                                 |                 |               |                   |
| qwen3.py | 常规              | --device mps                    | Mac Mini M4 GPU | 17            | -                 |
| qwen3.py | 常规编译          | --device mps --compile          | Mac Mini M4 GPU | InductorError | -                 |
| qwen3.py | KV cache          | --device mps --cache            | Mac Mini M4 GPU | 18            | -                 |
| qwen3.py | KV cache 编译     | --device mps --compile --cache  | Mac Mini M4 GPU | InductorError | -                 |
|          |                   |                                 |                 |               |                   |
| qwen3.py | 常规              | --device cuda                   | NVIDIA H100 GPU | 51            | 1.55 GB           |
| qwen3.py | 常规编译          | --device cuda --compile         | NVIDIA H100 GPU | 164           | 1.81 GB           |
| qwen3.py | KV cache          | --device cuda --cache           | NVIDIA H100 GPU | 48            | 1.52 GB           |
| qwen3.py | KV cache 编译     | --device cuda --compile --cache | NVIDIA H100 GPU | 141           | 1.81 GB           |

<br>

&nbsp;
### 优化模型


| 模型               | 模式              | 命令                                        | 硬件            | Tokens/秒  | GPU 显存 (VRAM)   |
| ------------------ | ----------------- | ------------------------------------------- | --------------- | ---------- | ----------------- |
| qwen3_optimized.py | 常规              | --optimized --device cpu                    | Mac Mini M4 CPU | 5          | -                 |
| qwen3_optimized.py | 常规编译          | --optimized --device cpu --compile          | Mac Mini M4 CPU | 7          | -                 |
| qwen3_optimized.py | KV cache          | --optimized --device cpu --cache            | Mac Mini M4 CPU | 49         | -                 |
| qwen3_optimized.py | KV cache 编译     | --optimized --device cpu --compile --cache  | Mac Mini M4 CPU | 51         | -                 |
|                    |                   |                                             |                 |            |                   |
| qwen3_optimized.py | 常规              | --optimized --device mps                    | Mac Mini M4 GPU | 21         | -                 |
| qwen3_optimized.py | 常规编译          | --optimized --device mps --compile          | Mac Mini M4 GPU | NameError  | -                 |
| qwen3_optimized.py | KV cache          | --optimized --device mps --cache            | Mac Mini M4 GPU | 29         | -                 |
| qwen3_optimized.py | KV cache 编译     | --optimized --device mps --compile --cache  | Mac Mini M4 GPU | 38         | -                 |
|                    |                   |                                             |                 |            |                   |
| qwen3_optimized.py | 常规              | --optimized --device cuda                   | NVIDIA H100 GPU | 55         | 1.50 GB           |
| qwen3_optimized.py | 常规编译          | --optimized --device cuda --compile         | NVIDIA H100 GPU | 173        | 1.81 GB           |
| qwen3_optimized.py | KV cache          | --optimized --device cuda --cache           | NVIDIA H100 GPU | 56         | 5.85 GB           |
| qwen3_optimized.py | KV cache 编译     | --optimized --device cuda --compile --cache | NVIDIA H100 GPU | 177        | 5.85 GB           |

<br>

对比以上两个表格，可以看出优化版本在大多数情况下的 tokens/秒明显更快。

但是请注意，在使用编译版本配合 KV cache 时，未优化版本更快（68 tok/秒）而优化版本为 51 tok/秒。

优化版本的基础 RAM 使用也更高（使用 KV Cache 时为 5.85 GB），而未优化版本为 1.5 GB。这是因为它会为最大支持的上下文长度预分配保存 KV 值的 tensor。（因此，当在 41k 上下文长度的 prompt 上运行未优化版本时，RAM 使用量会大致相似。）

**最佳建议可能是：使用 CPU 时选择未优化版本（配合 `--cache` 和 `--compile`）。使用 GPU 时选择优化版本（配合 `--cache` 和 `--compile`）。**
