# 第 8 章附加材料：生成蒸馏数据

此文件夹包含为数学问题生成教师模型输出的脚本，可用作训练较小推理模型的蒸馏数据，如第 8 章所述。

&nbsp;
**目录：**

- [文件](#文件)
- [输入数据格式](#输入数据格式)
- [输出格式](#输出格式)
- [1. 使用 Ollama 进行本地生成](#1-使用-ollama-进行本地生成)
  - [1.1 Ollama 设置](#11-ollama-设置)
  - [1.2 使用 Ollama 进行本地数据生成](#12-使用-ollama-进行本地数据生成)
  - [1.3 Ollama 故障排除](#13-ollama-故障排除)
    - [1.3.1 Ollama 未运行](#131-ollama-未运行)
    - [1.3.2 Ollama 模型未下载](#132-ollama-模型未下载)
- [2. 使用 OpenRouter 进行托管生成](#2-使用-openrouter-进行托管生成)
  - [2.1 OpenRouter 设置](#21-openrouter-设置)
  - [2.2 使用 OpenRouter 生成数据](#22-使用-openrouter-生成数据)
- [蒸馏数据集](#蒸馏数据集)
- [数据集统计](#数据集统计)
- [教师模型准确率](#教师模型准确率)
- [生成 MATH-500 蒸馏数据集](#生成-math-500-蒸馏数据集)
- [生成 12,000 个 MATH 样本的蒸馏数据集](#生成-12000-个-math-样本的蒸馏数据集)


&nbsp;
## 文件

- [average_field_lengths_json.py](average_field_lengths_json.py)：用于打印生成数据集基本统计信息的工具脚本。
- [generate_with_ollama.py](generate_with_ollama.py)：使用 Ollama 生成用于蒸馏的模型回答。如果你想从可以在本地运行的较小模型（例如 Qwen3 4B、gpt-oss 20B、DeepSeek R1 32B 等）进行蒸馏，推荐使用此脚本。
- [generate_with_openrouter.py](generate_with_openrouter.py)：通过 OpenRouter API 使用模型生成用于蒸馏的模型回答。当使用 DeepSeek R1（671B）或 Kimi K2.5（1T）等太大而无法在本地运行的大型模型时，推荐使用此脚本。
- [math_train_sample.json](math_train_sample.json)：用于快速健全性检查的小型样本数据集。

&nbsp;
## 输入数据格式

两个脚本都通过 `--math_json` 接收一个 JSON 文件。每个对象至少应包含：

- `problem`（字符串）：数学问题。
- `answer`（字符串）：标准答案。

额外的键如 `level`、`type` 和 `unique_id` 会被忽略。你可以查看 [math_train_sample.json](math_train_sample.json) 文件了解示例结构，它基于我们在第 6、7 和 8 章中使用的 [math_full_minus_math500.json](https://github.com/rasbt/math_full_minus_math500/blob/main/math_full_minus_math500.json)。

要将其应用于全部 12,000 个样本，只需下载 [math_full_minus_math500.json](https://github.com/rasbt/math_full_minus_math500/blob/main/math_full_minus_math500.json) 并通过 `--math_json math_full_minus_math500.json` 传入脚本。请注意这将花费很长时间，因此我建议将文件截断为几百或一千个样本。


&nbsp;
## 输出格式

两个脚本都输出一个 JSON 数组，每行格式如下：

```
{
  "problem": "...",             # 原始的 "problem"
  "gtruth_answer": "...",       # 原始的 "answer"
  "message_thinking": "...",    # 模型的思考过程
  "message_content": "..."      # 模型的最终回答
}
```

说明：

- 输入 JSON 文件中的原始 `"answer"` 被重命名为 `"gtruth_answer"` 以避免歧义（因为 "answer" 是一个通用术语，也可能指模型的回答）。
- 文件在每个样本之后增量写入，因此可以使用中间文件或中断运行。
- 脚本有 `--resume` 选项来继续被中断的运行。

&nbsp;
## 1. 使用 Ollama 进行本地生成


- Ollama 是一个用于高效运行 LLM 的开源应用。
- 它是 llama.cpp（[https://github.com/ggerganov/llama.cpp](https://github.com/ggerganov/llama.cpp)）的封装，后者用纯 C/C++ 实现 LLM 以最大化效率。
- 注意它是一个用于使用 LLM 生成文本（推理）的工具，而非用于训练或微调 LLM。

&nbsp;
### 1.1 Ollama 设置


- 在运行以下代码之前，请访问 [https://ollama.com](https://ollama.com) 安装 ollama 并按照说明操作（例如，点击 "Download" 按钮下载适用于你操作系统的 ollama 应用）。
- macOS 和 Windows 用户请点击下载的 ollama 应用；如果它提示你安装命令行使用，请选择 "yes"。
- Linux 用户可以使用 ollama 网站上提供的安装命令。
- 我们可以通过 3 种方式在计算机上运行 ollama：


&nbsp;
**1. `ollama serve`**

- 这会将 ollama 后端作为服务器运行，通常在 `http://localhost:11434`。在我们通过 API 调用之前，它不会加载模型。如果我们想通过 Python 使用 ollama，这就是我们需要的。

&nbsp;
**2. `ollama run deepseek-r1:8b`**

- 这是一个便捷封装。如果服务器尚未运行，它会启动服务器，然后下载模型（首次运行时），并进入一个交互式终端，我们可以在其中与模型对话。在底层，它使用相同的服务器 API。
- `deepseek-r1:8b` 模型在 `--max_new_tokens 8192` token 设置下大约需要 30 GB RAM。
  - 如果你有更多 RAM，我建议尝试更大的模型以获得更高质量的回答，例如 `deepseek-r1:32b`（大约需要 60 GB）
  - 如果你的 RAM 较少，尝试选择更小的模型；你可以在[这里](https://ollama.com/library/deepseek-r1)找到较小的 R1 模型列表。此外，你也可以使用 [Ollama 网站](https://ollama.com/) 上的 "Search model" 字段选择你可能感兴趣的其他模型，而不使用 DeepSeek 模型。
  - 或者，你也可以将 `--max_new_tokens 8192` 减少到 `--max_new_tokens 2048` 以降低 RAM 使用，但这可能会过早截断某些回答。

&nbsp;
**3. Ollama 桌面应用**

- 这会自动运行相同的后端，并在其上提供 GUI（如上图所示）。
  它还会应用默认设置（系统 prompt、temperature、停止序列），这可以解释为什么回答看起来与原始 API 使用不同。

&nbsp;
### 1.2 使用 Ollama 进行本地数据生成

```bash
uv run generate_with_ollama.py \
  --math_json math_train_sample.json \
  --dataset_size 5 \
  --model deepseek-r1:8b \
  --max_new_tokens 8192 \
  --out_file sample_ollama_outputs.json
```

如果你不是 `uv` 用户，请将 `uv run` 替换为 `python`。

预期输出如下：

```
Loading model: deepseek-r1:8b
Using CUDA:0
Model ready
5/5 | MATH-500: 5/5 | ETA: 00s        
Total time: 3.2 min

Wrote 5 rows to: /home/rasbt/reasoning-from-scratch-codedev/ch08/sample_ollama_outputs.json
```

生成的 [sample_ollama_outputs.json](sample_ollama_outputs.json) 文件中的条目如下：

```json
  {
    "problem": "A rectangular band formation...",
    "gtruth_answer": "98",
    "message_thinking": "I need to find the largest number of...",
    "message_content": "The function is continuous..."
  },
```

`"message_thinking"` 字段包含思维链解释，`"message_content"` 包含最终答案。例如，它们可以这样组合：

```python
complete_answer = f"<think>{data['message_thinking']}</think>\n\n{data['message_content']}"
```

即：

```
"<think>I need to find the largest number of...</think>

The function is continuous..."
```

&nbsp;
### 1.3 Ollama 故障排除

以下是运行 Ollama 数据生成脚本时的一些常见问题。

&nbsp;
#### 1.3.1 Ollama 未运行

如果你看到如下错误：

```
Loading model: deepseek-r1:32b
Using CUDA:0
Traceback (most recent call last):
  File "/home/rasbt/reasoning-from-scratch-codedev/ch08/generate_with_ollama.py", line 379, in <module>
    query_ollama_chat(
  File "/home/rasbt//reasoning-from-scratch-codedev/ch08/generate_with_ollama.py", line 235, in query_ollama_chat
    raise RuntimeError(
RuntimeError: Failed to query Ollama after 3 attempt(s). Last error: <urlopen error [Errno 111] Connection refused>
```

请确保 `ollama serve` 正在运行（在另一个终端标签页中）。

&nbsp;
#### 1.3.2 Ollama 模型未下载

如果你看到以下错误：

```
Loading model: deepseek-r1:8b
Using CUDA:0
Traceback (most recent call last):
  File "/home/rasbt/reasoning-from-scratch-codedev/ch08/generate_with_ollama.py", line 379, in <module>
    query_ollama_chat(
  File "/home/rasbt/reasoning-from-scratch-codedev/ch08/generate_with_ollama.py", line 235, in query_ollama_chat
    raise RuntimeError(
RuntimeError: Failed to query Ollama after 3 attempt(s). Last error: HTTP 404 from Ollama at http://localhost:11434/api/chat: {"error":"model 'deepseek-r1:8b' not found"}
```

这意味着模型尚未下载。在这种情况下，在单独的终端中运行 `ollama run deepseek-r1:8b`，这将下载模型并启动聊天。你可以在聊天中试用模型，然后通过 `\bye` 退出。


&nbsp;
## 2. 使用 OpenRouter 进行托管生成

如果你想在本地运行模型，Ollama 非常方便。但是，有些大型模型（如 671B 参数的 DeepSeek R1 模型）太大而无法在我们的硬件上本地运行。对于这些情况，我推荐 [OpenRouter](https://openrouter.ai)，它让我们通过类似 ChatGPT 的 API 使用各种开放权重和专有 LLM，托管在云端。

截至撰写本文时，[DeepSeek R1](https://openrouter.ai/deepseek/deepseek-r1) 的价格为每 100 万输入 token $0.70，每 100 万输出 token $2.50。注意 OpenRouter 上有许多更便宜（和更快）的模型；即使是更新的 [DeepSeek V3.2](https://openrouter.ai/deepseek/deepseek-v3.2) 模型每 100 万输出 token 也只需 $0.40。

话虽如此，让我们做一个简单的成本计算。假设平均输入 prompt 长度为 11 个 token，平均响应长度为 1524 个 token，生成 1000 个 MATH 问题的答案大约需要 $3.82。

详细分解如下：

- 总输入 token：11 × 1000 = 11,000
- 总输出 token：1524 × 1000 = 1,524,000
- 输入成本：`(11,000 / 1,000,000) × $0.70 = $0.0077`
- 输出成本：`(1,524,000 / 1,000,000) × $2.50 = $3.81`
- 总成本：`$3.81 + $0.0077 ≈ $3.82`

&nbsp;
### 2.1 OpenRouter 设置

设置非常简单。你只需在 [OpenRouter](https://openrouter.ai/) 上创建一个账户，在 [https://openrouter.ai/settings/keys](https://openrouter.ai/settings/keys) 下生成一个 API 密钥，并将 API 密钥保存在安全位置（例如密码管理器）。


&nbsp;
### 2.2 使用 OpenRouter 生成数据

OpenRouter 脚本的工作方式与 Ollama 脚本类似，只是我们将 API 密钥作为环境变量前置：

```bash
OPENROUTER_API_KEY="YOUR_API_KEY" uv run generate_with_openrouter.py \
  --math_json math_train_sample.json \
  --dataset_size 5 \
  --model deepseek/deepseek-r1 \
  --num_processes 1 \
  --out_file sample_openrouter_outputs.json
```

如果你不是 `uv` 用户，请将 `uv run` 替换为 `python`。

输出如下：

```
Loading model: deepseek/deepseek-r1
Using OpenRouter API: https://openrouter.ai/api/v1/chat/completions
Model ready
5/5 | MATH-500: 5/5 | ETA: 00s        
Total time: 2.2 min

Wrote 5 rows to: /Users/sebastian/Developer/reasoning-from-scratch/ch08/02_generate_distillation_data/sample_openrouter_outputs.json
```

[sample_openrouter_outputs.json](sample_openrouter_outputs.json) 输出文件的结构与 Ollama 脚本生成的相同。

**提示：** 如果你要生成大量数据，按顺序运行此蒸馏过程可能非常慢（例如，使用 DeepSeek R1 生成 12,000 个答案约需 100 小时）。在这种情况下，我建议通过 `--num_processes` 运行多个并行数据生成线程。例如，对 DeepSeek R1 模型使用 `--num_processes 50` 可将运行时间从 100 小时缩短至约 2 小时。


&nbsp;
## 蒸馏数据集

通过上述 OpenRouter 方法生成的数据集集合可在此处找到：[https://huggingface.co/datasets/rasbt/math_distill](https://huggingface.co/datasets/rasbt/math_distill)。

&nbsp;
## 数据集统计

要检查数据集统计信息，使用 [average_field_lengths_json.py] 脚本：

```bash
uv run average_field_lengths_json.py \
--json_path sample_openrouter_outputs.json
```

```
tokenizer-reasoning.json: 100% (10 MiB / 10 MiB)
Records: 5
Tokenizer: reasoning
Field             AvgTokens  MinTokens  MaxToken  Count
gtruth_answer          9.40          9        10      5
message_content      196.00        166       259      5
message_thinking     933.20        449      1676      5
problem               77.80         30       121      5
```

&nbsp;
## 教师模型准确率

要计算生成数据集的模型（即教师模型）的准确率，使用 [../../ch03/02_math500-verifier-scripts/evaluate_json.py](../../ch03/02_math500-verifier-scripts/evaluate_json.py) 脚本：

```bash
uv run ../../ch03/02_math500-verifier-scripts/evaluate_json.py \
--json_path sample_openrouter_outputs.json \
--gtruth_answer gtruth_answer \
--generated_text message_content
```

```
Accuracy: 100.0% (5/5)
```

&nbsp;
## 生成 MATH-500 蒸馏数据集

要为 500 个样本的 MATH-500 集生成教师回答，可以省略 `--math_json`；两个脚本都会自动加载 `math500_test.json`（首次使用时保存本地副本）。

**Ollama**

```bash
uv run generate_with_ollama.py \
  --dataset_size 500 \
  --model deepseek-r1:8b \
  --max_new_tokens 8192 \
  --out_file math500_ollama_distill.json
```

**OpenRouter**

```bash
OPENROUTER_API_KEY="YOUR_API_KEY" uv run generate_with_openrouter.py \
  --dataset_size 500 \
  --model deepseek/deepseek-r1 \
  --num_processes 1 \
  --out_file math500_openrouter_distill.json
```

&nbsp;
## 生成 12,000 个 MATH 样本的蒸馏数据集

这使用与第 6、7 和 8 章相同的不重叠的 12,000 个样本训练集。如果你还没有该文件，请先下载：

```bash
curl -fL -o math_full_minus_math500.json \
https://raw.githubusercontent.com/rasbt/math_full_minus_math500/refs/heads/main/math_full_minus_math500.json
```

**Ollama**

```bash
uv run generate_with_ollama.py \
  --math_json math_full_minus_math500.json \
  --dataset_size 12000 \
  --model deepseek-r1:8b \
  --max_new_tokens 8192 \
  --resume \
  --out_file math12000_ollama_distill.json
```

**OpenRouter**

```bash
OPENROUTER_API_KEY="YOUR_API_KEY" uv run generate_with_openrouter.py \
  --math_json math_full_minus_math500.json \
  --dataset_size 12000 \
  --model deepseek/deepseek-r1 \
  --num_processes 50 \
  --resume \
  --out_file math12000_openrouter_distill.json
```

对于大规模 OpenRouter 运行，根据你的账户限制和所需吞吐量减少或增加 `--num_processes`。
