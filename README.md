# 从零构建推理模型（Build A Reasoning Model From Scratch）

本仓库包含开发 LLM 推理模型（reasoning model）的代码，是 [*Build a Reasoning Model (From Scratch)*](https://mng.bz/lZ5B) 一书的官方代码仓库。


<br>
<br>

<a href="https://mng.bz/lZ5B"><img src="https://sebastianraschka.com/images/reasoning-from-scratch-images/cover.webp?123" width="250px"></a>

（彩色印刷。）

<br>

在 [*Build a Reasoning Model (From Scratch)*](https://mng.bz/lZ5B) 一书中，你将学习并理解推理大语言模型（reasoning LLM）的工作原理。

推理（reasoning）是近年来改进 LLM 最激动人心且最重要的进展之一，但如果你只是听到"推理"这个术语并在理论层面阅读，它也非常容易被误解。正因如此，本书采用动手实践的方式。我们将从一个预训练的基座 LLM 出发，然后一步步在代码中亲手添加推理能力，让你能够准确地看到它是如何工作的。

本书中描述的方法将引导你完成开发自己的小型但功能完备的推理模型的全过程，用于教育目的。它反映了创建大规模推理模型（如 DeepSeek R1、GPT-5 Thinking 等）所使用的方法。此外，本书还包含加载现有预训练模型权重的代码。

- 官方[源代码仓库](https://github.com/rasbt/reasoning-from-scratch)链接
- [Manning 出版社网站](https://mng.bz/lZ5B)上的书籍链接
- Amazon.com 上的书籍页面链接（待定）
- ISBN 9781633434677



<br>
<br>

要下载本仓库的副本，请点击 [Download ZIP](https://github.com/rasbt/reasoning-from-scratch/archive/refs/heads/main.zip) 按钮，或在终端中执行以下命令：

```bash
git clone --depth 1 https://github.com/rasbt/reasoning-from-scratch.git
```

<br>


> **提示：**
> 第 2 章提供了关于安装 Python、管理 Python 包和设置编码环境的额外建议。

<br>
<br>

## 目录（持续更新中）

[![Code tests Linux](https://github.com/rasbt/reasoning-from-scratch/actions/workflows/tests-linux.yml/badge.svg)](https://github.com/rasbt/reasoning-from-scratch/actions/workflows/tests-linux.yml)
[![Code tests macOS](https://github.com/rasbt/reasoning-from-scratch/actions/workflows/tests-macos.yml/badge.svg)](https://github.com/rasbt/reasoning-from-scratch/actions/workflows/tests-macos.yml)
[![Code tests Windows](https://github.com/rasbt/reasoning-from-scratch/actions/workflows/tests-windows.yml/badge.svg)](https://github.com/rasbt/reasoning-from-scratch/actions/workflows/tests-windows.yml)

- [故障排除指南](./troubleshooting.md)

| 章节标题                                                     | 主要代码                                                      |
| ----------------------------------------------------------- | ------------------------------------------------------------ |
| 第 1 章：理解推理模型                                         | 无代码                                                        |
| 第 2 章：使用预训练 LLM 生成文本                               | - [ch02_main.ipynb](ch02/01_main-chapter-code/ch02_main.ipynb)<br/>- [ch02_exercise-solutions.ipynb](ch02/01_main-chapter-code/ch02_exercise-solutions.ipynb) |
| 第 3 章：评估推理模型                                         | - [ch03_main.ipynb](ch03/01_main-chapter-code/ch03_main.ipynb)<br/>- [ch03_exercise-solutions.ipynb](ch03/01_main-chapter-code/ch03_exercise-solutions.ipynb) |
| 第 4 章：通过推理时扩展改进推理能力                             | - [ch04_main.ipynb](ch04/01_main-chapter-code/ch04_main.ipynb)<br/>- [ch04_exercise-solutions.ipynb](ch04/01_main-chapter-code/ch04_exercise-solutions.ipynb) |
| 第 5 章：通过自我精炼进行推理时扩展                             | - [ch05_main.ipynb](ch05/01_main-chapter-code/ch05_main.ipynb)<br/>- [ch05_exercise-solutions.ipynb](ch05/01_main-chapter-code/ch05_exercise-solutions.ipynb) |
| 第 6 章：使用强化学习训练推理模型                               | - [ch06_main.ipynb](ch06/01_main-chapter-code/ch06_main.ipynb)<br/>- [ch06_exercise-solutions.ipynb](ch06/01_main-chapter-code/ch06_exercise-solutions.ipynb) |
| 第 7 章：改进 GRPO 强化学习方法                                | - [ch07_main.ipynb](ch07/01_main-chapter-code/ch07_main.ipynb)<br/>- [ch07_exercise-solutions.ipynb](ch07/01_main-chapter-code/ch07_exercise-solutions.ipynb) |
| 第 8 章：蒸馏推理模型以实现高效推理                             | - [ch08_main.ipynb](ch08/01_main-chapter-code/ch08_main.ipynb)<br/>- [ch08_exercise-solutions.ipynb](ch08/01_main-chapter-code/ch08_exercise-solutions.ipynb) |
| 附录 A：参考文献与延伸阅读                                     | 无代码                                                        |
| 附录 B：练习题解答                                             | 代码和解答位于各章子文件夹中                                    |
| 附录 C：Qwen3 LLM 源代码                                      | - [chC_main.ipynb](chC/01_main-chapter-code/chC_main.ipynb)  |
| 附录 D：使用更大的 LLM                                        | - [chD_main.ipynb](chD/chD_main.ipynb)                       |
| 附录 E：批处理与面向吞吐量的执行                                | - [chE_main.ipynb](chE/chE_main.ipynb)                       |
| 附录 F：LLM 评估的常见方法                                     | - [chF_main.ipynb](chF/01_main-chapter-code/chF_main.ipynb)  |
| 附录 G：构建聊天界面                                           | - [chG](chG)                                                 |

<br>
&nbsp;

下面的思维导图总结了本书涵盖的主要技术。

<img src="https://sebastianraschka.com/images/reasoning-from-scratch-images/mental-model.webp" width="650px">



<br>



&nbsp;
## 配套书籍

请注意，*Build A Reasoning Model (From Scratch)* 是一本独立的书，专注于改进 LLM 推理的方法。

在本书中，我们使用一个预训练的开源基座 LLM（Qwen3），并在其基础上从零开始编写推理方法的代码。包括推理时扩展（inference-time scaling）、强化学习（reinforcement learning）和蒸馏（distillation）。

然而，如果你对理解传统基座 LLM 的实现方式感兴趣，可以参考我之前的书 [*Build a Large Language Model (From Scratch)*](https://amzn.to/4fqvn0D)。

<a href="https://amzn.to/4fqvn0D"><img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/cover.jpg?123" width="120px"></a>

- [Amazon 链接](https://amzn.to/4fqvn0D)
- [Manning 链接](http://mng.bz/orYv)
- [GitHub 仓库](https://github.com/rasbt/LLMs-from-scratch)


<br>
&nbsp;

## 硬件要求

本书主要章节中的代码设计为大部分可以在消费级硬件上在合理时间内运行，不需要专用的服务器硬件。这种设计确保了广泛的读者都能参与学习。此外，代码会在 GPU 可用时自动利用 GPU。需要说明的是，第 2-4 章在 CPU 和 GPU 上都能良好运行。对于第 5 章和第 6 章，如果你想复现章节中的结果，建议使用 GPU。


（请参阅 [setup_tips](ch02/02_setup-tips/python-instructions.md) 文档以获取更多建议。）

&nbsp;
## 练习题

本书每章都包含若干练习题。解答汇总在附录 B 中，对应的代码 notebook 位于本仓库各章的主文件夹中（例如 [`ch02/01_main-chapter-code/ch02_exercise-solutions.ipynb`](ch02/01_main-chapter-code/ch02_exercise-solutions.ipynb)）。


&nbsp;
## 附加材料

以下文件夹包含面向感兴趣读者的可选附加材料：

- **第 2 章：使用预训练 LLM 生成文本**
  - [可选的 Python 设置与云 GPU 推荐](ch02/02_setup-tips)
  - [使用 GPU 优化版本的 LLM](ch02/03_optimized-LLM)
  - [在 Windows 上使用 `torch.compile()`](ch02/04_torch-compile-windows)
  - [运行推理并与模型对话](ch02/05_use_model)
- **第 3 章：评估 LLM**
  - [MATH-500 验证脚本](ch03/02_math500-verifier-scripts)
  - [高级解析器](ch03/03_advanced-parser)（混合 LaTeX 解析器）
- **第 4 章：通过推理时扩展改进推理能力**
  - [在 MATH-500 上的推理扩展](ch04/02_math500-inference-scaling-scripts)（CoT prompting、self-consistency）
- **第 5 章：通过自我精炼进行推理时扩展**
  - [更多 MATH-500 推理扩展实验](ch05/02_math500-more-inference-scaling-scripts)（Best-of-N、自我精炼）
- **第 6 章：使用强化学习训练推理模型**
  - [GRPO 脚本](ch06/02_rlvr_grpo_scripts_intro)（含批处理模式）
- **第 7 章：改进 GRPO 强化学习方法**
  - [高级 GRPO 脚本](ch07/03_rlvr_grpo_scripts_advanced)（包括 DeepSeek-V3.2-、Olmo3- 和 GDPO 风格的训练）
  - [下载训练检查点](ch07/04_download_trainining_checkpoints)（如何下载和使用第 6 章和第 7 章的 GRPO 检查点）
- **第 8 章：蒸馏推理模型以实现高效推理**
  - [生成蒸馏数据](ch08/02_generate_distillation_data)（通过 Ollama 或 OpenRouter 生成教师模型输出）
  - [使用蒸馏进行训练](ch08/04_train_with_distillation)（包括单样本和批量蒸馏脚本）
  - [下载训练检查点](ch08/05_download_training_checkpoints)（如何下载和使用第 8 章的蒸馏检查点）
  - [通过 Hugging Face 使用 Qwen3](ch08/06_use_via_huggingface)（如何使用基座模型和第 6-8 章检查点配合 `transformers`）
- **附录 F：LLM 评估的常见方法**
  - [MMLU 评估方法](chF/02_mmlu)
  - [LLM 排行榜](chF/03_leaderboards)
  - [LLM 作为评判者](chF/04_llm-judge)
- **附录 G：构建聊天界面**
  - [聊天界面代码](chG/01_main-chapter-code)


&nbsp;
## 问题、反馈与贡献

常见问题请参阅[故障排除指南](./troubleshooting.md)。

欢迎各类反馈，最好通过 [Manning 讨论论坛](https://livebook.manning.com/forum?product=raschka2&page=1) 或 [GitHub Discussions](https://github.com/rasbt/reasoning-from-scratch/discussions) 分享。同样，如果你有任何问题或只是想和他人讨论想法，也请随时在论坛中发帖。

请注意，由于本仓库包含与印刷版书籍对应的代码，目前无法接受会扩展主要章节代码内容的贡献，因为这会导致与实体书的偏差。保持一致性有助于确保每个人都有流畅的体验。

&nbsp;
## 引用

如果你发现本书或代码对你的研究有用，请考虑引用。

Chicago 格式引用：

> Raschka, Sebastian. *Build A Reasoning Model (From Scratch)*. Manning, 2025. ISBN: 9781633434677.

BibTeX 条目：

```
@book{build-llms-from-scratch-book,
  author       = {Sebastian Raschka},
  title        = {Build A Reasoning Model (From Scratch)},
  publisher    = {Manning},
  year         = {2025},
  isbn         = {9781633434677},
  url          = {https://mng.bz/lZ5B},
  github       = {https://github.com/rasbt/reasoning-from-scratch}
}
```
