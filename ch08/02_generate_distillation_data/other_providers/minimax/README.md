# MiniMax 蒸馏数据提供商

此文件夹包含用于第 8 章蒸馏数据生成的 MiniMax 特定托管生成脚本。

请从 `ch08/02_generate_distillation_data/` 目录运行以下命令，以确保 `math_train_sample.json` 等相对路径能正常工作。

输入和输出 JSON 格式与[主 README](../../README.md#输入数据格式) 中记录的格式相同。

&nbsp;
## 文件

- [generate_with_minimax.py](generate_with_minimax.py)：使用 MiniMax 的云 API 生成用于蒸馏的模型回答。MiniMax 通过 OpenAI 兼容的 API 提供 MiniMax-M2.7（100 万上下文窗口）等模型。

&nbsp;
## MiniMax 设置

1. 在 [MiniMax Platform](https://platform.minimaxi.com/) 创建账户
2. 在你的账户设置中生成 API 密钥
3. 将 API 密钥保存在安全位置（例如密码管理器）

可用模型：
- `MiniMax-M2.7` — 最新模型，100 万上下文窗口（默认）
- `MiniMax-M2.7-highspeed` — 针对吞吐量优化的更快变体
- `MiniMax-M2.5` — 上一代，204K 上下文
- `MiniMax-M2.5-highspeed` — M2.5 的更快变体

&nbsp;
## 使用 MiniMax 生成数据

MiniMax 脚本的工作方式与 OpenRouter 脚本类似：

```bash
MINIMAX_API_KEY="YOUR_API_KEY" uv run other_providers/minimax/generate_with_minimax.py \
  --math_json math_train_sample.json \
  --dataset_size 5 \
  --model MiniMax-M2.7 \
  --num_processes 1 \
  --out_file sample_minimax_outputs.json
```

如果你不是 `uv` 用户，请将 `uv run` 替换为 `python`。

输出文件的结构与 Ollama 和 OpenRouter 脚本生成的相同。

**注意：** MiniMax 要求 temperature 参数在 (0.0, 1.0] 范围内。脚本会自动裁剪超出此范围的值。

&nbsp;
## 生成 MATH-500 蒸馏数据集

要为 500 个样本的 MATH-500 集生成教师回答，可以省略 `--math_json`；MiniMax 脚本会自动加载 `math500_test.json`（首次使用时保存本地副本）。

```bash
MINIMAX_API_KEY="YOUR_API_KEY" uv run other_providers/minimax/generate_with_minimax.py \
  --dataset_size 500 \
  --model MiniMax-M2.7 \
  --num_processes 1 \
  --out_file math500_minimax_distill.json
```

&nbsp;
## 生成 12,000 个 MATH 样本的蒸馏数据集

这使用与第 6、7 和 8 章相同的不重叠的 12,000 个样本训练集。如果你还没有该文件，请先下载：

```bash
curl -fL -o math_full_minus_math500.json \
https://raw.githubusercontent.com/rasbt/math_full_minus_math500/refs/heads/main/math_full_minus_math500.json
```

```bash
MINIMAX_API_KEY="YOUR_API_KEY" uv run other_providers/minimax/generate_with_minimax.py \
  --math_json math_full_minus_math500.json \
  --dataset_size 12000 \
  --model MiniMax-M2.7 \
  --num_processes 50 \
  --resume \
  --out_file math12000_minimax_distill.json
```

对于大规模运行，根据你的账户限制和所需吞吐量减少或增加 `--num_processes`。
