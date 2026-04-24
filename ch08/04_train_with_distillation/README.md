# 第 8 章附加材料：使用蒸馏进行训练

此文件夹包含一个简单的蒸馏脚本，用于在教师生成的推理轨迹上训练 Qwen3 0.6B 模型，如第 8 章所述。

&nbsp;
## 文件

- [distill.py](distill.py)：在 JSON 格式的蒸馏数据上训练 Qwen3 0.6B（格式详见下一节）。
  - 默认使用基座模型和基座 tokenizer 进行训练
  - 如果传入 `--use_think_tokens`，则使用推理 tokenizer，并在最终答案前将推理轨迹包装为 `<think>...</think>`，类似于第 8 章中的做法
  - 每个 epoch 后保存检查点到 `checkpoints/distill/`，并将训练指标追加到 `logs/distill_metrics.csv`
  - 如果从 `--checkpoint_path`（可选）初始化而非基座模型，你可以继续已有的检查点
- [distill_batched.py](distill_batched.py)：上述脚本的批处理版本。
  - 使用支持 padding 的批处理 Qwen3 实现，使不同长度的样本可以一起训练
  - 添加了 `--batch_size` 参数以在每个优化步骤中处理多个样本
  - 将检查点保存到 `checkpoints/distill_batched/`，指标追加到 `logs/distill_batched_metrics.csv`
  - 当然，注意批处理变体使用更多的 GPU 内存（取决于 batch size）

该脚本从 [`reasoning_from_scratch`](../../reasoning_from_scratch) 包导入共享功能，以避免重复模型加载和 prompt 格式化代码。（安装详情请参阅[第 2 章设置说明](../../ch02/02_setup-tips/python-instructions.md)。）


<br>

---

**注意**：如果你不是 `uv` 用户，请在下面的示例中将 `uv run ...py` 替换为 `python ...py`。

---


&nbsp;
## 输入数据格式

输入是 [`../02_generate_distillation_data`](../02_generate_distillation_data) 生成的 JSON 输出。每行应如下所示：

```json
{
  "problem": "Compute 1/2 + 1/6.",
  "gtruth_answer": "2/3",
  "message_thinking": "I will rewrite the fractions with a common denominator.",
  "message_content": "The final answer is \\boxed{\\tfrac{2}{3}}."
}
```

训练时只使用以下字段：

- `problem`：插入到第 3 章使用的相同数学 prompt 模板中
- `message_content`：必需；用作监督目标答案
- `message_thinking`：可选；如果存在，将被前置到 `message_content` 之前

缺少字段或格式错误的行会被自动跳过，超过 `--max_seq_len` 的样本在训练/验证拆分前会被过滤掉。


&nbsp;
## 示例运行

为了快速健全性检查，你可以在上一个文件夹中生成的小样本上进行训练：

```bash
uv run distill.py \
  --data_path ../02_generate_distillation_data/sample_openrouter_outputs.json \
  --dataset_size 5 \
  --validation_size 1 \
  --epochs 2 \
  --log_every 1
```

这将：

- 加载基座 Qwen3 0.6B 权重
- 对 prompt/答案对进行 tokenize
- 保留 1 个样本用于验证
- 在每个 epoch 后将检查点保存到 `checkpoints/distill/`
- 将 CSV 指标写入 `logs/distill_metrics.csv`

如果你想使用显式推理标签和推理 tokenizer 进行训练，添加 `--use_think_tokens`：

```bash
uv run distill.py \
  --data_path ../02_generate_distillation_data/sample_openrouter_outputs.json \
  --dataset_size 5 \
  --validation_size 1 \
  --epochs 2 \
  --log_every 1 \
  --use_think_tokens
```

如果你想改用批量训练，运行：

```bash
uv run distill_batched.py \
  --data_path ../02_generate_distillation_data/sample_openrouter_outputs.json \
  --dataset_size 5 \
  --validation_size 1 \
  --epochs 2 \
  --batch_size 2 \
  --log_every 1
```


&nbsp;
## 常用选项

```bash
uv run distill.py --help
```

重要参数：

- `--data_path`：蒸馏 JSON 文件的路径
- `--dataset_size`：在拆分前截断数据集（`0` 使用所有行）
- `--validation_size`：验证样本的绝对数量
- `--epochs`：训练集的遍历次数
- `--batch_size`：`distill_batched.py` 中每个优化步骤的样本数
- `--lr`：AdamW 学习率
- `--max_seq_len`：过滤掉 prompt + 答案序列长度超过此限制的样本
- `--checkpoint_path`：从早期蒸馏检查点初始化
- `--grad_clip_norm`：可选的梯度裁剪
- `--use_think_tokens`：切换到推理 tokenizer 和 `<think>...</think>` 格式

有关实际示例，请参阅下面的"实验"部分。

&nbsp;

## 评估蒸馏检查点

