# 附录 G：构建聊天界面



此文件夹包含用于运行类 ChatGPT 用户界面的代码，可用于与本书中使用和/或开发的 LLM 进行交互，如下所示。



![Chainlit UI 示例](https://sebastianraschka.com/images/LLMs-from-scratch-images/bonus/qwen/qwen3-chainlit.gif)



为了实现此用户界面，我们使用开源的 [Chainlit Python 包](https://github.com/Chainlit/chainlit)。

&nbsp;
## 第 1 步：安装依赖

首先，安装 `chainlit` 包和依赖项：

```bash
pip install chainlit
```

或者，如果你使用的是 `uv`：

```bash
uv add chainlit
```



&nbsp;

## 第 2 步：运行 `app` 代码

此文件夹包含 2 个文件：

1. [`qwen3_chat_interface.py`](qwen3_chat_interface.py)：此文件加载并使用 Qwen3 0.6B 模型的思考模式（thinking mode）。
2. [`qwen3_chat_interface_multiturn.py`](qwen3_chat_interface_multiturn.py)：与上面相同，但配置为记住消息历史。

（打开并检查这些文件以了解更多信息。）

从终端运行以下命令之一来启动 UI 服务器：

```bash
chainlit run qwen3_chat_interface.py
```

或者，如果你使用的是 `uv`：

```bash
uv run chainlit run qwen3_chat_interface.py
```

运行上述命令之一后，应该会打开一个新的浏览器标签页，你可以在其中与模型交互。如果浏览器标签页没有自动打开，请检查终端命令并将本地地址复制到浏览器地址栏中（通常地址为 `http://localhost:8000`）。

## 使用自定义检查点

由于 `chainlit run ...` 占用了命令行参数，这些脚本从 `CHECKPOINT_PATH` 环境变量（而非 `argparse`）中读取自定义检查点路径。

终端示例：

```bash
CHECKPOINT_PATH=/absolute/path/to/qwen3-0.6B-distill-step06682-epoch1.pth \
uv run chainlit run qwen3_chat_interface.py
```

注意事项：

- 保持脚本中的 `WHICH_MODEL` 与检查点所期望的分词器（tokenizer）一致。
- 来自 [`ch08/05_download_training_checkpoints`](../../ch08/05_download_training_checkpoints) 的第 8 章检查点使用推理分词器，因此请使用 `WHICH_MODEL = "reasoning"`。
- 当设置了 `CHECKPOINT_PATH` 时，脚本只会将分词器下载到 `LOCAL_DIR`；它不会重新下载默认模型权重。
