# 🦙 LLaMA Factory 学习笔记

> **参考资料**  
> - [LLaMA Factory 详解（公众号）](https://mp.weixin.qq.com/s/aQCY8873d09zFIhMhrx7Pg)  
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

2. **微调训练**：

   * 训练阶段（预训练 / 指令微调 / 强化学习）
   * 数据集配置与验证比例
   * 学习率、轮数、批量大小等关键参数
   * LoRA、RLHF、优化器参数
   * SwanLab 训练可视化参数

3. **模型评估**：

   * 使用指定数据集对模型进行验证与效果测试。

4. **在线推理**：

   * 支持 HuggingFace、vLLM 等推理引擎，实现模型对话测试。

5. **模型导出**：

   * 可配置导出模型、适配器、量化等级、设备与目录。

---

## 五、微调通用设置

### 🔍 1. 选择模型

| 分类      | 标识          | 含义         | 示例                             |
| :------ | :---------- | :--------- | :----------------------------- |
| 功能与任务类型 | Base        | 基础模型（原始能力） | Qwen3-14B-Base                 |
|         | Chat        | 对话优化模型     | DeepSeek-LLM-7B-Chat           |
|         | Instruct    | 指令微调模型     | Qwen3-0.6B-Instruct            |
|         | Distill     | 知识蒸馏模型     | DeepSeek-R1-1.5B-Distill       |
|         | Math        | 数学推理模型     | DeepSeek-Math-7B-Instruct      |
|         | Coder       | 编程任务模型     | DeepSeek-Coder-V2-16B          |
| 多模态     | VL          | 视觉-语言模型    | Kimi-VL-A3B-Instruct           |
|         | Video       | 视频多模态模型    | LLaVA-NeXT-Video-7B-Chat       |
|         | Audio       | 音频输入模型     | Qwen2-Audio-7B                 |
| 技术特性    | Int8 / Int4 | 权重量化模型     | Qwen2-VL-2B-Instruct-GPTQ-Int8 |
|         | AWQ / GPTQ  | 特定量化技术     | Qwen2.5-VL-72B-Instruct-AWQ    |
|         | MoE         | 混合专家模型     | DeepSeek-MoE-16B-Chat          |
|         | RL          | 强化学习优化模型   | MiMo-7B-Instruct-RL            |
| 版本      | v0.1 / v0.2 | 模型版本号      | Mistral-7B-v0.1                |
| 变体      | Pure        | 纯净版模型      | Index-1.9B-Base                |
|         | Character   | 角色对话模型     | Index-1.9B-Character-Chat      |
|         | Long-Chat   | 长上下文模型     | Orion-14B-Long-Chat            |
| 应用领域    | RAG         | 检索增强生成模型   | Orion-14B-RAG-Chat             |
|         | Chinese     | 中文优化模型     | Llama-3-70B-Chinese-Chat       |
|         | MT          | 翻译模型       | BLOOMZ-7B1-mt                  |

---

### ⚙️ 2. 微调方法

#### 🧠 **Full（全参数微调）**

* **概念**：训练全部参数，最大化性能。
* **类比**：拆掉房子重建。
* **优点**：性能最强、完全适应性高。
* **缺点**：显存与计算量需求极高。
* **适用场景**：有充足算力、数据量大、任务差异大。

---

#### 🧩 **Freeze（参数冻结微调）**

* **概念**：冻结部分参数，仅训练部分层。
* **类比**：给房子局部装修。
* **优点**：计算效率高、防止过拟合、节省内存。
* **缺点**：性能可能略低、需专业调参。
* **适用场景**：资源有限、小数据集、任务相似。

---

#### 🔗 **LoRA（低秩适配微调）**

* **概念**：通过注入小型可训练矩阵实现高效微调。
* **类比**：给房子加智能模块。
* **优点**：

  * 训练参数减少 99%+
  * 显存占用低、速度快
  * 支持多任务适配与轻量化部署
* **缺点**：

  * 性能上限略低
  * 超参数调节复杂
* **适用场景**：

  * GPU 资源有限
  * 需快速适配新任务
  * 多模态任务与迭代优化场景

> 💡 **LoRA 微调是当前主流方法**：
> 在保持预训练权重不变的情况下，LoRA 通过低秩矩阵学习特定任务特征，实现快速收敛、低显存占用与良好泛化性能。

---

