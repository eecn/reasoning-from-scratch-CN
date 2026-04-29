# 第 3 章：评估推理模型

&nbsp;
## 主要章节代码

- [ch03_main.ipynb](ch03_main.ipynb)：主要章节代码
- [ch03_exercise-solutions.ipynb](ch03_exercise-solutions.ipynb)：练习题解答


&nbsp;
## 附加材料

- [../02_math500-verifier-scripts/evaluate_math500.py](../02_math500-verifier-scripts/evaluate_math500.py)：在 MATH-500 数据集上评估模型的独立脚本
- [../02_math500-verifier-scripts/evaluate_math500_batched.py](../02_math500-verifier-scripts/evaluate_math500_batched.py)：与上面相同，但在生成过程中并行处理多个样本（以获得更高吞吐量）

两个评估脚本都从 [`reasoning_from_scratch`](../../reasoning_from_scratch) 包导入功能以避免代码重复。（安装详情请参阅[第 2 章设置说明](../../ch02/02_setup-tips/python-instructions.md)。）
