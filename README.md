# Silicon Manual

**AI-native Semiconductor Technical Documentation Framework**

中文名：**芯片手册智能解析与知识表示框架**

Silicon Manual 旨在解决嵌入式开发中 Datasheet、Reference Manual、Programming Manual、Application Note 等长篇技术文档难以被 AI 高质量理解和检索的问题。

项目不以“PDF 转 Markdown”为目标，而是建立一套面向芯片技术手册的 **标准化输入 → 视觉解析 → 结构化技术表示（RI / SMIR）→ 证据追溯 → 校验 → AI 应用** 的完整链路。

---

## 1. 项目目标

传统 PDF 对人类工程师仍然可读，但直接将几百页、几千页 PDF 交给 AI 往往存在以下问题：

- 上下文过大，Token 消耗高
- 表格、寄存器位图、时序图、状态图等视觉结构容易丢失
- PDF 文本抽取后，页面结构和语义关系可能被破坏
- AI 输出难以准确回到原始手册位置
- 手工截图存在尺寸、缩放、分辨率、页码缺失等不一致问题

Silicon Manual 的核心目标是：

> **让 AI 读取“标准化的技术页面”和“结构化的技术语义”，而不是直接面对未经处理的长篇 PDF。**

同时保证：

> **AI 得出的任何重要技术信息，都尽可能能够追溯到原始 PDF 页面和具体区域。**

---

## 2. 核心理念

项目不追求 PDF → Markdown。

目标是建立一种面向芯片技术手册的 **Technical Documentation IR（Intermediate Representation）**。

可以暂称：

**SMIR — Silicon Manual Intermediate Representation**

整体链路：

```text
PDF
 │
 ▼
Page Normalization
 │
 ├── Standardized Image
 ├── Page Metadata
 └── Coordinate System
 │
 ▼
Vision AI Extraction
 │
 ▼
SMIR / RI
 │
 ├── Register
 ├── Field
 ├── Table
 ├── Figure
 ├── Timing
 ├── State Machine
 ├── Procedure
 ├── Pin / Signal
 ├── Memory Map
 ├── Note / Warning
 └── Other Technical Objects
 │
 ▼
Validation / Review
 │
 ▼
AI / RAG / Code Generation / IDE Integration
```

---

## 3. 四层语言模型

结构化结果同时服务于人类工程师和 AI / 程序，因此语言不应混为一谈。

```text
English Identifier = Identity
Original           = Evidence
中文                = Understanding
Meaning            = Interpretation
```

### English Identifier

用于机器、代码和稳定引用：

- Register name
- Field name
- Bit name
- Pin name
- Signal name
- Peripheral name
- Interrupt name
- Macro / Function / Enum
- Address / Constant

例如：

```text
GPIOx_MODER
MODER0
RCC_AHB1ENR
GPIOAEN
TIM2_CH1
```

### Original

保留手册原始文本，是最重要的事实证据。

### 中文

用于工程师阅读和理解。

### Meaning

只在需要时提供明确标记的工程语义解释，不与原文或翻译混合。

---

## 4. 为什么要标准化 PDF 页面

人工截图不是可靠的输入协议。

不同截图可能存在：

- 分辨率不同
- DPI 不同
- 页面缩放不同
- 裁剪范围不同
- 图片尺寸不同
- 页码不在截图中
- 同一 PDF 的坐标体系无法统一

因此项目首先建立统一的 **Page Artifact**。

```text
PDF
 ↓
Renderer
 ↓
page-000145.png
page-000145.json
```

每个 Page Artifact 至少包含：

- document_id
- page_id
- PDF page number
- PDF page label（如存在）
- PDF page size
- rendering DPI
- image width / height
- PDF coordinate system
- image coordinate system
- 原始页面状态

重要原则：

> **page 信息由 PDF 预处理工具确定，而不是依赖 AI 从图片中读取。**

> **长期证据定位优先使用 PDF 原生坐标，而不是图片像素坐标。**

这样即使未来换 DPI、换视觉模型，也不会破坏已有的证据定位。

---

## 5. AI 视觉解析原则

AI 不是 OCR 工具，也不是摘要工具。

处理技术手册页面时，应：

1. 先理解视觉结构
2. 再读取文字
3. 恢复对象和对象关系
4. 保留所有工程约束
5. 对不确定信息明确标记
6. 不根据常识或上下文自行补全

必须重点保留：

- 地址
- bit number
- field boundary
- reset value
- access type
- 单位
- 条件
- 例外
- Reserved / Undefined / Don't care
- Note / Warning / Caution
- 图表结构
- 脚注
- 引用关系

---

## 6. RI / SMIR

RI（Representation / Intermediate Representation）是项目的核心数据层。

它不应只是 Markdown，也不应只是 OCR 文本，而应表达技术文档的语义对象和关系。

示例：

```text
@REGISTER name=GPIOx_MODER offset=0x00 reset=0xA8000000
  original: "GPIO port mode register"
  zh: "GPIO 端口模式寄存器"

  @FIELD name=MODER0 bits=1:0 access=RW reset=0x0
    original: "Port x configuration bits 0"
    zh: "端口 x 的配置位 0"

  @SOURCE
    document: STM32-Reference-Manual
    page: 526
    region_pdf: [x1, y1, x2, y2]
```

RI 的核心属性：

- 结构化
- 可检索
- 可验证
- 可追溯
- 可生成代码
- 与具体 AI 模型解耦

---

## 7. Evidence / Source

每个重要技术对象都应该尽可能拥有来源证据。

推荐至少维护：

```text
source.document_id
source.page_id
source.pdf_page_number
source.region_pdf
source.region_image
```

