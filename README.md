# Agent Collaboration Protocol · 多 Agent 协作工作流协议

> **差别不在能力，在位置。**
> **The difference is not in capability, but in position.**

---

## 这是什么 · What This Is

一套结构化的多 Agent 协作指令协议，定义了 **Hermes（编排者）、Claude Code（执行者）、Codex（素材副手）、Scripts（工具链）** 四个角色在复杂任务中的协作方式。

A structured instruction protocol for multi-agent collaboration, defining how **Hermes (Orchestrator), Claude Code (Executor), Codex (Material Assistant), and Scripts (Infrastructure)** work together on complex tasks.

**核心洞察 · Core Insight:** 所有 Agent 能力相近，差别在位置。每个角色只做自己该做的事——不越位、不沉默、不省略、可追溯。

All agents have similar capabilities; the difference is their position in the workflow. Each role does only what it should — no scope creep, no silence, no omission, full traceability.

---

## 仓库内容 · Repository Contents

| 文件 | 类型 | 用途 |
|------|------|------|
| `protocol.yaml` | YAML 机器协议 | 可被机器解析的形式化协议——角色定义、状态机、消息格式、错误代码 |
| `guide.md` | Markdown 手册 | 人类阅读的完整协作指导——SOP、检查清单、降级策略、全景图 |
| `SKILL.md` | Hermes Skill | 可直接导入 Hermes Agent 的技能文件，加载后自动启用协作模式 |

| File | Type | Purpose |
|------|------|------|
| `protocol.yaml` | YAML (machine) | Machine-parseable formal protocol — roles, state machine, message formats, error codes |
| `guide.md` | Markdown (human) | Human-readable complete guide — SOP, checklists, degradation policies, workflow diagram |
| `SKILL.md` | Hermes Skill | Drop-in skill file for Hermes Agent — load to auto-enable collaboration mode |

---

## 角色体系 · Role Architecture

```
用户输入 → Hermes（编排） → Claude Code / Codex / Scripts（执行） → 产物 → Hermes（收尾） → 用户
User Input → Hermes → Claude Code / Codex / Scripts → Artifacts → Hermes → User
```

### Hermes · 编排者 (Orchestrator)
**位置:** 用户 ↔ 执行工具之间  
**职责:** 接收模糊需求 → 结构化 prompt → 拆分任务 → 分配执行者 → 收尾回执 → 降级决策  
**禁止:** 抢写内容、自行执行、省略校验、只回「已完成」

### Claude Code · 执行者 (Executor)
**位置:** 项目仓库内部  
**职责:** 执行具体任务 → 控制质量 → 自检 → 报告异常  
**禁止:** 编排全局、自行降级、扩展任务范围

### Codex · 素材副手 (Material Assistant)
**位置:** 按需出场  
**职责:** 生产素材（SVG/TikZ/HTML） → 跑脚本 → 交付即用素材  
**禁止:** 主导流程、扩展任务范围

### Scripts · 工具链 (Infrastructure)
**位置:** 管线末端  
**职责:** 格式转换 → 尺寸校验 → 页序校验 → 打包 → 体积检查  
**禁止:** 做创作决策

---

## 核心原则 · Core Principles

| ID | 原则 Principle | 说明 |
|----|---------------|------|
| P1 | 位置大于能力 · Position Over Capability | 每个 Agent 做好自己位置的事 |
| P2 | 显式交接 · Explicit Handoff | 每个环节必须明确交接（产物 + 状态报告） |
| P3 | 永不因失败停止 · Never Stop at Failure | 降级不是承认失败，是确保用户拿到产物 |

---

## 工作流全景 · Workflow Overview

```
┌─────────────┐
│   用户输入    │ 「帮我做一篇小红书长文」
└──────┬──────┘
       ▼
┌─────────────┐
│   Hermes     │ 1. 识别需求 2. 结构化 prompt 3. 拆分编排
└──┬──┬──┬───┘
   │  │  │
   ▼  │  │
┌──────────┐  │  │
│Claude Code│  │  │  起草正文 → 用户确认 → 排版分页
└──────────┘  │  │
   │          ▼  │
   │    ┌──────┐ │
   │    │Codex │ │  SVG/图表素材（按需）
   │    └──────┘ │
   │       │     │
   └───────┴─────┘
           ▼
    ┌──────────┐
    │ Scripts  │  导出 JPG/PDF → 校验尺寸 → 打包 ZIP
    └────┬─────┘
         ▼
    ┌──────────┐
    │  Hermes  │  审查校验 → 交付回执（产物+校验+异常+降级）
    └────┬─────┘
         ▼
    ┌──────────┐
    │   用户    │  收到 pages_jpg/ + PDF + ZIP（或下载链接）
    └──────────┘
```

---

## 状态机 · State Machine

```
IDLE → RECEIVING → STRUCTURING → ORCHESTRATING → EXECUTING
                                                    ↓
                                               VERIFYING
                                               ↓        ↑ (retry)
                                         PACKAGING      │
                                              ↓          │
                                         DELIVERING ─────┘
                                         ↓        ↘
                                    COMPLETED    DEGRADED → FAILED
```

---

## 降级策略 · Degradation

| 失败场景 | 降级方案 |
|----------|----------|
| 文件过大 | 上传云端 + 下载链接 |
| 格式不支持 | 转换为兼容格式 |
| 素材生成失败 | 纯文本/简单图形替代 |
| 校验失败 | 修复后重新校验 |
| 链接过期 | 重新上传告知新链接 |

**降级必须由 Hermes 决策，回执中必须注明原因和方案。**

---

## 导入 Hermes Agent · Import to Hermes

```bash
# 将 SKILL.md 复制到 Hermes skills 目录
cp SKILL.md ~/.hermes/skills/agent-collaboration-protocol/SKILL.md

# 或者在 Hermes 中直接：
# 「加载 agent-collaboration-protocol skill」
```

---

## 交付标准 · Delivery Standards

- 尺寸: 1080 × 1440 px (±2%)
- 安全边距: 上 100px, 下 100px, 左 80px, 右 80px
- 页数: 9–12
- 命名: 01.jpg, 02.jpg, ... (零填充，1 起始)
- 格式: JPG + PDF + ZIP

---

## License

MIT
