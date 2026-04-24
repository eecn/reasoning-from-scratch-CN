
# 排行榜排名

此附加材料实现了两种不同的方式来从成对比较中构建 LM Arena（前身为 Chatbot Arena）风格的排行榜。

两种实现都通过 `--path` 参数从 json 文件中读取成对偏好列表（左：赢家，右：输家）。以下是提供的 [votes.json](votes.json) 文件的摘录：

```json
[
  ["GPT-5", "Claude-3"],
  ["GPT-5", "Llama-4"],
  ["Claude-3", "Llama-3"],
  ["Llama-4", "Llama-3"],
  ...
]
```



<br>

---

**注意**：如果你不是 `uv` 用户，请在下面的示例中将 `uv run ...py` 替换为 `python ...py`。

---

&nbsp;
## 方法 1：Elo 评分

- 实现了流行的 Elo 评分方法（灵感来自国际象棋排名），最初被 LM Arena 使用
- 详情请参阅[主 notebook](../01_main-chapter-code/chF_main.ipynb)

```bash
➜  03_leaderboards git:(main) ✗ uv run 1_elo_leaderboard.py --path votes.json

Leaderboard (Elo) 
-----------------------
 1. GPT-5       1095.9
 2. Claude-3    1058.7
 3. Llama-4      958.2
 4. Llama-3      887.2
```






&nbsp;
## 方法 2：Bradley-Terry 模型

- 实现了 [Bradley-Terry 模型](https://en.wikipedia.org/wiki/Bradley–Terry_model)，类似于新的 LM Arena 排行榜，如官方论文（[Chatbot Arena: An Open Platform for Evaluating LLMs by Human Preference](https://arxiv.org/abs/2403.04132)）中所述
- 与 LM Arena 排行榜一样，分数被重新缩放以类似于原始 Elo 分数
- 这里的代码使用 PyTorch 的 Adam 优化器来拟合模型（为了更好的代码熟悉度和可读性）



```bash
➜  03_leaderboards git:(main) ✗ uv run 2_bradley_terry_leaderboard.py --path votes.json 

Leaderboard (Bradley-Terry)
-----------------------------
 1. GPT-5       1140.6
 2. Claude-3    1058.7
 3. Llama-4      950.3
 4. Llama-3      850.4
```

