# NVH 售前市场调研 Skill

> 输入客户名称 + 目标产品 → 输出该产品异音/NVH 需求画像，支撑售前打单决策。

一个 [Claude Code](https://claude.com/claude-code) Skill（同样适用于其他支持 SKILL.md 规范的 Agent 工具）。

---

## 这个 skill 做什么

输入客户名称和目标产品，自动完成：**客户画像 → 产品异音画像 → 客户反馈挖掘 → 需求匹配与售前建议**，输出结构化的 Markdown + HTML 售前调研报告。

核心机制：

- **异音 9 维度展开**：现象描述 / 来源定位（精确到零件运动副）/ 推测原因（物理机制链）/ 时域 / 频域 / 阶次 / 心理声学 / 严重程度 / 检测手段——每种异音一个完整小节，不是表格带过
- **内置领域知识库**：79 类零部件 × 9 维度的异音知识 + 30 张产品信息卡（`references/nvh-domain-knowledge.md`），先注入再搜索验证
- **传感器布点推导**：从异音来源定位反推点位（P0/P1/P2 分级 + 通道组合策略 + 安装要点），附 9 类产品模板
- **法规窗口期扫描**：GB/EU/UN R51/CQCC 等强制标准时间线，作为客户 NVH 投入的外部驱动力
- **量化价值主张**：技术能力 → 业务语言的翻译表 + Demo→PoC→试用→部署 渐进式合作路径

## 适用人群

- 工业声学AI检测 / NVH 检测方案商的销售、售前、技术工程师
- 需要深入了解目标客户产品 NVH 需求和异音特征的技术人员
- 声学/NVH 工程师：领域知识库本身就可以当异音速查手册用

---

## 安装方法

### Step 1：克隆到 Claude Code 的 skills 目录

```bash
# macOS / Linux
git clone https://github.com/tongzhouAM/nvh-pre-sales-research.git \
  ~/.claude/skills/nvh-pre-sales-research

# Windows (Git Bash)
git clone https://github.com/tongzhouAM/nvh-pre-sales-research.git \
  "/c/Users/$USERNAME/.claude/skills/nvh-pre-sales-research"
```

安装后目录结构：

```
.claude/skills/nvh-pre-sales-research/
├── SKILL.md                              ← 核心方法论（五步调研流程）
├── README.md                             ← 本文件
├── VERSION                               ← 版本信息
├── references/
│   ├── nvh-domain-knowledge.md           ← NVH 领域知识库（79零部件×9维度 + 30产品信息卡）
│   ├── search-strategies.md              ← 搜索策略与数据源清单
│   ├── analysis-platform-capabilities.md ← 分析平台能力-需求匹配矩阵
│   └── sensor-placement-guide.md         ← 接触式传感器点位推荐指南（9 类产品模板）
└── templates/
    └── pre-sales-report-template.html    ← HTML 报告标准模板（corporate-clean）
```

### Step 2：重启 Claude Code，skill 自动加载

### Step 3：验证

对话中输入"帮我做一份售前调研"即可触发。

---

## 使用方法

### 基础调用

```
帮我做一份 {某新能源压缩机厂商} 电动压缩机 的售前调研
```

### 带背景信息的调用

```
帮我调研一下 {某汽车内饰Tier1} 的空调出风口异音检测需求，他们有30个听音工，主要投诉是拨轮步进咔哒声和齿条跳齿声
```

### 触发关键词

售前调研 · 客户调研 · 异音分析 · NVH 需求 · 竞品异响 · 客户投诉分析 · 产品异响调研

---

## 输出产物

每次调研产出 **两份文件**（保存到用户指定目录，未指定时 Claude 会先询问）：

| 文件 | 用途 |
|------|------|
| **Markdown 报告** `{客户简称}{产品简称}-NVH售前调研报告-{日期}.md` | 全量分析正文，可提交内部评审 |
| **HTML 网页报告** `{客户简称}{产品简称}-NVH售前调研报告-{日期}.html` | corporate-clean 风格网页，浏览器打开、分享、投屏、打印 PDF |

报告结构（4 大章）：客户画像（基本面+产业链+NVH成熟度+CQCC对标+决策链）→ 产品异音需求画像（全景表+N种异音×9维度+速查矩阵+传感器布点）→ 客户端反馈与质量信号（投诉+召回+竞品NVH策略拆解+法规驱动）→ 能力匹配与行动建议（能力映射+切入点+Demo建议+合作路径+量化价值）

### 异音 9 维度展开

| 维度 | 说明 |
|------|------|
| 现象描述 | 主观感受、工况条件、用户主观体验 |
| 来源定位 | 精确到零件的具体运动副/界面 |
| 推测原因 | 物理机制链条、根因传导路径 |
| 时域特征 | 波形形态、特征参数、冲击特征 |
| 频域特征 | 特征频率、频谱形态、调制特征 |
| 阶次特征 | 与转速的阶次关系、colormap/waterfall 表现 |
| 心理声学维度 | 响度/尖锐度/粗糙度/波动度/音调度 |
| 严重程度 | 安全/体验/偶发，投诉占比 |
| 检测手段 | 传感器选择、分析节点、采样要求、易踩坑点 |

---

## 与 acoustic-ai-customer-assessment 的分工

| 场景 | 使用 skill |
|------|-----------|
| 判断某公司值不值得打、怎么打 | [acoustic-ai-customer-assessment](https://github.com/tongzhouAM/acoustic-ai-customer-assessment) |
| 深入了解某产品的异音特征和传感器布点 | **本 skill** |
| 先筛选客户，锁定目标后做深度技术方案 | acoustic-ai-customer-assessment → **本 skill**（串联） |

---

## 注意事项

1. **调研结论基于公开信息**：异音分析来自互联网公开数据和领域知识推断，实际异音特征需现场采集验证
2. **传感器点位为理论最优推荐**：所有点位推荐基于振动传递路径理论，部署前必须有阶段 0 验证（便携式采集套装多点位对比采集）
3. **招聘信息不直接展示**：搜索到的招聘信息（薪资/岗位 JD 等）只用于推断客户 NVH 团队能力方向，报告中不出现原始招聘信息
4. **领域知识定期更新**：`references/nvh-domain-knowledge.md` 中的异音类型和产品信息卡建议每季度更新
5. **本仓库为通用化版本**：公司名、产品型号、客户名均已匿名化，分析平台能力映射部分替换为贵方自己的平台能力即可

---

## 版本

**v1.0.0** · 2026-11-17（公开通用版，源自内部 v4.3.2 通用化）

作者：tongzhouAM · MIT License
