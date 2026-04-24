# 第 6 章：使用强化学习训练推理模型

&nbsp;

&nbsp;
## 附加材料

- [rlvr_grpo_original_no_kl.py](rlvr_grpo_original_no_kl.py)：实现原始 GRPO 算法的脚本，用于通过可验证奖励的强化学习（RLVR）训练推理模型。该算法被 [DeepSeek R1](https://arxiv.org/abs/2501.12948) 使用，最初在 [DeepSeekMath](https://arxiv.org/abs/2402.03300) 论文中提出。但是，此脚本省略了 KL 散度项（如 [DAPO](https://arxiv.org/abs/2503.14476)、[Dr. GRPO](https://arxiv.org/abs/2503.20783)、[Olmo 3](https://arxiv.org/abs/2512.13961) 等论文所建议的）
  - KL 散度项确保训练后的模型不会偏离原始模型太多，但它可能会影响性能（特别是在数学任务上）
  - 此脚本在概念上与第 6 章中的代码相同；但有两个次要性能调优：
    1. 移除了 `torch.multinomial` 采样器中的 `.cpu()` 转换，将吞吐量提高了 20%；有关实现细节请参阅 [PR #178](https://github.com/rasbt/reasoning-from-scratch/pull/178)
    2. 使用 `--skip-zero-advantage-updates` 标志时，如果所有奖励相等则跳过模型更新，这进一步加快了训练速度并可以降低内存需求（因为超过 `--max_new_tokens` 的长序列开销最大，且通常由于在生成正确答案前就达到 token 限制而导致零奖励）；有关实现细节请参阅 [PR #186](https://github.com/rasbt/reasoning-from-scratch/pull/186)
    - 如果你想查看没有上述两项改进的脚本，可以在[这里](https://github.com/rasbt/reasoning-from-scratch/blob/da009e41aacb17a433968cf84a4a6cf2a0fa4655/ch06/02_rlvr_grpo_scripts_intro/rlvr_grpo_original_no_kl.py)查看原始代码
- [rlvr_grpo_original_no_kl_batched.py](rlvr_grpo_original_no_kl_batched.py)：与上面相同，但支持批量训练。但请注意，这会增加内存需求，因此可能需要降低 rollout 数量和 rollout 长度。使用方式与上面的脚本相同，但增加了 `--num_batches`。
  - 注意，与第 3 章的 [evaluate_math500_batched.py](https://github.com/rasbt/reasoning-from-scratch/blob/main/ch03/02_math500-verifier-scripts/evaluate_math500_batched.py) 代码不同，此代码不需要从 [qwen3_batched.py](https://github.com/rasbt/reasoning-from-scratch/blob/main/reasoning_from_scratch/qwen3_batched.py) 导入 `Qwen3Model`，详情请参阅 [PR #179](https://github.com/rasbt/reasoning-from-scratch/pull/179)

- [rlvr_grpo_original_no_kl_batched_fsdp.py](rlvr_grpo_original_no_kl_batched_fsdp.py)：与上面相同，但支持使用 PyTorch 的 FSDP 在多个 GPU 上训练。如果你可以使用多个 GPU，这是推荐的训练脚本。使用方式与上面的脚本相同，但增加了 `--num_gpus`。

这些脚本从 [`reasoning_from_scratch`](../../reasoning_from_scratch) 包导入部分功能以避免代码重复。（安装详情请参阅[第 2 章设置说明](../../ch02/02_setup-tips/python-instructions.md)。）但在这种情况下，代码也重新实现了章节本身的核心函数，以便更容易检查和修改。



<br>

---

**注意**：如果你不是 `uv` 用户，请在下面的示例中将 `uv run ...py` 替换为 `python ...py`。

---


&nbsp;

|      | 方法                                   | 步骤 | 最大 token 数 | Rollout 数量 | MATH-500 准确率 | 平均 token 数   |
| ---- | -------------------------------------- | ---- | ------------- | ------------ | --------------- | --------------- |
| 1    | 基座（第 3 章）                         | -    |               |              | 15.2%           | 78.85           |
| 2    | 推理（第 3 章）                         | -    |               |              | 48.2%           | 1369.79         |
| 3    | GRPO 原始版（第 7 章）                  | 50   | 512           | 8            | 33.4%           | 910.33          |
| 4    | GRPO 原始版（第 7 章）                  | 100  | 512           | 8            | 0.4%            | 1168.05         |
| 5    | GRPO 原始版无 KL（本章）                | 50   | 512           | 8            | 47.4%           | 586.11          |
| 6    | GRPO 原始版无 KL（本章）                | 100  | 512           | 8            | 44.0%           | 555.95          |
| 7    | GRPO Olmo 3 改进版（第 7 章）           | 50   | 512           | 8            | 46.4%           | 601.61          |
| 8    | GRPO Olmo 3 改进版（第 7 章）           | 100  | 512           | 8            | 45.4%           | 589.51          |
| 9    | GRPO DeepSeek V3.2 改进版（第 7 章）    | 50   | 512           | 8            | 44.2%           | 618.49          |
| 10   | GRPO DeepSeek V3.2 改进版（第 7 章）    | 100  | 512           | 8            | 45.2%           | 676.96          |

检查点每 50 步保存一次。如果你通过 KeyboardInterrupt 中断脚本，它也会保存最后一步作为检查点。

注意训练只允许最多 512 个生成 token（上表中的最大 token 数），以使其在所需计算内存方面更易于访问。

但是，评估脚本（与第 3 章相同的方法）允许最多 2048 个生成 token，上表中的"平均 token 数"列衡量了在 MATH-500 测试数据集上平均使用了多少 token。（训练在 MATH 数据集中不与 MATH-500 测试集重叠的 12,000 个样本上进行。详情请参阅 [https://github.com/rasbt/math_full_minus_math500](https://github.com/rasbt/math_full_minus_math500)。）

**第 1 行**

```bash
uv run ../../ch03/02_math500-verifier-scripts/evaluate_math500.py \
--dataset_size 500 \
--which_model base
```

- 提示：你可以在上面的代码执行命令中添加 `--show_eta` 来显示脚本在你的机器上的总运行时间预估

**第 2 行**

```bash
uv run ../../ch03/02_math500-verifier-scripts/evaluate_math500.py \
--dataset_size 500 \
--which_model reasoning
```

**第 3 和第 4 行**

```bash
uv run ../../ch07/02_rlvr_grpo_scripts_advanced/rlvr_grpo_original.py \
--num_rollouts 8 \
--max_new_tokens 512 
```

然后，要评估模型，对生成的检查点运行 `evaluate_math500.py` 脚本。例如：

```bash
uv run ../../ch03/02_math500-verifier-scripts/evaluate_math500.py \
--dataset_size 500 \
--which_model base \
--checkpoint_path checkpoints/rlvr_grpo_original/qwen3-0.6B-rlvr-grpo-step00050.pth
```

**第 5 和第 6 行**

```bash
uv run rlvr_grpo_original_no_kl.py \
--num_rollouts 8 \
--steps 100 \
--max_new_tokens 512
```

**第 7 和第 8 行**

```bash
uv run ../../ch07/02_rlvr_grpo_scripts_original/rlvr_grpo_olmo3.py \
--num_rollouts 8 \
--max_new_tokens 512 
```

**第 9 和第 10 行**

```bash
uv run ../../ch07/02_rlvr_grpo_scripts_original/rlvr_grpo_deepseek_v32.py \
--num_rollouts 8 \
--max_new_tokens 512 
```


<br>

如果你的 RAM 不足，考虑降低 rollout 数量（`--num_rollouts`）或响应长度（`--max_new_tokens`）。下表列出了一些资源需求供参考。



| num_rollouts | max_new_tokens | 所需 RAM (GB)     |
| ------------ | -------------- | ----------------- |
| 8            | 1024           | 30.50 GB          |
| 8            | 512            | 20.31 GB          |
| 8            | 256            | 15.60 GB          |
| 4            | 1024           | 12.80 GB          |
| 4            | 512            | 14.60 GB          |
| 4            | 256            | 10.59 GB          |


请注意，降低 token 数或 rollout 数量可能会对性能产生负面影响。如果你使用较低的 rollout 数量，可以通过将 `--accum_steps` 从 1 增加到 2 或 4（梯度累积）来在一定程度上提高训练稳定性；但这需要更多的计算时间。

注意在这些设置下，原始（"vanilla"）GRPO 方法在超过 50 步时不太稳定，如果你想训练超过 50 步，可能需要考虑第 7 章中的改进版本。


<br>

注意原始 GRPO 算法可以通过多种方式改进以稳定和提升训练效果，这是[下一章](../../ch07)的主题。



&nbsp;
## 绘制训练曲线

[plot_metrics.py](plot_metrics.py) 可用于绘制 CSV 格式的训练运行数据。`logs` 文件夹中包含一个 200 步的示例运行（日志文件使用默认设置创建，除了将 `--max_new_tokens 2048` 调高）：

```bash
uv run plot_metrics.py \
--csv logs/rlvr_grpo_original_no_kl_metrics.csv \
--moving_average 20
```

（`--moving_average 20` 设置对过去 20% 的步骤取平均以获得更平滑的趋势线。）

<img src="https://sebastianraschka.com/images/reasoning-from-scratch-images/ch06/other/plot.webp?1" width="600px">
