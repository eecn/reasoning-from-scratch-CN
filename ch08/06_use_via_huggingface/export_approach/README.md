# 第 8 章附加材料：通过 Hugging Face Transformers 使用从零实现的 Qwen3 代码

此文件夹展示了如何将从零实现的 [`Qwen3Model`](../../../reasoning_from_scratch/qwen3.py) 和通过第 6-8 章创建的任何兼容 `.pth` 检查点转换为 Hugging Face Transformers 兼容的文件夹，以及如何使用 Hugging Face 推理函数和 `Trainer` 运行它。

导出实现为自定义 `transformers` 架构，因此可以与标准 Hugging Face API 配合使用，如 `AutoConfig`、`AutoTokenizer`、`AutoModelForCausalLM`、`model.generate(...)` 和 `Trainer`。但因为是自定义代码，加载时需要使用 `trust_remote_code=True`。

&nbsp;
## 文件

- [hf_export.py](hf_export.py)：将从零实现的 Qwen3 权重或已保存的 `.pth` 检查点转换为 Hugging Face 模型文件夹
- [hf_inference.py](hf_inference.py)：使用 `AutoModelForCausalLM` 运行文本生成
- [hf_trainer.py](hf_trainer.py)：使用 `transformers.Trainer` 在第 8 章蒸馏 JSON 格式上继续训练导出的模型
- [hf_qwen3.py](hf_qwen3.py)：导出的 Qwen3 架构的自定义 Hugging Face `PretrainedConfig` 和 `PreTrainedModel` 实现

导出脚本将 Hugging Face 特定的模型代码保留在此文件夹本地，并从 [`reasoning_from_scratch`](../../../reasoning_from_scratch) 包导入共享工具函数，用于第 3 章 prompt 模板、RoPE 辅助函数和 Qwen3 下载函数。（安装详情请参阅[第 2 章设置说明](../../../ch02/02_setup-tips/python-instructions.md)。）

---

**注意**：如果你不是 `uv` 用户，请在下面的示例中将 `uv run ...py` 替换为 `python ...py`。

---

&nbsp;
## 步骤 1：安装依赖

本指南除了仓库依赖外还使用 Hugging Face Transformers。对于 `transformers.Trainer`，你还需要 `accelerate`。

```bash
pip install transformers accelerate
```

或者，如果你使用 `uv`：

```bash
uv add --dev transformers accelerate
```

&nbsp;
## 步骤 2：导出原始 Qwen3 模型

要将原始基座模型导出为 Hugging Face 文件夹，运行：

```bash
uv run hf_export.py \
  --output_dir hf-qwen3-base \
  --tokenizer_kind "base"  # 或使用 "reasoning"
```

如果你已经在本地有原始 `.pth` 模型和 tokenizer，可以避免下载：

```bash
uv run hf_export.py \
  --output_dir hf-qwen3-base \
  --tokenizer_kind base \
  --model_path ../../../ch02/01_main-chapter-code/qwen3/qwen3-0.6B-base.pth \
  --tokenizer_path ../../../ch02/01_main-chapter-code/qwen3/tokenizer-base.json
```

第 6-8 章检查点的 `.pth` 文件也同样适用。

导出的文件夹将包含：

- `config.json`
- `generation_config.json`
- tokenizer 文件
- 模型权重（默认为 `model.safetensors`）
- `trust_remote_code=True` 所需的自定义 Python 模块副本

&nbsp;
### 导出代码做了什么

导出器不会将模型转换为官方 Hugging Face Qwen 实现，也不会修改学到的权重。它做的是以下内容：

1. 构建一个自定义的 Hugging Face `PretrainedConfig` 和 `PreTrainedModel`，复现从零实现的 `Qwen3Model` 架构
2. 将原始 `.pth` 的 `state_dict` 直接加载到该自定义 Hugging Face 模型中，不重命名或重塑可训练参数
3. 使用标准 Hugging Face 文件夹格式保存结果，以便 `AutoConfig`、`AutoTokenizer`、`AutoModelForCausalLM`、`generate(...)` 和 `Trainer` 可以加载它

添加或包装的主要内容是：

