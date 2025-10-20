# 🦙 LLaMA Factory 学习笔记

> **参考资料**  
> - [LLaMA Factory（一）](https://mp.weixin.qq.com/s/aQCY8873d09zFIhMhrx7Pg)
> - [LLaMA Factory（二）](https://mp.weixin.qq.com/s/N8LdX3eRuaIJ-yxkpxZJ5w)
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

### 3. 模型量化（Model Quantization）

模型量化与蒸馏同属于 **模型压缩（Model Compression）** 技术。  
对于参数量巨大的模型（如 DeepSeek-R1 6710B、Qwen3 235B 等），直接部署成本极高，因此通常通过量化或蒸馏降低模型复杂度，在 **轻微精度损失** 的情况下显著减少显存与计算需求。

#### 🌐 什么是模型量化？

模型量化是一种通过**降低权重与激活值的数值精度**来减少模型大小、加快推理速度，同时尽量保持准确率的技术。  
简而言之，就是“用更少的比特去存储模型参数”。

> 🎧 类比理解：  
> 从无损音乐（FLAC）到压缩音乐（MP3）  
> - FLAC ≈ FP32 精度模型（完整但大）  
> - MP3 ≈ INT8/INT4 量化模型（更小但略有精度损失）

---

#### 🧮 模型参数与精度单位

模型参数通常以浮点数存储。常见精度格式包括：

| 精度类型 | 示例 | 含义 | 特点 |
|-----------|--------|------|------|
| **FP（Floating Point）** | FP16 / FP32 | 标准浮点数格式 | 高精度，但占用显存多 |
| **BF（Brain Floating Point）** | BF16 | 为深度学习优化的浮点表示 | 精度与效率平衡 |
| **INT（Integer）** | INT8 / INT4 | 整数表示的量化权重 | 极低显存占用，可能损失精度 |

💡 在计算机中：
- FP32：32 位（4 字节）表示一个浮点数  
- FP16：16 位（2 字节）表示一个浮点数  
- INT8：8 位（1 字节）表示一个整数  

---

#### ⚙️ QLoRA 简介

> **QLoRA（Quantized LoRA）**  
> 是一种结合 **LoRA + 4-bit 动态量化** 的创新方法。

通过：
- 4-bit 量化基础模型  
- 双量化 + 训练时反量化机制  

QLoRA 实现了：
- 🚀 显存占用极低  
- ✅ 保留梯度更新精度  
- 💨 训练速度与内存效率兼得  

> 因此，当在 LLaMA Factory 中选择 **量化等级（如 4-bit）** 时，默认使用的微调方法就是 **QLoRA**。

---

### 4. 对话模板（Conversation Template）

对话模板（Conversation Template）是 LLM 与用户交互的 **输入格式化器**。  
不同模型（如 Llama、Qwen、ChatGLM）有不同的输入格式要求。

#### 🧩 作用

1. **训练-推理一致性**：  
   模型训练与使用阶段的输入格式需保持一致，否则性能会显著下降。

2. **上下文管理**：  
   模板帮助组织多轮对话、区分说话者身份（User / Assistant / System）。

3. **高级功能支持**：  
   - 系统提示（System Prompt）  
   - 工具调用（Tool Calling）  
   - 思维链（Chain-of-Thought）  
   - 多模态输入（图像、音频等）

---

#### 🧱 Qwen 对话模板示例

```python
register_template(
    name="qwen",
    format_user=StringFormatter(slots=["<|im_start|>user\n{{content}}<|im_end|>\n<|im_start|>assistant\n"]),
    format_assistant=StringFormatter(slots=["{{content}}<|im_end|>\n"]),
    format_system=StringFormatter(slots=["<|im_start|>system\n{{content}}<|im_end|>\n"]),
    format_function=FunctionFormatter(slots=["{{content}}<|im_end|>\n"], tool_format="qwen"),
    format_observation=StringFormatter(
        slots=["<|im_start|>user\n<tool_response>\n{{content}}\n</tool_response><|im_end|>\n<|im_start|>assistant\n"]
    ),
    format_tools=ToolFormatter(tool_format="qwen"),
    default_system="You are Qwen, created by Alibaba Cloud. You are a helpful assistant.",
    stop_words=["<|im_end|>"],
    replace_eos=True,
)
````

📜 **示例输入输出**

**用户输入：**

> 帮我写一首关于春天的诗

**格式化后：**

```
<|im_start|>system
You are Qwen, created by Alibaba Cloud. You are a helpful assistant.
<|im_end|>

<|im_start|>user
帮我写一首关于春天的诗
<|im_end|>

<|im_start|>assistant
```

模型在 `<|im_start|>assistant` 后开始生成文本，直到 `<|im_end|>` 停止。

---

### 5. 加速方式（Acceleration Methods）

LLaMA Factory 支持多种加速方法：

| 加速方法                 | 说明                                                  | 配置参数                        |
| -------------------- | --------------------------------------------------- | --------------------------- |
| **FlashAttention-2** | 提高注意力计算速度、减少显存占用                                    | `flash_attn: fa2`           |
| **Unsloth**          | 支持 LLaMA、Mistral、Qwen、Phi 等模型；优化 QLoRA/LoRA 微调速度与显存 | `use_unsloth: True`         |
| **Liger Kernel**     | 优化大模型训练吞吐量与显存使用                                     | `enable_liger_kernel: True` |

> ⚙️ **推荐配置**
> 若本地硬件资源有限，可选择 **Unsloth + 4-bit 量化**；
> 若对性能要求一般，可保留默认（FlashAttention）。

---

## 六、数据集（Dataset）

在 **LLaMA Factory** 中主要支持以下两种数据格式：

* 🟦 **Alpaca 格式**（单轮指令数据）
* 🟩 **ShareGPT 格式**（多轮对话数据）

---

### 📁 数据集配置位置

在 `Train` 选项下，LLaMA Factory 提供了两个与数据集相关的配置项：

| 配置项                        | 说明                    |
| :------------------------- | :-------------------- |
| **数据路径（data path）**        | 默认指向项目文件夹下的 `data` 目录 |
| **数据集（dataset_info.json）** | 管理所有数据集的加载与映射配置       |

其基本结构如下：

```json
{
  "数据集名称": {
    "配置项1": "值1",
    "配置项2": "值2"
  }
}
```

---

### 🌐 数据源定义方式

LLaMA Factory 支持 **三种数据源加载方式**：

#### ① 在线数据集

来源平台：**Hugging Face Hub**、**ModelScope Hub**、**OpenMind Hub**

```json
"alpaca_en": {
  "hf_hub_url": "llamafactory/alpaca_en",
  "ms_hub_url": "llamafactory/alpaca_en",
  "om_hub_url": "HaM/alpaca_en"
}
```

#### ② 本地文件

通过相对路径或绝对路径指定本地数据文件：

```json
"alpaca_en_demo": {
  "file_name": "alpaca_en_demo.json"
}
```

#### ③ 自定义脚本生成数据集

通过脚本自动加载或生成数据：

```json
"belle_multiturn": {
  "script_url": "belle_multiturn",
  "formatting": "sharegpt"
}
```

---

### 🧩 数据格式配置

#### （1）基本格式类型

通过 `formatting` 字段指定数据格式：

```json
"slimorca": {
  "hf_hub_url": "Open-Orca/SlimOrca",
  "formatting": "sharegpt"
}
```

#### （2）列映射配置

将原始数据字段映射为标准字段：

```json
"openorca": {
  "hf_hub_url": "Open-Orca/OpenOrca",
  "columns": {
    "prompt": "question",
    "response": "response",
    "system": "system_prompt"
  }
}
```

#### （3）角色标签配置（ShareGPT 格式）

自定义用户与助手的角色标签：

```json
"mllm_demo": {
  "formatting": "sharegpt",
  "tags": {
    "role_tag": "role",
    "content_tag": "content",
    "user_tag": "user",
    "assistant_tag": "assistant"
  }
}
```

#### （4）多模态数据支持

支持文本、图像、视频、音频等数据：

```json
"mllm_demo": {
  "file_name": "mllm_demo.json",
  "formatting": "sharegpt",
  "columns": {
    "messages": "messages",
    "images": "images"
  }
}
```

---

### ⚙️ 特殊训练任务配置

#### ✅ 排序 / 对比学习数据（RLHF / DPO）

```json
"webgpt": {
  "hf_hub_url": "openai/webgpt_comparisons",
  "ranking": true,
  "columns": {
    "prompt": "question",
    "chosen": "answer_0",
    "rejected": "answer_1"
  }
}
```

#### 🧰 工具调用（Tool Calling）数据

```json
"glaive_toolcall_en_demo": {
  "file_name": "glaive_toolcall_en_demo.json",
  "formatting": "sharegpt",
  "columns": {
    "messages": "conversations",
    "tools": "tools"
  }
}
```

---

📄 **延伸阅读**

> 数据集构造与自定义方法详见：[`数据集的构造.md`](./数据集的构造.md)
