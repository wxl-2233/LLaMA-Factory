# 📦 数据集指南（Dataset & Construction Guide）

> 本文档整合了 **LLaMA Factory** 的数据集配置说明与 **Easy Dataset（EDS）** 数据构造指南，涵盖数据加载、格式定义、增强方法与质量控制标准。

---

## 📑 目录

1. [数据集格式与加载（LLaMA Factory）](#一-数据集格式与加载-llama-factory)
2. [数据集构造思路（Easy Dataset）](#二-数据集构造思路-easy-dataset)
3. [数据集质量控制要点](#三-数据集质量控制要点)
4. [数据构造步骤（EDS 实践）](#四-数据构造步骤-eds-实践)
5. [数据集 Review 标准](#五-数据集-review-标准)
6. [数据增强（Data Augmentation）](#六-数据增强-data-augmentation)

---

## 一、数据集格式与加载（LLaMA Factory）

在 **LLaMA Factory** 中主要支持以下两种数据格式：

* 🟦 **Alpaca 格式**（单轮指令数据）
* 🟩 **ShareGPT 格式**（多轮对话数据）

---

### 📁 数据集配置位置

在 `Train` 选项下，LLaMA Factory 提供了两个与数据集相关的配置项：

| 配置项 | 说明 |
| :------ | :------ |
| **数据路径（data path）** | 默认指向项目文件夹下的 `data` 目录 |
| **数据集（dataset_info.json）** | 管理所有数据集的加载与映射配置 |

基本结构如下：

```json
{
  "数据集名称": {
    "配置项1": "值1",
    "配置项2": "值2"
  }
}
````

---

### 🌐 数据源定义方式

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

```json
"alpaca_en_demo": {
  "file_name": "alpaca_en_demo.json"
}
```

#### ③ 自定义脚本生成数据集

```json
"belle_multiturn": {
  "script_url": "belle_multiturn",
  "formatting": "sharegpt"
}
```

---

### 🧩 数据格式配置

#### （1）基本格式类型

```json
"slimorca": {
  "hf_hub_url": "Open-Orca/SlimOrca",
  "formatting": "sharegpt"
}
```

#### （2）列映射配置

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

## 二、数据集构造思路（Easy Dataset）

> 使用 **Easy Dataset（EDS）** 构造高质量的微调数据集，特别适用于 Web 安全、知识问答等垂直领域。

### 🧠 核心原则

* 保证 **相关性**：聚焦任务目标领域；
* 保证 **均衡性**：覆盖不同难度与类型；
* 保证 **高质量**：清洗、去噪、人工审核。

数据来源包括书籍、论文、教师模型蒸馏与专家复审，详见完整流程👇

---

## 三、数据集质量控制要点

| 问题类别        | 说明                              |
| :---------- | :------------------------------ |
| **数据量太小**   | 样本不足导致过拟合，建议 ≥ 1000 条（7B 模型起步）。 |
| **噪声数据多**   | 错别字、重复、乱码或错误标注需清洗。              |
| **样本偏差严重**  | 训练数据与真实应用分布差异大。                 |
| **任务相关性不足** | 数据与目标任务不符。                      |
| **数据多样性不足** | 指令形式单一，缺乏覆盖不同场景的样本。             |

> ✅ **宁缺毋滥：质量优先于数量。**

---

## 四、数据构造步骤（EDS 实践）

1️⃣ **准备原始素材**
→ Web 安全书籍、论文、研究报告、专家问答

2️⃣ **创建任务**
→ 选择模板（Alpaca / ShareGPT 格式）

3️⃣ **清洗与格式统一**
→ 去除冗余、统一字段名（`instruction`、`output`）

4️⃣ **教师模型蒸馏增强**
→ 调用 DeepSeek R1 生成高质量回答与 CoT

5️⃣ **多样性扩充（GA 方法）**
→ 按 Genre + Audience 组合增强样本

6️⃣ **专家复审**
→ 确保事实一致性与任务聚焦性

---

## 五、数据集 Review 标准

| 审查维度      | 要求                             |
| :-------- | :----------------------------- |
| **事实一致性** | 不得出现错误或虚构信息。                   |
| **问答对应性** | 答案需直接回应问题。                     |
| **术语统一性** | 专业术语保持一致。                      |
| **重复合并**  | 同类问题仅保留一份。                     |
| **定量明确化** | 模糊表述需具体化，如“质量较高”→“Kappa=0.85”。 |

---

## 六、数据增强（Data Augmentation）

> 使用 **Massive Genre-Audience (MGA)** 方法，在保持知识一致性的前提下生成多样化变体，从而提升模型泛化能力。

### 🌍 背景

* 高质量数据稀缺
* 重复样本会降低泛化与优化稳定性

### 🧩 MGA 核心思想

通过不同的“体裁（Genre）”与“受众（Audience）”重新表达同一知识内容，实现数据多样性扩展。

### 🧱 实施步骤（EDS 内置）

1. 启用 MGA 模式
2. 自动生成 5 个 GA 组合
3. 文本重构并一致性审查
4. 数据扩展倍增

### 📈 效果

| 模型规模       | 提升任务           | 提升幅度           | 备注   |
| :--------- | :------------- | :------------- | :--- |
| 13B → 130B | 推理任务（GSM8K）    | ↑ 2.03%–15.47% | 泛化增强 |
| -          | 知识任务（MMLU-Pro） | ↑ 2.15%        | 保真度高 |

---

### ✅ 示例（幽默科普型 / 面向中学生）

```json
{
  "genre": "Humorous Science Story",
  "audience": "Tech-Interested Teenagers",
  "question": "为什么 XSS 攻击能绕过常规防护？请用一个日常生活的比喻解释。",
  "answer": "就像有人在你家的邮箱里偷偷放广告，XSS 也是在网页中偷偷塞入恶意脚本。"
}
```