- Hugging Face 配置文件（`config.json`）
- 具有与 `transformers` 兼容的 `forward(...)` 签名的 Hugging Face 模型类
- Hugging Face tokenizer 文件
- Hugging Face 生成元数据（`generation_config.json`）
- `trust_remote_code=True` 加载的自定义 Python 源文件

导出过程中有一个小的额外细节。即，从零实现的检查点只保存可训练权重，而 Hugging Face 导出还打包了预计算的 RoPE `cos` 和 `sin` 缓冲区，使重新加载导出的模型在数值上保持一致。

对于 `--tokenizer_kind reasoning`，导出器还会将推理聊天模板附加到 tokenizer，以便推理脚本可以自动将 prompt 包装为预期的聊天格式。

因为此自定义 Hugging Face 模块导入了已安装的 [`reasoning_from_scratch`](../../../reasoning_from_scratch) 包，只要该包安装在 Python 环境中，导出的文件夹就是兼容的。

&nbsp;
## 步骤 3：导出已保存的检查点

同一导出器也适用于第 8 章蒸馏检查点或本仓库生成的任何其他兼容 `.pth` 文件。

例如，如果你使用推理 tokenizer 训练了第 8 章检查点：

```bash
uv run hf_export.py \
  --output_dir hf-qwen3-distill \
  --model_path ../../04_train_with_distillation/checkpoints/distill/qwen3-0.6B-distill-step00004-epoch1.pth \
  --tokenizer_kind reasoning
```

重要说明：

- 对于第 8 章蒸馏检查点和其他使用推理 tokenizer 训练的检查点，使用 `--tokenizer_kind reasoning`。
- 对于使用基座 tokenizer 训练的检查点，使用 `--tokenizer_kind base`。
- 如果匹配的 tokenizer JSON 已在磁盘上，可以通过 `--tokenizer_path` 传入以避免下载。

&nbsp;
## 步骤 4：运行 Hugging Face 推理

导出后，使用 `AutoTokenizer` 和 `AutoModelForCausalLM` 运行推理：

```bash
uv run hf_inference.py \
  --model_dir hf-qwen3-base \
  --prompt "If x + 7 = 19, what is x?"
```

内部，脚本：

1. 使用 `trust_remote_code=True` 加载导出的模型
2. 使用第 3 章相同的数学 prompt 模板格式化 prompt
3. 当导出的模型使用推理 tokenizer 时，自动应用推理聊天封装
4. 调用 `model.generate(...)`

如果你更喜欢直接使用原始 Hugging Face API，等效的模式是：

```python
from transformers import AutoConfig, AutoModelForCausalLM, AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained("hf-qwen3-base")
config = AutoConfig.from_pretrained("hf-qwen3-base", trust_remote_code=True)
model = AutoModelForCausalLM.from_pretrained(
    "hf-qwen3-base",
    trust_remote_code=True,
)
```

&nbsp;
## 步骤 5：使用 `Trainer` 继续训练

你可以使用 Hugging Face `Trainer` 在 [`../../04_train_with_distillation`](../../04_train_with_distillation) 中使用的相同 JSON 格式上继续训练导出的检查点。

示例：

```bash
uv run hf_trainer.py \
  --model_dir hf-qwen3-base \
  --data_path ../../02_generate_distillation_data/sample_openrouter_outputs.json \
  --dataset_size 5 \
  --validation_size 1 \
  --epochs 1 \
  --logging_steps 1 \
  --save_steps 10
```

该脚本保持与从零蒸馏代码相同的仅答案训练目标：

- prompt token 从损失中被掩码
- 只有蒸馏答案 token 参与交叉熵损失计算
- 对于推理导出，脚本在最终答案前将教师轨迹包装为 `<think>...</think>`



&nbsp;
## 在其他地方加载导出的模型

导出后，你可以将文件夹复制到另一台机器或上传到 Hugging Face Hub 并在那里加载，只要该环境中安装了 `reasoning_from_scratch`：

```python
from transformers import AutoModelForCausalLM, AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained(
    "path-or-hub-repo",
    trust_remote_code=True,
)
model = AutoModelForCausalLM.from_pretrained(
    "path-or-hub-repo",
    trust_remote_code=True,
)
```
