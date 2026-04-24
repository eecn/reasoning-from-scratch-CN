# 故障排除指南

本页面汇总了在学习本书过程中遇到的常见问题和设置建议。

&nbsp;
## JupyterLab 滚动 Bug

如果你在 JupyterLab（而非 VSCode）中查看 notebook 代码，请注意 JupyterLab（在默认设置下）最近的版本存在滚动 bug。我的建议是进入 Settings -> Settings Editor，将 "Windowing mode" 更改为 "none"（如下图所示），这似乎可以解决该问题。


![Jupyter 显示异常 1](https://sebastianraschka.com/images/reasoning-from-scratch-images/bonus/setup/jupyter_glitching_1.webp)

<br>

![Jupyter 显示异常 2](https://sebastianraschka.com/images/reasoning-from-scratch-images/bonus/setup/jupyter_glitching_2.webp)


&nbsp;
## 第 2 章

&nbsp;
### 文件下载问题

如果你在下载文件时遇到任何问题，请使用[此讨论页面](https://github.com/rasbt/reasoning-from-scratch/discussions/145)。

代码会从以下 Hugging Face 位置下载文件，你也可以在浏览器中手动打开这些链接，检查你的机器或网络是否阻止了访问：

- 第 2 章模型和 tokenizer 文件：[rasbt/qwen3-from-scratch](https://huggingface.co/rasbt/qwen3-from-scratch/tree/main)
- 基座模型文件：[qwen3-0.6B-base.pth](https://huggingface.co/rasbt/qwen3-from-scratch/resolve/main/qwen3-0.6B-base.pth)
- 基座 tokenizer 文件：[tokenizer-base.json](https://huggingface.co/rasbt/qwen3-from-scratch/resolve/main/tokenizer-base.json)
- 推理模型文件：[qwen3-0.6B-reasoning.pth](https://huggingface.co/rasbt/qwen3-from-scratch/resolve/main/qwen3-0.6B-reasoning.pth)
- 推理 tokenizer 文件：[tokenizer-reasoning.json](https://huggingface.co/rasbt/qwen3-from-scratch/resolve/main/tokenizer-reasoning.json)
- 第 7 章 GRPO 检查点：[rasbt/qwen3-from-scratch-grpo-checkpoints](https://huggingface.co/rasbt/qwen3-from-scratch-grpo-checkpoints/tree/main)
- 第 8 章蒸馏检查点：[rasbt/qwen3-from-scratch-distill-checkpoints](https://huggingface.co/rasbt/qwen3-from-scratch-distill-checkpoints/tree/main)

&nbsp;
#### SSL / 代理 / 证书错误

如果模型下载失败，错误信息中提到 `SSL`、`CERTIFICATE_VERIFY_FAILED` 或 `ProxyError`，通常问题出在环境配置而非文件缺失。

这种情况整体上并不常见，但在工作单位或学校的机器上可能会发生，因为 VPN、代理、防火墙或杀毒软件可能拦截了 HTTPS 流量。在这种情况下，请尝试以下方法：

- 检查上面列出的相关 Hugging Face URL 是否能在浏览器中打开。
- 如果 tokenizer 可以下载但 `.pth` 模型文件不行，代理可能阻止了较大的文件或 `.pth` 扩展名。
- 请联系你的 IT 团队允许下载，或让代理证书被 Python 信任。
- 在某些受管理的机器上，读者报告使用 `pip install pip-system-certs` 成功解决了问题，该包使 Python 使用操作系统的证书存储。

&nbsp;
### `InductorError: CppCompileError`
如果你是 Linux 用户，在执行 `torch.compile` 时看到 `InductorError: CppCompileError: C++ compile error`，包含以下行：

```python
Python.h: No such file or directory
81 | #include <Python.h>
| ^~~~~~~~~~
compilation terminated.
```

这表示你的 Python 运行时可能缺少编译模型所需的一些 C++ 头文件（用于 CPU 使用）。

你可以例如检查文件是否存在：`ls -l /usr/include/python3.12/Python.h`。

如果不存在，你可以尝试通过以下命令安装不同的 Python 运行时：

```bash
sudo apt-get install -y python3.12-dev build-essential
```

或者你可以在调用 `torch.compile` 之前禁用 PyTorch 中的 C++ 要求：

```python
import torch
import torch._inductor.config as inductor_config

inductor_config.cpp_wrapper = False

compiled_model = torch.compile(model)
```

更多上下文请参阅 [#192](https://github.com/rasbt/reasoning-from-scratch/issues/192)。


&nbsp;
### Windows CPU：`fatal error C1083`（涉及 `algorithm` 或 `omp.h`）

如果你在 Windows 上使用 `torch.compile()` 时失败，报错如下：

```text
fatal error C1083: Cannot open include file: 'algorithm': No such file or directory
```

或

```text
fatal error C1083: Cannot open include file: 'omp.h': No such file or directory
```

问题通常出在 TorchInductor 使用的本地 Windows 编译器 / OpenMP 设置上（而非本书/仓库中的代码）。

一位读者在 [论坛](https://livebook.manning.com/forum?product=raschka2&comment=583365) 中分享了在仅有 CPU 的 Intel 系统上的以下建议：

- 升级 PyTorch 解决了缺少 `algorithm` 头文件的问题。
- 但缺少 `omp.h` 头文件的问题仍然存在。
- 使用回退后端（如 `"eager"` 或 `"aot_eager"`）使代码可以运行。

例如：

```python
compiled_model = torch.compile(model, backend="eager")

# 或

compiled_model = torch.compile(model, backend="aot_eager")
```

请注意，这是一种变通方法，而非完整修复。它可能有所帮助，但不使用完整的 TorchInductor 编译路径，因此加速效果可能不如完全正常工作的 `torch.compile()` 显著。

**但也请记住，torch.compile 对本书来说并非必需，你完全可以跳过该部分。**

无论如何，如果你正在尝试使其工作，在花费大量时间调试之前，先运行一个最小的健全性检查会很有帮助：

```python
import torch

device = "cpu"  # 或 "xpu"

def foo(x, y):
    a = torch.sin(x)
    b = torch.cos(y)
    return a + b


opt_foo = torch.compile(foo)
out = opt_foo(torch.randn(10, 10).to(device), torch.randn(10, 10).to(device))
print(out.shape)
```

如果这个小示例已经失败，问题可能出在你的 PyTorch / 编译器设置上，而非本书/仓库中的模型代码。

更多设置建议，请参阅[在 Windows 上使用 `torch.compile()`](ch02/04_torch-compile-windows/README.md) 和 PyTorch 的 [Windows CPU/XPU 指南](https://docs.pytorch.org/tutorials/unstable/inductor_windows.html)。并且如前所述，如果 `torch.compile()` 在你的系统上仍不稳定，跳过它对于本书示例来说是完全没问题的。



&nbsp;
## 第 6 章

&nbsp;
### 损坏的检查点

在 `train_rlvr_grpo`（第 6 章）中，按 `Ctrl+C` 会触发 `KeyboardInterrupt` 处理程序来保存一个 `-interrupt` 检查点。如果你在保存完成之前再次按 `Ctrl+C`，可能会中断正在写入的 `torch.save`，导致生成截断的 `.pth` 文件。请在看到 `-interrupt` 检查点消息后再退出。

损坏的模型检查点通常会在加载时引发错误或在评估时失败；另一个明显的迹象是它们比预期的约 1.5 GB 小得多。

&nbsp;
## 其他问题

对于其他问题，请随时提交新的 GitHub [Issue](https://github.com/rasbt/reasoning-from-scratch/issues)。
