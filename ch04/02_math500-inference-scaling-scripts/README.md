# 第 4 章：通过推理时扩展改进推理能力


&nbsp;
## 附加材料

- [cot_prompting_math500.py](cot_prompting_math500.py)：在 MATH-500 数据集上使用思维链（chain-of-thought）prompting 评估模型的独立脚本
- [self_consistency_math500.py](self_consistency_math500.py)：在 MATH-500 数据集上使用 self-consistency 采样评估模型的独立脚本
- [run_all_experiments_math500.sh](run_all_experiments_math500.sh)：一个便捷的 bash 脚本，用于运行下面 README 中列出的所有实验（第 4 行到第 12 行）

两个评估脚本都从 [`reasoning_from_scratch`](../../reasoning_from_scratch) 包导入功能以避免代码重复。（安装详情请参阅[第 2 章设置说明](../../ch02/02_setup-tips/python-instructions.md)。）



<br>

---

**注意**：如果你不是 `uv` 用户，请在下面的示例中将 `uv run ...py` 替换为 `python ...py`。

---



&nbsp;

## 思维链 prompting（Chain-of-thought prompting）

[`cot_prompting_math500.py`](self_consistency_math500.py) 脚本实现了第 4 章中的思维链 prompting 方法。

&nbsp;

<img src="https://sebastianraschka.com/images/reasoning-from-scratch-images/ch04/CH04_F04_raschka.webp" width=600>

&nbsp;

下表将此方法（第 3 行）与第 3 章的基线进行了比较：

|    | 方法                                         | 模型      | 准确率   | 时间       |
|----|----------------------------------------------|-----------|----------|------------|
| 1  | 基线（第 3 章），贪心解码                       | 基座      | 15.2%    | 10.1 分钟  |
| 2  | 基线（第 3 章），贪心解码                       | 推理      | 48.2%    | 182.1 分钟 |
| 3  | 思维链 prompting（"CoT"）                     | 基座      | 40.6%    | 84.5 分钟  |

表中显示的准确率和运行时间是在 MATH-500 测试集的全部 500 个样本上使用 "cuda" GPU（DGX Spark）计算的。

要运行第一行的实验，使用：

```bash
python cot_prompting_math500.py \
--which_model "base" \
--dataset_size 500
```

或者，使用 `uv`：


```bash
uv run cot_prompting_math500.py \
--which_model "base" \
--dataset_size 500
```

更多选项请使用 `--help` 标志。



&nbsp;
## Self-consistency 采样

[`self_consistency_math500.py`](self_consistency_math500.py) 脚本实现了第 4 章中的采样方法。

（可选地，还有一个 [`self_consistency_math500_batched.py`](self_consistency_math500_batched.py) 变体，它将所有 `--num_samples` 作为一个 batch 执行以加快处理速度。注意，这需要更多的计算内存。）

&nbsp;

<img src="https://sebastianraschka.com/images/reasoning-from-scratch-images/ch04/CH04_F17_raschka.webp" width=600>

&nbsp;

下表将此方法（第 4-12 行）与第 3 章的基线（第 1-2 行）进行了比较：

|      | 方法                                      | 模型      | 准确率   | 时间       |
| ---- | ----------------------------------------- | --------- | -------- | --------- |
| 1    | 基线（第 3 章），贪心解码                    | 基座      | 15.2%    | 10.1 分钟  |
| 2    | 基线（第 3 章），贪心解码                    | 推理      | 48.2%    | 182.1 分钟 |
| 3    | 思维链 prompting（"CoT"）                  | 基座      | 40.6%    | 84.5 分钟  |
| 4    | Temperature 和 top-p（"Top-p"）            | 基座      | 17.8%    | 30.7 分钟  |
| 5    | "Top-p" + Self-consistency (n=3)          | 基座      | 29.6%    | 97.6 分钟  |
| 6    | "Top-p" + Self-consistency (n=5)          | 基座      | 27.8%    | 116.8 分钟 |
| 7    | "Top-p" + Self-consistency (n=10)         | 基座      | 31.6%    | 300.4 分钟 |
| 8    | "Top-p" + "CoT"                           | 基座      | 33.4%    | 129.2 分钟 |
| 9    | Self-consistency (n=3) + "Top-p" + "CoT"  | 基座      | 42.2%    | 211.6 分钟 |
| 10   | Self-consistency (n=5) + "Top-p" + "CoT"  | 基座      | 48.0%    | 452.9 分钟 |
| 11   | Self-consistency (n=10) + "Top-p" + "CoT" | 基座      | 52.0%    | 862.6 分钟 |
| 12   | Self-consistency (n=3) + "Top-p" + "CoT"  | 推理      | 55.2%    | 544.4 分钟 |

表中显示的准确率和运行时间是在 MATH-500 测试集的全部 500 个样本上使用 "cuda" GPU（DGX Spark）计算的。

以下代码说明了如何运行第 4-12 行的 self-consistency 实验（如果你不使用 `uv`，请将 `uv run` 替换为 `python`）。

**第 4 行：**

```bash
uv run self_consistency_math500.py \
    --which_model "base" \
    --temperature 0.9 \
    --top_p 0.9 \
    --num_samples 1 \
    --dataset_size 500
```

**第 5 行：**

```bash
uv run self_consistency_math500.py \
    --which_model "base" \
    --temperature 0.9 \
    --top_p 0.9 \
    --num_samples 3 \
    --dataset_size 500
```

**第 6 行：**

```bash
uv run self_consistency_math500.py \
    --which_model "base" \
    --temperature 0.9 \
    --top_p 0.9 \
    --num_samples 5 \
    --dataset_size 500
```

**第 7 行：**

```bash
uv run self_consistency_math500.py \
    --which_model "base" \
    --temperature 0.9 \
    --top_p 0.9 \
    --num_samples 10 \
    --dataset_size 500
```

**第 8 行：**

```bash
uv run self_consistency_math500.py \
    --which_model "base" \
    --temperature 0.9 \
    --top_p 0.9 \
    --num_samples 1 \
    --dataset_size 500 \
    --prompt_suffix "\n\nExplain step by step."
```

**第 9 行：**

```bash
uv run self_consistency_math500.py \
    --which_model "base" \
    --temperature 0.9 \
    --top_p 0.9 \
    --num_samples 3 \
    --dataset_size 500 \
    --prompt_suffix "\n\nExplain step by step."
```

**第 10 行：**

```bash
uv run self_consistency_math500.py \
    --which_model "base" \
    --temperature 0.9 \
    --top_p 0.9 \
    --num_samples 5 \
    --dataset_size 500 \
    --prompt_suffix "\n\nExplain step by step."
```

**第 11 行：**

```bash
uv run self_consistency_math500.py \
    --which_model "base" \
    --temperature 0.9 \
    --top_p 0.9 \
    --num_samples 10 \
    --dataset_size 500 \
    --prompt_suffix "\n\nExplain step by step."
```

**第 12 行：**

```bash
uv run self_consistency_math500.py \
    --which_model "reasoning" \
    --temperature 0.9 \
    --top_p 0.9 \
    --num_samples 3 \
    --dataset_size 500 \
    --prompt_suffix "\n\nExplain step by step."
```


更多选项请使用 `--help` 标志。

