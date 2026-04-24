# 第 7 章：改进强化学习中的策略优化

本节包含高级 GRPO 脚本，扩展了第 6 章的实现，增加了额外的跟踪、稳定化和奖励建模变体。


&nbsp;
## 脚本概览

&nbsp;
### 主要脚本

- `7_3_plus_tracking.py`（*7.3 跟踪更高级的 GRPO 性能指标*）：跟踪额外的性能指标（advantage 统计量和 entropy）
- `7_4_plus_clip_ratio.py`（*7.4 使用裁剪策略比率稳定序列级 GRPO*）：在上面的基础上使用裁剪策略比率计算策略梯度损失
- `7_5_plus_kl.py`（*7.5 使用 KL 项控制模型变化幅度*）：在上面的基础上添加 KL 损失项
- `7_6_plus_format_reward.py`（*7.6 添加显式格式奖励*）：在上面的基础上为 `<think>` token 添加额外的格式奖励（与其他脚本的一个关键区别是，这是应用于推理模型而非基座模型，因为推理模型已经熟悉这些 token，如主章节中所讨论的）

<br>

&nbsp;
### GRPO 技巧附加脚本

自 GRPO 于 2024 年 4 月首次发表（[DeepSeekMath](https://arxiv.org/abs/2402.03300)）并于 2025 年 1 月流行起来（[DeepSeek-R1](https://arxiv.org/abs/2501.12948)）以来，文献中提出了许多改进。以下列出了一些最值得注意的：

1. 零梯度信号过滤（[DAPO by Yu et al., 2025](https://arxiv.org/abs/2503.14476)）
2. 主动采样（DAPO）
3. Token 级损失（DAPO）
4. 无 KL 损失（DAPO 和 [Dr. GRPO by Liu et al., 2025](https://arxiv.org/abs/2503.20783)）
5. 提高裁剪上限（DAPO）
6. 截断重要性采样（[Yao et al., 2025](https://fengyao.notion.site/off-policy-rl)）
7. 不使用标准差归一化（Dr. GRPO）
8. 使用领域特定 KL 强度进行 KL 调优；数学任务设为零（[DeepSeek V3.2](https://arxiv.org/abs/2512.02556)）
9. 重加权 KL（DeepSeek V3.2）
10. 离策略序列掩码（DeepSeek V3.2）
11. 保留 top-p / top-k 的采样掩码（DeepSeek V3.2）
12. 保持原始 GRPO advantage 归一化（DeepSeek V3.2）
13. 聚合前按奖励进行分组归一化（[GDPO by Liu et al., 2026](https://arxiv.org/abs/2601.05242)）
14. 序列级重要性采样和裁剪（[GSPO by Zheng et al., 2025](https://arxiv.org/abs/2507.18071)）
15. 裁剪重要性采样权重而非 token 更新（[CISPO by MiniMax et al., 2025](https://arxiv.org/abs/2506.13585)）

（我计划在完成主要内容后进行更详细的整理。）

<br>

以下脚本实现了其中一些改进：

- `7_7_improvements/olmo3_style.py`：此脚本在 [7_5_plus_kl.py](7_5_plus_kl.py) 基础上实现了类似 [Olmo 3](https://arxiv.org/abs/2512.13961) 的改进 1-7

- `7_7_improvements/deepseek_v32_style.py`：此脚本在 [7_5_plus_kl.py](7_5_plus_kl.py) 基础上实现了类似 [DeepSeek-V3.2](https://arxiv.org/abs/2512.02556) 的改进 8-12

- `7_7_improvements/gdpo.py`：在 [7_6_plus_format_reward.py](7_6_plus_format_reward.py) 基础上实现了 [GDPO](https://arxiv.org/abs/2601.05242)（因为 GDPO 是针对多重奖励的改进）

---

**注意**：如果你不是 `uv` 用户，请在下面的示例中将 `uv run ...py` 替换为 `python ...py`。

---


&nbsp;
