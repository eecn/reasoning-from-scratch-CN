# 第 8 章附加材料：通过 Hugging Face 使用 Qwen3

此文件夹包含两种方式，可以将本仓库中从零实现的 [`Qwen3Model`](../../reasoning_from_scratch/qwen3.py) 和兼容的 `.pth` 检查点与 Hugging Face `transformers` 一起使用。

两种方法都可以让你使用 Hugging Face 风格的推理和训练。区别在于你是否想要一个可重用的 Hugging Face 模型目录，还是一个更轻量的本地封装器来包装现有的 PyTorch 模型。

&nbsp;
## 方法

&nbsp;
### 1) `wrapper_approach`

[./wrapper_approach](./wrapper_approach) 将模型保持为本地 `.pth` 文件，并将 `Qwen3Model` 包装在一个轻量的本地 `PreTrainedModel` 中，使其可以与部分 Hugging Face API 配合使用。

使用此方法如果你想要：

- 最少量的额外代码
- 在本仓库内进行本地实验
- 无需导出步骤即可使用 `model.generate(...)` 和 `transformers.Trainer`
- 直接从 `.pth` 加载基座模型或第 6-8 章检查点


&nbsp;
### 2) `export_approach`

[./export_approach](./export_approach) 将从零实现的 Qwen3 权重或兼容的检查点转换为 Hugging Face 兼容的模型文件夹。

使用此方法如果你想要：

- 包含 `config.json`、tokenizer 文件和权重的已保存模型目录
- `AutoConfig`、`AutoTokenizer` 和 `AutoModelForCausalLM`
- 更接近 Hugging Face 模型通常打包方式的工作流



&nbsp;
## 如何选择？

- 如果目标是学习用途以及与 `transformers` 的轻量本地集成，选择 [wrapper_approach](wrapper_approach)。
- 如果目标是创建 Hugging Face 模型包并优化计算性能，选择 [export_approach](export_approach)。
