# 🦙 LLaMA Factory 学习笔记

> **参考资料**  
> - [（一）LLaMA Factory](https://mp.weixin.qq.com/s/aQCY8873d09zFIhMhrx7Pg)
> - [（二）数据集](https://mp.weixin.qq.com/s/N8LdX3eRuaIJ-yxkpxZJ5w)
> - [（三）参数](https://mp.weixin.qq.com/s/AbyWaTaPOp9sr5mz5SOVwg)
> - [（四）LOSS曲线](https://mp.weixin.qq.com/s/6sNGvqLktPk6AP7kPs9JyA)
> - [Bilibili 教程视频](https://www.bilibili.com/video/BV1oTEwzcEeZ?t=0.2)
---

## 一、介绍

### 🧩 1. 全场景模型微调能力

- **多模型支持**：高效适配超 100 个主流模型，涵盖 Qwen、DeepSeek、LLaMA、Gemma、LLaVA、Mistral、Mixtral-MoE、Yi、Baichuan、ChatGLM、Phi 等，满足 NLP、多模态等多领域需求。  
- **多样化算法与精度**：集成 LoRA、GaLore、DoRA 等微调技术，支持（增量）预训练、（多模态）指令监督微调、奖励模型训练、PPO/DPO/KTO/ORPO 等强化学习方法；提供 16 比特全参数微调、冻结微调、LoRA 微调，以及基于 AQLM/AWQ/GPTQ 等技术的 2/3/4/5/6/8 比特 QLoRA 低比特量化微调。  
- **数据集灵活配置**：支持自定义或社区数据集（HuggingFace、ModelScope），自动下载缓存模型与数据集，适配多卡训练。  
- **多种加速技巧**：支持 FlashAttention-2、Unsloth 等高效算子。

### ⚙️ 2. 极简操作与高效工具链

- **零代码 Web UI**：通过可视化界面完成模型配置、数据加载、参数调优、训练监控与日志查看。  
- **实时监控与评估**：集成 Wandb、MLflow、SwanLab、TensorBoard 等工具实现可视化训练与性能分析。

### 🧱 3. 工程化部署与资源利用

- **模型部署**：LoRA 适配器可一键合并为完整模型，便于本地化部署。  
- **推理引擎**：内置 Transformers 与基于 vLLM 的 OpenAI 风格 API。  
- **资源优化**：支持单机多卡与多硬件适配。

---

## 二、LLaMA Factory VS Unsloth

| 对比项 | **LLaMA Factory** | **Unsloth** |
| :-- | :-- | :-- |
| **主要特点** | 全场景微调平台，支持多模型与强化学习 | 高速低显存的微调工具 |
| **性能优化** | 支持多种加速技术与量化微调 | 微调速度提升 2-5 倍，显存占用下降 50%-80% |
| **硬件需求** | 适配多卡与企业级 GPU | 7GB 显存可训练 1.5B 参数模型 |
| **适用场景** | 企业级部署、通用任务、零代码用户 | 个人开发者、低资源场景、快速迭代 |
| **可视化支持** | WebUI、API Server、监控系统 | Colab Notebook 快速上手 |

> ✅ **结论**：  
> - **Unsloth**：适合资源有限、需要快速实验的场景。  
> - **LLaMA Factory**：适合通用大模型训练、企业部署与教学展示。

---

## 三、安装方式

推荐使用 **Conda 虚拟环境** 进行隔离安装。

```bash
# 创建虚拟环境
conda create -n llama_factory python=3.10
conda activate llama_factory

# 安装依赖
pip install -U pip
pip install -U llama-factory
````

---

## 四、WebUI 模块结构

LLaMA Factory 的 WebUI 可分为五大模块：

1. **通用设置**：
   设置语言、模型、微调方式、量化与加速方式。
   Setting.md

3. **微调训练**：

   * 训练阶段（预训练 / 指令微调 / 强化学习）
   * 数据集配置与验证比例 Dataset.md
   * 学习率、轮数、批量大小等关键参数 Parameters.md
   * LoRA、RLHF、优化器参数
   * SwanLab 训练可视化参数

4. **模型评估**：

   * 使用指定数据集对模型进行验证与效果测试。

5. **在线推理**：

   * 支持 HuggingFace、vLLM 等推理引擎，实现模型对话测试。

6. **模型导出**：

   * 可配置导出模型、适配器、量化等级、设备与目录。

---

