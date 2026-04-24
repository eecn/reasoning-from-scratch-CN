# 第 8 章附加材料：通过本地 Hugging Face 封装器使用 Qwen3

此文件夹展示了如何通过将从零实现的 [`Qwen3Model`](../../../reasoning_from_scratch/qwen3.py) 包装在一个轻量的本地 `PreTrainedModel` 类中，使其与 Hugging Face `transformers` 库兼容。

这使你可以直接使用：

- `model.generate(...)`
- `transformers.Trainer`

配合本仓库的本地 `.pth` 模型文件，包括基座 Qwen3 权重和第 6-8 章的兼容检查点。

&nbsp;
## 文件

- [hf_wrapper.py](hf_wrapper.py)：围绕我们在本书中使用的从零实现的 `Qwen3Model` 的本地 `PreTrainedModel` 封装器
- [hf_inference.py](hf_inference.py)：使用封装器和仓库 tokenizer 的文本生成
- [hf_trainer.py](hf_trainer.py)：使用封装器和第 8 章蒸馏 JSON 格式的 `Trainer` 示例

---

**注意**：如果你不是 `uv` 用户，请在下面的示例中将 `uv run ...py` 替换为 `python ...py`。

---

&nbsp;
## 此封装器做了什么

封装器将模型保持在本仓库本地，并将其适配为 Hugging Face API。

具体来说，它：

- 直接将本地 `.pth` 模型文件加载到 `Qwen3Model` 中
- 将该模型包装在 `PreTrainedModel` 接口中
- 暴露与 `Trainer` 兼容的 `forward(...)` 方法
- 启用 `model.generate(...)`
- 继续使用仓库的 `Qwen3Tokenizer`

为什么？因为有些读者对在 `transformers` 中进一步探索模型感到好奇，`transformers` 比本仓库的从零代码有更多功能。

&nbsp;
## 限制

这是围绕从零模型的小型本地封装器。

重要说明：

- 适用于安装了 `reasoning_from_scratch` 的环境
- 不提供 `AutoTokenizer.from_pretrained(...)` 工作流
- 不创建包含 `config.json` 和 tokenizer 文件的可重用模型目录
- 生成部分故意保持简单，因此它重新计算完整前缀而不是将从零的 KV cache 适配到 Hugging Face cache 类；如果你需要完整支持，需要切换到 [../export_approach](../export_approach)

请注意，这些约束使代码简短，并专注于本仓库内的本地使用。

&nbsp;
## 步骤 1：安装依赖

本指南除了仓库依赖外还使用 Hugging Face Transformers。

```bash
pip install transformers accelerate
```

或者，如果你使用 `uv`：

```bash
uv add --dev transformers accelerate
```

&nbsp;
## 步骤 2：运行本地封装推理

要通过封装器运行基座模型，使用：

```bash
  uv run hf_inference.py \
    --tokenizer_kind base \
    --prompt "If x + 7 = 19, what is x?"
```

要运行推理变体，使用：

```bash
  uv run hf_inference.py \
    --tokenizer_kind reasoning \
    --prompt "If x + 7 = 19, what is x?"
```

要运行本地检查点：

```bash
uv run hf_inference.py \
  --tokenizer_kind reasoning \
  --model_path ../../04_train_with_distillation/checkpoints/distill/qwen3-0.6B-distill-step00004-epoch1.pth \
  --prompt "If x + 7 = 19, what is x?"
```

如果省略 `--model_path`，脚本会为选定的 `--tokenizer_kind` 下载默认的基座或推理模型。如果提供了 `--model_path`，它可以指向基座 Qwen3 `.pth` 文件或第 6-8 章生成的任何兼容检查点。

内部，推理脚本：

1. 构建本地封装模型
2. 将选定的 `.pth` 模型文件加载到封装的 `Qwen3Model` 中
3. 使用仓库的 tokenizer 对 prompt 进行 tokenize
4. 调用 `model.generate(...)`

&nbsp;
## 步骤 3：使用 `Trainer` 继续训练

同一封装器也可以与 `transformers.Trainer` 一起使用：

```bash
uv run hf_trainer.py \
  --tokenizer_kind reasoning \
  --model_path ../../04_train_with_distillation/checkpoints/distill/qwen3-0.6B-distill-step00004-epoch1.pth \
  --data_path ../../02_generate_distillation_data/sample_openrouter_outputs.json \
  --dataset_size 5 \
  --validation_size 1 \
  --epochs 1 \
  --logging_steps 1
```

与推理一样，`--model_path` 可以指向基座 Qwen3 权重或兼容的第 6-8 章检查点。

训练器保持第 8 章中其他地方使用的相同仅答案目标：

- prompt token 被掩码
- 只有答案 token 参与损失计算
- 推理模式将教师轨迹包装为 `<think>...</think>`

输入 JSON 格式与 [../../02_generate_distillation_data](../../02_generate_distillation_data) 中生成的蒸馏数据匹配。

&nbsp;
