# 运行推理并与模型对话

&nbsp;

<img src="https://sebastianraschka.com/images/reasoning-from-scratch-images/bonus/chat/chat.gif?1" width=600px>

&nbsp;

此文件夹包含独立的示例脚本，用于使用我们在第 2 章（及练习）中加载的模型生成文本：

- `generate_simple.py`：以类似主要章节的方式生成文本。
- `chat.py`：与上面的代码类似，作为交互式封装，这样我们可以多次向模型发送 prompt，而无需每次都重新加载模型到内存中。
- `chat_multiturn.py`：与上面相同，但增加了记忆功能以记住消息历史。



更多使用细节在下面的章节中提供。

&nbsp;
## generate_simple.py

这个简单的函数按照第 2 章的方式加载模型，并使用第 2 章练习中的 `generate_text_simple_cache_stream` 函数。你可以按如下方式使用（如果你不使用 `uv`，请将 `uv run` 替换为 `python`）：

```bash
uv run ch02/05_use_model/generate_simple.py
Using Apple Silicon GPU (MPS)
✓ qwen3/qwen3-0.6B-base.pth already up-to-date

============================================================
torch     : 2.7.1
device    : mps
cache     : True
compile   : False
reasoning : False
============================================================

 Large language models are artificial intelligence systems that can understand, generate, and process human language, enabling them to perform a wide range of tasks, from answering questions to writing essays.

Time: 1.52 sec
22 tokens/sec
```

如果你想用基座模型或推理变体快速尝试不同的 prompt，这个函数很有用。额外选项如下所列：

```bash
usage: generate_simple.py [-h] [--device DEVICE]
                          [--max_new_tokens MAX_NEW_TOKENS] [--compile]
                          [--reasoning] [--prompt PROMPT]

Run Qwen3 text generation

options:
  -h, --help            show this help message and exit
  --device DEVICE       Device to run on (e.g. 'cpu', 'cuda', 'mps'). If not
                        provided, will auto-detect with get_device().
  --max_new_tokens MAX_NEW_TOKENS
                        Maximum number of new tokens to generate (default:
                        2048).
  --compile             Compile PyTorch model (default: False).
  --reasoning           Use reasoning model variant (default: False).
  --prompt PROMPT       Use a custom prompt. If not explicitly provided, uses
                        the following defaults: 'Explain large language models
                        in a single sentence.' for the base model, and 'Find
                        all c in Z_3 such that Z_3[x]/(x^2 + c) is a field.'
                        for the reasoning model.
```

&nbsp;
## chat.py

与上面的函数类似，此函数适用于在基座模型和推理模型上尝试不同的 prompt。

不过，与前一个函数不同的是，此函数保持用户在交互模式中，这样每次都不必重新加载模型：

```bash
uv run ch02/05_use_model/chat.py        
Using Apple Silicon GPU (MPS)
✓ qwen3/qwen3-0.6B-base.pth already up-to-date

============================================================
torch     : 2.7.1
device    : mps
cache     : True
compile   : False
reasoning : False
memory    : False
============================================================

Interactive REPL (no memory). Type '\exit' or '\quit' to quit.

>> Explain language models in 1 sentence

------------------------------------------------------------
[User]
Explain language models in 1 sentence

[Model]

Language models are algorithms that analyze and predict the likelihood of future words in a text based on the words already seen, enabling them to generate coherent and contextually relevant text.

[Stats]
Time: 1.53 sec
22 tokens/sec
------------------------------------------------------------
>> Explain machine learning in 1 sentence.

------------------------------------------------------------
[User]
Explain machine learning in 1 sentence.

[Model]
 Machine learning is a subset of artificial intelligence that enables computers to learn from data and improve their performance over time without being explicitly programmed.

[Stats]
Time: 1.04 sec
24 tokens/sec
------------------------------------------------------------
```

额外选项如下所列：

```bash
usage: chat.py [-h] [--device DEVICE] [--max_new_tokens MAX_NEW_TOKENS] [--compile]
               [--reasoning]

Run Qwen3 text generation (interactive REPL)

options:
  -h, --help            show this help message and exit
  --device DEVICE       Device to run on (e.g. 'cpu', 'cuda', 'mps'). If not provided,
                        will auto-detect with get_device().
  --max_new_tokens MAX_NEW_TOKENS
                        Maximum number of new tokens to generate (default: 2048).
  --compile             Compile PyTorch model (default: False).
  --reasoning           Use reasoning model variant (default: False).
```



&nbsp;

## chat_multiturn.py

此函数与上面类似，但增加了多轮记忆功能，使 LLM 能够记住之前轮次的对话。强烈建议在此使用推理变体，因为基座模型在对话中表现不佳：



```bash
uv run ch02/05_use_model/chat_multiturn.py --reasoning
Using Apple Silicon GPU (MPS)
✓ qwen3/qwen3-0.6B-reasoning.pth already up-to-date
✓ qwen3/tokenizer-reasoning.json already up-to-date

============================================================
torch     : 2.7.1
device    : mps
cache     : True
compile   : False
reasoning : True
memory    : True
max_new_tokens (per turn): 2048
context_length: 40960
============================================================

Interactive REPL with memory. Type '\exit' or '\quit' to quit.
Commands: \clear (forget memory), \history (show turn count)

>> What is 1+1 in short?

------------------------------------------------------------
[User]
What is 1+1 in short?

[Model]
<think>
Okay, the user is asking, "What is 1+1 in short?" Let me break this down. First, they want to know the result of adding 1 and 1. In math, 1 plus 1 equals 2. But the question says "in short," which probably means they want a concise answer without the full calculation.

So, the answer is straightforward. 1+1=2. But maybe they want a more concise way to write it? Like, "2" or "2+2"? But "2" is more direct. Let me check if there's any trick here. Sometimes people might think of 1+1 as something else, but no, it's just two ones. 

I should make sure to present the answer clearly. Since the user is asking in a short form, maybe they just want the number 2. So the final answer is 2.
</think>

1+1 equals 2.

[Stats]
Time: 8.27 sec
23 tokens/sec
------------------------------------------------------------
>> What were you just asked?

------------------------------------------------------------
[User]
What were you just asked?

[Model]
<think>
Okay, the user just asked, "What were you just asked?" and I responded with "1+1 equals 2." Now, they're asking again. Let me check if there's any hidden context or if they want more information. Since the previous answer was clear, maybe they want confirmation or a different interpretation. But since the user is asking again, perhaps they want to know if I provided the answer correctly. I should confirm that 1+1 is indeed 2 and that the answer is correct. No further information is needed here. Just a simple confirmation.

[Stats]
Time: 5.21 sec
22 tokens/sec
------------------------------------------------------------
```



额外选项如下所列：

```bash
usage: chat_multiturn.py [-h] [--device DEVICE] [--max_new_tokens MAX_NEW_TOKENS]
                         [--compile] [--reasoning]

Run Qwen3 text generation (interactive REPL)

options:
  -h, --help            show this help message and exit
  --device DEVICE       Device to run on (e.g. 'cpu', 'cuda', 'mps'). If not provided,
                        will auto-detect with get_device().
  --max_new_tokens MAX_NEW_TOKENS
                        Maximum number of new tokens to generate in each turn (default:
                        2048).
  --compile             Compile PyTorch model (default: False).
  --reasoning           Use reasoning model variant (default: False).
```

