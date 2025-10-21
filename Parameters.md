# 🎯 微调参数说明（Tuning Hyperparameters）

> 本文档详细介绍在 LLaMA Factory 框架下进行指令微调（SFT）时的重要超参数（Hyperparameters）。
>
> 参数的合理配置直接决定模型学习的稳定性与效果。

---

## 📚 目录（Table of Contents）

- [1. 学习率（Learning Rate）](#1-学习率learning-rate)
- [2. 训练轮数（Number-of-Epochs）](#2-训练轮数number-of-epochs)
- [3. 批量大小（Batch-Size）](#3-批量大小batch-size)
- [4. 截断长度（Cutoff-Length）](#4-截断长度cutoff-length)
- [5. LoRA 秩（LoRA-Rank）](#5-lora-秩lora-rank)
- [6. 验证集比例（Validation-Size）](#6-验证集比例validation-size)
- [7. 训练命令配置（Training-Command）](#7-训练命令配置training-command)
- [8. 显存消耗估算（VRAM-Estimation）](#8-显存消耗估算vram-estimation)
- [9. 显存优化技巧（Optimization-Tips）](#9-显存优化技巧optimization-tips)
- [10. 参数汇总表（Summary）](#10-参数汇总表summary)

---

## 1. 学习率（Learning Rate）

| 项目 | 内容 |
|------|------|
| **定义** | 控制每次梯度更新的步长。 |
| **直观理解** | 模型学习的“节奏”：太大会“学偏”，太小则“学不动”。 |
| **经验建议** | 对于全参数微调（Full Finetune），推荐 `4e-5 ~ 5e-5`。 |
| **显存影响** | 几乎无。 |
| **本次取值** | **5e-5** |

> ⚙️ **建议**：若训练 loss 不下降，可尝试稍增；若震荡或发散，适当减小。

---

## 2. 训练轮数（Number of Epochs）

| 项目 | 内容 |
|------|------|
| **定义** | 模型完整遍历一次训练集称为一个 Epoch。 |
| **直观理解** | “复习几遍教材”。 |
| **经验建议** | 通常 3 轮足够；可根据 loss 变化微调。 |
| **显存影响** | 无显著影响。 |
| **本次取值** | **3** |

---

## 3. 批量大小（Batch Size）

| 项目 | 内容 |
|------|------|
| **定义** | 每次参数更新的样本数量。 |
| **直观理解** | “一次做几道题”。 |
| **经验建议** | 小显存环境推荐 `per_device_train_batch_size=1` 且 `gradient_accumulation_steps=4~8`。 |
| **显存影响** | 高度相关。批量越大显存占用越高。 |
| **本次取值** | 批大小 **1**；累计步数 **8** |

---

## 4. 截断长度（Cutoff Length）

| 项目 | 内容 |
|------|------|
| **定义** | 模型单次能处理的最大 Token 数。 |
| **直观理解** | “每次能看多长的文章”。 |
| **经验建议** | 根据数据分布选取 P95 或 P99。过长易爆显存。 |
| **显存影响** | 显著。每翻倍，显存近似线性增加。 |
| **本次取值** | **4096** |

> 💡 可使用 [tiktokenizer.vercel.app](https://tiktokenizer.vercel.app/) 计算文本 Token 数。

---

## 5. LoRA 秩（LoRA Rank）

| 项目 | 内容 |
|------|------|
| **定义** | 控制可训练权重矩阵的秩（低秩分解）。 |
| **直观理解** | “学习模板数量”：秩高则更灵活但易过拟合。 |
| **经验建议** | 推荐区间 `8 ~ 16`，一般不超过 16。 |
| **显存影响** | 中等。rank 每翻倍约增加 1GB 显存。 |
| **本次取值** | **8** |

---

## 6. 验证集比例（Validation Size）

| 项目 | 内容 |
|------|------|
| **定义** | 训练集划分出的验证样本比例。 |
| **经验建议** | 小数据集取 `0.1~0.2`，大数据集取 `0.05~0.1`。 |
| **显存影响** | 无。 |
| **本次取值** | **0.15（约 431 条样本）** |

---

## 7. 训练命令配置（Training Command）

```bash
llamafactory-cli train \
  --stage sft \
  --do_train True \
  --model_name_or_path /root/autodl-tmp/Qwen/Qwen2.5-7B-Instruct \
  --preprocessing_num_workers 16 \
  --finetuning_type lora \
  --template qwen \
  --flash_attn auto \
  --dataset_dir data \
  --dataset security \
  --cutoff_len 4096 \
  --learning_rate 5e-05 \
  --num_train_epochs 3.0 \
  --per_device_train_batch_size 1 \
  --gradient_accumulation_steps 8 \
  --lr_scheduler_type cosine \
  --max_grad_norm 1.0 \
  --save_steps 100 \
  --warmup_steps 0 \
  --bf16 True \
  --plot_loss True \
  --lora_rank 8 \
  --lora_alpha 16 \
  --lora_dropout 0 \
  --val_size 0.15 \
  --optim adamw_torch \
  --output_dir /root/autodl-tmp/models/security007
````

> 🧠 建议：使用 `--plot_loss True` 可生成训练损失曲线，便于观察收敛。

---

## 8. 显存消耗估算（VRAM Estimation）

| 项目      | 描述                  | 预估显存         |
| ------- | ------------------- | ------------ |
| 模型权重    | Qwen2.5-7B（BF16 精度） | ≈14 GB       |
| 框架开销    | PyTorch Runtime 缓冲  | ≈1 GB        |
| LoRA 参数 | Rank=8              | ≈0.5 GB      |
| 激活值缓存   | 长度 4096 Token       | ≈10 GB       |
| **总计**  |                     | **≈25.5 GB** |

---

## 9. 显存优化技巧（Optimization Tips）

### 🧩 ① 启用 Liger Kernel

* 融合计算步骤、减少中间缓存。
* 每 1K Token 显存增长由约 2.5GB 降至 0.6GB。

### ⚡ ② 启用 DeepSpeed Stage 3

* 使用 ZeRO 技术切分优化器与梯度状态，
  实现多卡“分布式显存分摊”。
* 推荐在多 GPU 环境下启用。

---

## 10. 参数汇总表（Summary）

| 参数                          | 含义          | 当前设置                |
| --------------------------- | ----------- | ------------------- |
| model                       | 基座模型        | Qwen2.5-7B-Instruct |
| finetuning_type             | 微调方式        | LoRA                |
| learning_rate               | 学习率         | 5e-5                |
| num_train_epochs            | 训练轮数        | 3                   |
| per_device_train_batch_size | 批量大小        | 1                   |
| gradient_accumulation_steps | 梯度累计步数      | 8                   |
| cutoff_len                  | 截断长度        | 4096                |
| lora_rank                   | LoRA 秩      | 8                   |
| val_size                    | 验证集比例       | 0.15                |
| optim                       | 优化器         | AdamW (Torch)       |
| bf16                        | 半精度训练       | True                |
| flash_attn                  | 注意力加速       | Auto                |
| lr_scheduler_type           | 学习率调度策略     | Cosine              |
| plot_loss                   | 可视化 Loss 曲线 | True                |
