
# LLM 作为评判者

此附加材料实现了 LLM 作为评判者（LLM-as-a-judge）的方法，其中 gpt-oss:20b（通过开源的 Ollama 库）在 MATH-500 上评估 Qwen3 0.6B 的基座和推理变体。

<img src="https://sebastianraschka.com/images/reasoning-from-scratch-images/appendix-f/Appendix_F_F06_raschka.webp" width="500px">




- Ollama 是一个用于高效运行 LLM 的开源应用
- 它是 llama.cpp（[https://github.com/ggerganov/llama.cpp](https://github.com/ggerganov/llama.cpp)）的封装，后者用纯 C/C++ 实现 LLM 以最大化效率
- 注意它是一个用于使用 LLM 生成文本（推理）的工具，而非用于训练或微调 LLM
- 在运行以下代码之前，请访问 [https://ollama.com](https://ollama.com) 安装 ollama 并按照说明操作（例如，点击 "Download" 按钮下载适用于你操作系统的 ollama 应用）
- macOS 和 Windows 用户请点击下载的 ollama 应用；如果它提示你安装命令行使用，请选择 "yes"
- Linux 用户可以使用 ollama 网站上提供的安装命令
- 我们可以通过 3 种方式在计算机上运行 ollama：



**1. `ollama serve`**

- 这会将 ollama 后端作为服务器运行，通常在 `http://localhost:11434`。在我们通过 API 调用之前，它不会加载模型。如果我们想通过 Python 使用 ollama，这就是我们需要的。

**2. `ollama run gpt-oss:20b`**

- 这是一个便捷封装。如果服务器尚未运行，它会启动服务器，然后下载模型（首次运行时），并进入一个交互式终端，我们可以在其中与模型对话。在底层，它使用相同的服务器 API。

**3. Ollama 桌面应用**

- 这会自动运行相同的后端，并在其上提供 GUI（如上图所示）。
它还会应用默认设置（系统 prompt、temperature、停止序列），这可以解释为什么回答看起来与原始 API 使用不同。



## 使用方法



选项和默认值如下所示。

<br>

---

**注意**：如果你不是 `uv` 用户，请在下面的示例中将 `uv run ...py` 替换为 `python ...py`。

---



```bash
uv run ollama-judge.py --help
usage: ollama-judge.py [-h] [--device DEVICE]
                       [--which_model {base,reasoning}]
                       [--dataset_size DATASET_SIZE]
                       [--max_new_tokens MAX_NEW_TOKENS]
                       [--url URL]
                       [--judge_model JUDGE_MODEL]

options:
  -h, --help            show this help message and
                        exit
  --device DEVICE       Device e.g., "cpu",
                        "cuda", "cuda:0", "mps".
  --which_model {base,reasoning}
                        Candidate variant to use.
                        Defaults to "base".
  --dataset_size DATASET_SIZE
                        Number of MATH-500
                        examples to evaluate.
                        Default: 10
  --max_new_tokens MAX_NEW_TOKENS
                        Max new tokens for
                        candidate generation.
                        Default: 2048
  --url URL             Ollama chat endpoint for
                        the judge. Default: "http:
                        //localhost:11434/api/chat
                        "
  --judge_model JUDGE_MODEL
                        Judge model name (Ollama).
                        Used only for scoring.
                        Default: "gpt-oss:20b"
```



**基座模型**

```bash
➜  uv run ollama-judge.py
Using Apple Silicon GPU (MPS)
Model: base
Device: mps
✓ qwen3/qwen3-0.6B-base.pth already up-to-date
✓ qwen3/tokenizer-base.json already up-to-date
Ollama running: True
[1/10] score=5
[2/10] score=1
[3/10] score=5
[4/10] score=5
[5/10] score=3
[6/10] score=5
[7/10] score=5
[8/10] score=3
[9/10] score=5
[10/10] score=1

Summary
-------
Average score: 3.800 over 10 example(s)
Counts: 1:2 2:0 3:2 4:0 5:6
```

**推理模型**

```bash
➜  uv run ollama-judge.py --which_model reasoning
Using Apple Silicon GPU (MPS)
Model: reasoning
Device: mps
✓ qwen3/qwen3-0.6B-reasoning.pth already up-to-date
✓ qwen3/tokenizer-reasoning.json already up-to-date
Ollama running: True
[1/10] score=5
[2/10] score=5
[3/10] score=5
[4/10] score=5
[5/10] score=4
[6/10] score=5
[7/10] score=5
[8/10] score=1
[9/10] score=5
[10/10] score=3

Summary
-------
Average score: 4.300 over 10 example(s)
Counts: 1:1 2:0 3:1 4:1 5:7
```

