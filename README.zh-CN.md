# 前言
此前， [LLMs-from-scratch-CN](https://github.com/MLNLP-World/LLMs-from-scratch-CN) 对 [LLMs-from-scratch](https://github.com/rasbt/LLMs-from-scratch.git) 的翻译工作，让我受益匪浅。虽然英语技术文章阅读难度不大，但是中文能提供更直觉、更系统的思维和清晰的知识结构。这个翻译工作帮助我快速了解掌握了一些大模型的实现细节。

近日，原作者 Sebastian Raschka 博士开启了新项目 [reasoning-from-scratch](https://github.com/rasbt/reasoning-from-scratch.git)，旨在从零开始解析大语言模型的推理机制。为追踪此技术前沿，我决定尝试独自进行该系列内容的同步翻译工作，并在一些需要解释验证的地方增加注解。翻译工作纯属个人学习，旨在为中文社区提供一份参考。疏漏之处，欢迎指正。

这里收集了机器之心对Sebastian Raschka 博士的博客文章[first-look-at-reasoning-from-scratch](https://magazine.sebastianraschka.com/p/first-look-at-reasoning-from-scratch) 的翻译 [从头开始推理](/reasoning-from-scratch-CN/Sebastian_Raschka_first-look-at-reasoning-from-scratch.md)，以供参考。

因为源代码还在更新中，所以我保留了原始的英文内容以供参考。

# Build A Reasoning Model (From Scratch)

This repository contains the code for developing an LLM reasoning model and is the official code repository for the book [*Build a Reasoning Model (From Scratch)*](https://mng.bz/lZ5B).

这个仓库包含了开发LLM推理模型的代码，是 [*Build a Reasoning Model (From Scratch)*](https://mng.bz/lZ5B) 一书的官方代码仓库。


<br>
<br>

<a href="https://mng.bz/lZ5B"><img src="https://sebastianraschka.com/images/reasoning-from-scratch-images/cover.webp?123" width="250px"></a>

(Printed in color.)

<br>

In [*Build a Reasoning Model (From Scratch)*](https://mng.bz/lZ5B), you will learn and understand how a reasoning large language model (LLM) works.

在[*Build a Reasoning Model (From Scratch)*](https://mng.bz/lZ5B) 一书中，你将学习并理解推理大语言模型（LLM）的工作原理。

Reasoning is one of the most exciting and important recent advances in improving LLMs, but it’s also one of the easiest to misunderstand if you only hear the term reasoning and read about it in theory. This is why this book takes a hands-on approach. We will start with a pre-trained base LLM and then add reasoning capabilities ourselves, step by step in code, so you can see exactly how it works.

推理(reasoning)是最近改进LLM的最令人兴奋和重要的进展之一，但如果你只听到推理这个术语并在理论上阅读它，它也最容易被误解。这就是为什么这本书采用了动手实践的方法。我们将从一个预训练的基础LLM开始，然后逐步添加推理功能，在代码中，你可以看到它究竟是如何工作的。

The methods described in this book walk you through the process of developing your own small-but-functional reasoning model for educational purposes. It mirrors the approaches used in creating large-scale reasoning models such as DeepSeek R1, GPT-5 Thinking, and others. In addition, this book includes code for loading the weights of existing, pretrained models.

本书介绍的方法将引导你逐步开发自己的小型但功能齐全的推理模型，以供教学目的。它反映了在创建大规模推理模型（如DeepSeek R1、GPT-5 Thinking等）时使用的方法。此外，本书还包括加载现有、预训练模型权重的代码。

- Link to the official [source code repository](https://github.com/rasbt/reasoning-from-scratch)
- Link to the [book at Manning](https://mng.bz/lZ5B) (the publisher's website)
- Link to the book page on Amazon.com (TBD)
- ISBN 9781633434677

- 链接到官方 [源代码仓库](https://github.com/rasbt/reasoning-from-scratch)
- 链接到 [Manning出版社](https://mng.bz/lZ5B) 的 [书籍](https://mng.bz/lZ5B)（出版商网站）
- 链接到亚马逊上的书籍页面（待定）
- ISBN 9781633434677



<br>
<br>

To download a copy of this repository, click on the [Download ZIP](https://github.com/rasbt/reasoning-from-scratch/archive/refs/heads/main.zip) button or execute the following command in your terminal:

要下载此仓库的副本，请点击 [下载ZIP](https://github.com/rasbt/reasoning-from-scratch/archive/refs/heads/main.zip) 按钮，或在终端中执行以下命令：

```bash
git clone --depth 1 https://github.com/rasbt/reasoning-from-scratch.git
```

翻译仓库：
```bash
git clone --depth 1 https://github.com/eecn/reasoning-from-scratch-CN.git
```

<br>


> **Tip:**
> Chapter 2 provides additional tips on installing Python, managing Python packages, and setting up your coding environment.

> **提示：** 第2章提供了有关安装Python、管理Python包和设置编码环境的额外提示。

<br>
<br>

## Table of Contents (In Progress) 目录（进行中）


[![Code tests Linux](https://github.com/rasbt/reasoning-from-scratch/actions/workflows/tests-linux.yml/badge.svg)](https://github.com/rasbt/reasoning-from-scratch/actions/workflows/tests-linux.yml)
[![Code tests macOS](https://github.com/rasbt/reasoning-from-scratch/actions/workflows/tests-macos.yml/badge.svg)](https://github.com/rasbt/reasoning-from-scratch/actions/workflows/tests-macos.yml)
[![Code tests Windows](https://github.com/rasbt/reasoning-from-scratch/actions/workflows/tests-windows.yml/badge.svg)](https://github.com/rasbt/reasoning-from-scratch/actions/workflows/tests-windows.yml)

| Chapter Title                                                | Main Code                                                    |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 第一章: 理解推理模型                         | 无代码(可参考first-look-at-reasoning-from-scratch译文了解)                                                      |
| 第二章: 使用预训练LLM生成文本                 | - [ch02_main.ipynb](ch02/01_main-chapter-code/ch02_main.ipynb)<br/>- [ch02_exercise-solutions.ipynb](ch02/01_main-chapter-code/ch02_exercise-solutions.ipynb) |
| Ch 3: Evaluating reasoning models                            | TBA                                                          |
| Ch 4: Improving reasoning with inference-time scaling        | TBA                                                          |
| Ch 5: Training reasoning models with reinforcement learning  | TBA                                                          |
| Ch 6: Distilling reasoning models for efficient reasoning    | TBA                                                          |
| Ch 7: Improving the reasoning pipeline and future directions | TBA                                                          |
| Appendix A: References and further reading                   | No code                                                      |
| 附录 B: 练习答案                               | 代码和解答位于各章节的子文件夹中           |
| 附录 C: Qwen3大语言模型源码                            | - [chC_main.ipynb](chC/01_main-chapter-code/chC_main.ipynb)  |

<br>
&nbsp;

The mental model below summarizes the main techniques covered in this book.

下面这个心理模型总结了本书介绍的主要技术。

<img src="https://sebastianraschka.com/images/reasoning-from-scratch-images/mental-model.webp" width="650px">



<br>



&nbsp;
## Companion Book 配套书籍

Please note that *Build A Reasoning Model (From Scratch)* is a standalone book focused on methods to improve LLM reasoning.

请注意，*Build A Reasoning Model (From Scratch)* 是一本独立的书籍，专注于改进LLM推理的方法。

In this book, we work with a pre-trained open-source base LLM (Qwen3) on top of which we code apply reasoning methods from scratch. This includes inference-time scaling, reinforcement learning, and distillation.

本书中，我们在预训练的开源基础LLM（Qwen3）上工作，并从头开始编码推理方法。这包括推理时扩展、强化学习和蒸馏。

However, if you are interested in understanding how a conventional base LLM is implemented, you may like my previous book, [*Build a Large Language Model (From Scratch)*](https://amzn.to/4fqvn0D).

然而，如果你对了解如何实现传统的基础LLM感兴趣，你可能喜欢我之前的书，[*Build a Large Language Model (From Scratch)*](https://amzn.to/4fqvn0D)。

<a href="https://amzn.to/4fqvn0D"><img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/cover.jpg?123" width="120px"></a>

- [Amazon link](https://amzn.to/4fqvn0D)
- [Manning link](http://mng.bz/orYv)
- [GitHub repository](https://github.com/rasbt/LLMs-from-scratch)


<br>
&nbsp;

## Hardware Requirements 硬件要求

The code in the main chapters of this book is designed to mostly run on consumer hardware within a reasonable timeframe and does not require specialized server hardware. This approach ensures that a wide audience can engage with the material. Additionally, the code automatically utilizes GPUs if they are available. That being said, chapters 2-4 will work well on CPUs and GPUs. For chapters 5 and 6, it is recommended to use a GPU if you want to replicate the results in the chapter.

本书主要章节中的代码旨在在合理的时间内主要在消费者硬件上运行，并且不需要专用服务器硬件。这种方法确保了广泛的受众可以参与材料。此外，代码在可用时会自动利用GPU。也就是说，第2-4章在CPU和GPU上都能很好地工作。对于第5章和第6章，如果希望复制章节中的结果，建议使用GPU。


(Please see the [setup_tips](ch02/https://github.com/rasbt/reasoning-from-scratch/blob/main/ch02/01_main-chapter-code/python-instructions.md) doc for additional recommendations.)

（请参阅 [setup_tips](ch02/https://github.com/rasbt/reasoning-from-scratch/blob/main/ch02/01_main-chapter-code/python-instructions.md) 文档以获取其他建议。）

&nbsp;
## Exercises 练习

Each chapter of the book includes several exercises. The solutions are summarized in Appendix B, and the corresponding code notebooks are available in the main chapter folders of this repository (for example,  [`ch02/01_main-chapter-code/ch02_exercise-solutions.ipynb`](ch02/01_main-chapter-code/ch02_exercise-solutions.ipynb)).

本书的每一章都包含几个练习。解决方案总结在附录B中，相应的代码笔记本可在本仓库的主要章节文件夹中找到（例如，[`ch02/01_main-chapter-code/ch02_exercise-solutions.ipynb`](ch02/01_main-chapter-code/ch02_exercise-solutions.ipynb))。

&nbsp;
## Questions, Feedback, and Contributing to This Repository 问题、反馈和为本仓库做贡献


I welcome all sorts of feedback, best shared via the [Manning Discussion Forum](https://livebook.manning.com/forum?product=raschka2&page=1) or [GitHub Discussions](https://github.com/rasbt/reasoning-from-scratch/discussions). Likewise, if you have any questions or just want to bounce ideas off others, please don't hesitate to post these in the forum as well.

我欢迎各种反馈，最好通过 [Manning Discussion Forum](https://livebook.manning.com/forum?product=raschka2&page=1) 或 [GitHub Discussions](https://github.com/rasbt/reasoning-from-scratch/discussions) 来分享。同样，如果你有任何问题，或者只是想与其他人讨论想法，请不要犹豫，在论坛上发帖。

Please note that since this repository contains the code corresponding to a print book, I currently cannot accept contributions that would extend the contents of the main chapter code, as it would introduce deviations from the physical book. Keeping it consistent helps ensure a smooth experience for everyone.

请注意，由于此仓库包含与印刷书籍相对应的代码，我目前无法接受会扩展主要章节代码内容的贡献，因为它会偏离物理书籍。保持一致性有助于确保每个人的体验顺畅。

&nbsp;
## Citation 引用

If you find this book or code useful for your research, please consider citing it.

如果你觉得这本书或代码对你的研究有帮助，请考虑引用它。

Chicago-style citation: 芝加哥式引用：

> Raschka, Sebastian. *Build A Reasoning Model (From Scratch)*. Manning, 2025. ISBN: 9781633434677.

BibTeX entry: BibTeX条目：

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