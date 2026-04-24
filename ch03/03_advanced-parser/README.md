# 第 3 章：高级解析器（附加材料）

此文件夹包含来自 [issue #133](https://github.com/rasbt/reasoning-from-scratch/issues/133) 的解析器实验，其中提出了一种混合 LaTeX 解析器来处理当前章节解析器可能遗漏的边缘情况。



&nbsp;

## 文件

- [compare_with_current_parser.ipynb](compare_with_current_parser.ipynb)：包含使用示例的 notebook
- [math500_gpt_answers.json](math500_gpt_answers.json)：包含 LLM 回答的 MATH-500 示例，用于上面 notebook 中的一个章节
- [gen_llm_answers.py](gen_llm_answers.py)：从 Qwen3 模型获取 JSON 格式 boxed 答案的便捷脚本
- [evaluate_math500_advanced.py](evaluate_math500_advanced.py)：与第 3 章的 LLM 评估脚本 [evaluate_math500.py](../02_math500-verifier-scripts/evaluate_math500.py) 相同，但支持 `--hybrid_parser` 作为额外参数来使用替代的混合解析器，例如：

```python
uv run evaluate_math500_advanced.py --dataset_size 500 --hybrid_parser
```



&nbsp;
## 与第 3 章解析器的区别

[reasoning_from_scratch/ch03.py](../../reasoning_from_scratch/ch03.py) 中的章节解析器设计得紧凑且易于教学：

- 它专注于轻量级的规范化加符号等价性检查
- 主要将答案视为算术/符号表达式

此文件夹中的混合解析器（`latex_normalizer_hybrid.py`）以模式匹配优先且覆盖面更广：

- 它在回退解析之前先识别答案格式
- 增加了对区间、并集、方程、矩阵、集合表示法、成员关系（`\in`）和 `\pm` 的支持
- 更好地保留重要的边缘情况，如带下标的答案（`52_8`）和文本大小写（`\text{Evelyn}`）

行为不同的示例：

- `52_8` -> 章节路径通常解析为 `528`；混合解析器保留 `52_8`
- `11,\! 111,\! 111,\! 100` -> 章节路径可能变为元组；混合解析器规范化为 `11111111100`
- `(0,9) \cup (9,36)` -> 章节路径通常保持为文本；混合解析器返回符号化的并集

权衡：

- 章节解析器：更简单、更快、更容易理解
- 混合解析器：在 LaTeX 边缘情况上覆盖更好，但规则和复杂度更多；还增加了 SymPy LaTeX 后端依赖

&nbsp;
## 使用方法

你可以直接从包中导入混合解析器：

```python
from reasoning_from_scratch.bonus.parser import normalize_text_hybrid, sympy_parser_hybrid
```

更多详细使用示例请参阅 [compare_with_current_parser.ipynb](compare_with_current_parser.ipynb)。