训练完成后，你可以使用第 3 章的评估脚本在 MATH-500 上评估检查点。

如果你训练时没有使用 `--use_think_tokens`，将其作为 `base` 模型评估：

```bash
uv run ../../ch03/02_math500-verifier-scripts/evaluate_math500.py \
  --dataset_size 500 \
  --which_model base \
  --checkpoint_path checkpoints/distill/qwen3-0.6B-distill-step00004-epoch1.pth
```

**重要：** 如果你训练时使用了 `--use_think_tokens`，将其作为 `reasoning` 模型评估以使用推理 tokenizer：

```bash
uv run ../../ch03/02_math500-verifier-scripts/evaluate_math500.py \
  --dataset_size 500 \
  --which_model reasoning \
  --checkpoint_path checkpoints/distill/qwen3-0.6B-distill-step00004-epoch1.pth
```


&nbsp;
## 实验

第 8 章使用的蒸馏数据集可从我的 Hugging Face 仓库 [rasbt/math_distill](https://huggingface.co/datasets/rasbt/math_distill) 获取。在第 8 章中，它们通过一个辅助函数加载，该函数下载分区，例如：

````python
from reasoning_from_scratch.ch08 import load_distill_data

_ = load_distill_data(
    partition="deepseek-r1-math-train.json",
    local_path="deepseek-r1-math-train.json"
)
_ = load_distill_data(
    partition="qwen3-235b-a22b-math-train.json",
    local_path="qwen3-235b-a22b-math-train.json"
)
````



对于下面的实验，我使用了该数据集集合中的 `deepseek-r1-math-train.json` 和 `qwen3-235b-a22b-math-train.json` 文件。


&nbsp;

|      | 教师数据                                 | Epoch | MATH-500 准确率 | 最终验证损失   |
| ---- | ---------------------------------------- | ----- | --------------- | -------------- |
| 1    | 基座（第 3 章）                           | -     | 15.2%           | -              |
| 2    | 推理（第 3 章）                           | -     | 48.2%           | -              |
| 3    | DeepSeek R1 蒸馏数据                      | 1     | 30.6%           | 0.5436         |
| 4    | DeepSeek R1 蒸馏数据                      | 2     | 32.4%           | 0.5349         |
| 5    | DeepSeek R1 蒸馏数据                      | 3     | 33.6%           | 0.5343         |
| 6    | Qwen3 235B A22B 蒸馏数据                  | 1     | 45.0%           | 0.4043         |
| 7    | Qwen3 235B A22B 蒸馏数据                  | 2     | 43.8%           | 0.3963         |
| 8    | Qwen3 235B A22B 蒸馏数据                  | 3     | 44.2%           | 0.3948         |

训练在 H100 上大约需要 30 分钟，在 DGX Spark 上大约需要 3 小时，使用最多 15 GB RAM。

以下是复现表中结果的代码片段。

&nbsp;
**第 1 行**

```bash
uv run ../../ch03/02_math500-verifier-scripts/evaluate_math500.py \
--dataset_size 500 \
--which_model base
```

&nbsp;
**第 2 行**

```bash
uv run ../../ch03/02_math500-verifier-scripts/evaluate_math500.py \
--dataset_size 500 \
--which_model reasoning
```

&nbsp;
**第 3、4 和第 5 行**

```bash
uv run distill.py \
--data_path deepseek-r1-math-train.json \
--validation_size 25 \
--epochs 3 \
--lr 1e-5 \
--max_seq_len 2048 \
--use_think_tokens \
--grad_clip 1.0
```

然后，要评估各 epoch 的检查点，运行：

&nbsp;
```bash
uv run ../../ch03/02_math500-verifier-scripts/evaluate_math500.py \
--dataset_size 500 \
--which_model reasoning \
--max_new_tokens 4096 \
--checkpoint_path run-1/checkpoints/distill/qwen3-0.6B-distill-step06682-epoch1.pth
```

对于第 4 行和第 5 行，分别将检查点路径替换为 `...step13364-epoch2.pth` 和 `...step20046-epoch3.pth`。

&nbsp;
**第 6、7 和第 8 行**

```bash
uv run distill.py \
--data_path qwen3-235b-a22b-math-train.json \
--validation_size 25 \
--epochs 3 \
--lr 1e-5 \
--max_seq_len 2048 \
--use_think_tokens \
--grad_clip 1.0
```

然后，要评估各 epoch 的检查点，运行：

```bash
uv run ../../ch03/02_math500-verifier-scripts/evaluate_math500.py \
--dataset_size 500 \
--which_model reasoning \
--max_new_tokens 4096 \
--checkpoint_path run_11/checkpoints/distill/qwen3-0.6B-distill-step05746-epoch1.pth
```

对于第 7 行和第 8 行，分别将检查点路径替换为 `...step11492-epoch2.pth` 和 `...step17238-epoch3.pth`。
