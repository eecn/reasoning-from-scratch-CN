# 第 3 章：评估推理模型

&nbsp;


&nbsp;
## 附加材料

- [evaluate_math500.py](evaluate_math500.py)：在 MATH-500 数据集上评估模型的独立脚本
- [evaluate_math500_batched.py](evaluate_math500_batched.py)：与上面相同，但在生成过程中并行处理多个样本（以获得更高吞吐量）
- [evaluate_json.py](evaluate_json.py)：评估已保存的 JSON/JSONL 记录文件并报告准确率

两个评估脚本都从 [`reasoning_from_scratch`](../../reasoning_from_scratch) 包导入功能以避免代码重复。（安装详情请参阅[第 2 章设置说明](../../ch02/02_setup-tips/python-instructions.md)。）



<br>

---

**注意**：如果你不是 `uv` 用户，请在下面的示例中将 `uv run ...py` 替换为 `python ...py`。

---



&nbsp;

## `evaluate_math500.py` 使用方法

运行：

```bash
python evaluate_math500.py
```

或者，使用 `uv`：


```bash
uv run evaluate_math500.py
```

选项：

```bash
uv run evaluate_math500.py --help

options:
  -h, --help            show this help message and exit
  --device DEVICE       Device to use: "auto" (default) or any torch device string
                        (e.g., "cpu", "cuda", "cuda:0", "mps").
  --which_model {base,reasoning}
                        Model variant to load (default: "base").
  --dataset_size DATASET_SIZE
                        Number of MATH-500 examples to evaluate (default: 10).
  --max_new_tokens MAX_NEW_TOKENS
                        Max new tokens to generate (default: 2048).
  --compile             Enable torch.compile.
  --verbose             Print per-sample correctness while evaluating.
```

&nbsp;
## `evaluate_math500_batch.py` 使用方法

此版本将批处理扩展到生成本身，实现并行解码：

```bash
uv run evaluate_math500_batched.py --help
```

额外选项：

```bash
  --batch_size BATCH_SIZE
                        Number of examples to generate in parallel (default: 4).
  --disable_efficient_mode
                        Use a simpler batched inference method. Slower and more
                        memory-intensive, but easier to debug.
```


&nbsp;


**实现说明：**
默认情况下，批处理生成会在序列发出停止 token 时暂停该序列。使用 `--disable_efficient_mode` 时，所有序列会继续直到最长的那个结束。这仅影响计算效率，不影响定性结果，因为停止 token 之后的 token 会被丢弃。

&nbsp;

**提示（MPS 设备）：**
使用以下命令运行：

```bash
PYTORCH_ENABLE_MPS_FALLBACK=1 uv run evaluate_math500_batched.py
```

高效批处理推理中使用的一些 PyTorch 操作在 MPS 上尚不支持。作为替代方案，你也可以使用 `--disable_efficient_mode`。



&nbsp;

- `evaluate_math500.py --dataset_size 500`


| 设备 / 数据集大小                             | 基座模型   | 推理模型        |
| ------------------------------------------- | ---------- | --------------- |
| **Mac Mini M4 CPU**（500 样本，顺序执行）      | 43.6 分钟  | 未运行（过热）    |
| **Mac Mini M4 GPU**（500 样本，顺序执行）      | 37.5 分钟  | 未运行（过热）    |
| **DGX Spark**（500 样本，顺序执行）            | 10.0 分钟  | 182.2 分钟      |
| **H100 GPU**（500 样本，顺序执行）             | 13.3 分钟  | 185.4 分钟      |

<br>
<br>

- `evaluate_math500_batched.py --dataset_size 500 --batch_size 128`

| 设备 / 数据集大小                                              | 基座模型   | 推理模型        |
| ------------------------------------------------------------ | ---------- | --------------- |
| **Mac Mini M4 CPU**（500 样本，批处理，`--batch_size 128`）     | 167.2 分钟 | 未运行（过热）    |
| **Mac Mini M4 GPU**（500 样本，批处理，`--batch_size 128`）     | 错误*      | 错误            |
| **DGX Spark**（500 样本，批处理，`--batch_size 128`）           | 16.3 分钟  | 119.3 分钟      |
| **H100 GPU**（500 样本，批处理，`--batch_size 128`）            | 3.3 分钟   | 14.6 分钟       |



- 基座模型的准确率为 15.6%（78/500）；推理模型的准确率为 50.8%（254/500）。


&nbsp;
## `evaluate_json.py` 使用方法

如果你已经有保存的记录文件，只想（重新）计算准确率，可以使用此脚本：

```bash
uv run evaluate_json.py --json_path math500_base-mps-evaluate-script.jsonl
# Accuracy 15.6% (78/500)

可选的键名参数：

```bash
uv run evaluate_json.py \
  --json_path my_records.json \
  --gtruth_answer "gtruth_answer" \
  --generated_text "generated_text"
```