证据系统的目标是支持：

```text
AI Answer
    ↓
RI Object
    ↓
Source Evidence
    ↓
PDF Page
    ↓
Original Region Highlight
```

工程师可以快速判断：

> AI 是真的从手册中读到了这个信息，还是自己推出来的？

---

## 8. 推荐仓库结构

第一阶段保持简单，不提前建设所有未来功能。

```text
silicon-manual/
│
├── README.md
├── pyproject.toml
│
├── docs/
│   ├── architecture.md
│   └── roadmap.md
│
├── prompts/
│   ├── extraction.md
│   └── task.md
│
├── schemas/
│   ├── ri.md
│   └── examples/
│
├── tools/
│   └── pdf-normalizer/
│       ├── src/
│       └── tests/
│
├── examples/
│   └── demo/
│
└── tests/
```

随着项目成熟，可以进一步扩展：

```text
prompts/validation/
prompts/qa/
tools/page-inspector/
tools/ri-validator/
core/
datasets/
benchmarks/
experiments/
scripts/
```

---

## 9. 第一阶段 MVP

第一阶段不要同时解决 RAG、代码生成、IDE 插件和 GUI。

只验证下面这条链路：

```text
PDF
 ↓
标准化 Page
 ↓
Page Metadata
 ↓
Vision AI
 ↓
SMIR / RI
 ↓
人工审查
 ↓
回到 PDF 原位置
```

### PDF Normalizer

第一版建议：

- Python
- PyMuPDF
- Pydantic
- JSON
- JSON Schema

工具目标：

```bash
silicon-manual render manual.pdf
```

生成：

```text
output/
├── manifest.json
├── pages/
│   ├── page-000001.png
│   ├── page-000002.png
│   └── ...
└── metadata/
    ├── page-000001.json
    ├── page-000002.json
    └── ...
```

第一版重点验证：

- 固定渲染参数
- PDF 页面编号与 page_id
- PDF page label
- 页面尺寸
- PDF 坐标 → 图片坐标转换
- manifest 设计
- 页面图像质量
- 多页处理
- 旋转页面处理
- 页面来源追溯

---

## 10. Prompt 体系

Prompt 不应只有一个。

建议最终形成：

```text
prompts/
├── extraction/
│   ├── system.md
│   └── task.md
│
├── validation/
│   ├── visual-review.md
│   └── consistency-check.md
│
└── qa/
    └── manual-qa.md
```

其中：

### Extraction

图片 → RI

### Validation

原图 + RI → 检查错误

重点检查：

- Register address
- Offset
- Bit range
- Reset value
- Access
- Numeric values
- Units
- Table cells
- Figure relationships

### QA

RI → 工程师可用的技术问答

---

## 11. 设计原则

### 11.1 不丢失原始信息

原文不是摘要素材，而是事实证据。

### 11.2 不让 AI 猜

无法确认时使用 `UNCERTAIN`。

### 11.3 结构优先于排版

保存语义关系，而不是复刻 PDF 视觉排版。

### 11.4 Evidence First

重要数据必须可以回溯来源。

### 11.5 Model Agnostic

RI 不绑定 GPT、Claude、Gemini 或其他具体模型。

### 11.6 Human Auditable

最终结果必须方便嵌入式工程师审查。

### 11.7 Coding Friendly

identifier、address、bits、access、enum 等数据应能够直接用于后处理和代码生成。

---

## 12. 技术路线

当前推荐：

```text
Python MVP
 ↓
真实芯片手册验证
 ↓
RI / Evidence 协议稳定
 ↓
性能与部署需求评估
 ↓
必要时 Rust 化核心组件
```

Rust 可以作为后续实现方向，尤其适合：

- 高性能批量 PDF 处理
- 跨平台单文件发布
- 更严格的核心数据模型
- 长期产品化

但当前阶段优先验证协议和数据模型，而不是提前优化实现语言。

---

## 13. 当前已有设计文档

项目的设计资料目前位于资料库 **“芯片手册新框架”** 中，包括：

- 双语技术手册视觉解析 Prompt
- 单次图片解析任务模板
- PDF 页面标准化与证据定位设计
- Harness 实现需求：PDF 页面标准化工具

这些文档应作为仓库 `prompts/`、`docs/` 和 `tools/` 的初始设计输入，后续以 Git 仓库版本为准进行迭代。

---

## 14. Roadmap

### Phase 1 — Page Normalization

完成稳定的 PDF → Page Artifact 流程。

### Phase 2 — Vision Extraction

验证视觉模型从标准页面提取 SMIR 的效果。

### Phase 3 — Validation

建立原图 ↔ RI 的双向校验机制。

### Phase 4 — Knowledge Retrieval

基于 RI 构建更低 Token、更高精度的检索和问答。

### Phase 5 — Code Generation

从 RI 生成：

- Register definitions
- C / C++ access code
- Rust bindings
- CMSIS-SVD 等结构化产物

### Phase 6 — Engineering Integration

探索：

- IDE 集成
- 局部证据跳转
- Register / Field 查询
- Source Highlight
- 技术手册智能导航

---

## 15. 最终愿景

Silicon Manual 最终希望建立的不是一个 PDF 转换器，而是一套：

> **让芯片技术手册成为 AI 可以可靠理解、工程师可以审查、程序可以直接使用的结构化技术知识。**

核心链路：

```text
Human Manual
     ↓
Standardized Page
     ↓
Vision Understanding
     ↓
Semantic IR
     ↓
Evidence
     ↓
Validation
     ↓
AI / RAG / Code / IDE
```
