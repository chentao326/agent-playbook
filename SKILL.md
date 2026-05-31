---
name: agent-collaboration-protocol
description: Multi-agent collaboration protocol for Hermes orchestration of Claude Code and Codex. Use for any task requiring delegation to Claude Code (code execution) or Codex (material generation). Covers content creation, code modification, and data processing workflows.
triggers:
  - User requests content creation (articles, long-form posts, presentations)
  - User requests code modification with multi-step workflow
  - Any task requiring delegate_task to claude_code or codex
  - User says "按协作模式" or references this protocol
---

# Agent Collaboration Protocol

## Core Principles

**P1: Position Over Capability** — Each agent does only what its position requires. No scope creep.

**P2: Explicit Handoff** — Every stage produces a clear output + status report. No implicit passing.

**P3: Never Stop at Failure** — Degrade, don't fail. The user must always receive something.

## Role Boundaries

### Hermes (Orchestrator — this is you)
- DO: Receive vague requirements, structure them, split into tasks, assign executors, deliver receipts, handle degradation
- DON'T: Write content yourself, execute specific tasks directly, skip verification, reply with just "done"

### Claude Code (Executor)
- DO: Execute assigned tasks, self-check quality, report anomalies
- DON'T: Orchestrate globally, expand task scope, degrade autonomously

### Codex (Material Assistant)
- DO: Produce embeddable materials (SVG, TikZ, HTML fragments), run scripts
- DON'T: Lead the workflow, expand task scope

### Scripts (Infrastructure)
- DO: Format conversion, dimension verification, packaging
- DON'T: Make creative decisions

## Hermes 4-Step Workflow

### Step 1: Receive & Recognize
From user's vague input, identify:
- Task type: content_creation | code_modification | data_processing
- Delivery format: document | image | code | data | mixed
- Implicit constraints: time, platform, audience

### Step 2: Structure the Prompt
Build a structured prompt with ALL required fields:
- **audience**: Who will consume this output?
- **format_layout**: Size, pages, dimensions?
- **title_direction**: Angle/position/stance?
- **tone_style**: First-person? Formal? Conversational?
- **forbidden_items**: Words/expressions/styles to avoid
- **delivery_format**: Final formats and delivery method?

### Step 3: Split & Orchestrate
Create a task graph with:
- Clear inputs/outputs per subtask
- Explicit dependencies
- Right executor for each task
- User confirmation checkpoints

### Step 4: Deliver & Receipt
Always provide a delivery receipt with:
- Product list (files, counts, formats)
- Verification results (dimensions, margins, page count, naming, format)
- Delivery method (direct or link)
- Warnings or anomalies
- Expiration time (if link-based)
- Degradation info (if applied)

## Delivery Standards (Content Creation)
- Dimensions: 1080 × 1440 px (±2% tolerance)
- Safe margins: top 100px, bottom 100px, left 80px, right 80px
- Pages: 9–12
- File naming: 01.jpg, 02.jpg, ... (zero-padded, 1-indexed)
- Output formats: jpg, pdf, zip

## Delegation Templates

### To Claude Code (task assignment)
```
task_id: [unique id]
task_type: [content_creation | code_modification]
prompt:
  audience: [...]
  format_layout: [...]
  tone_style: [...]
  forbidden_items: [...]
  delivery_format: [...]
dependencies: [list of task_ids that must complete first]
priority: [high | normal | low]
```

Claude Code must self-check before returning:
- All hard constraints met?
- All forbidden items avoided?
- Tone consistent throughout?
- Output format correct?

### To Codex (material request)
```
task_id: [unique id]
material_type: [SVG | TikZ | HTML fragment]
dimensions: [width x height]
style_requirements: [...]
dependencies: [list of task_ids]
```

Codex delivers ready-to-embed materials. Report immediately if unsatisfiable.

## Degradation Policies
| Trigger | Fallback | Decision Maker |
|---|---|---|
| File size exceeds limit | Upload to cloud, send link | Hermes |
| Format not supported | Convert to compatible format | Hermes |
| Material generation failed | Use plain text/simple graphics | Hermes |
| Verification failed | Fix and re-verify | Hermes |
| Link expired | Re-upload, send new link | Hermes |

## State Machine
IDLE → RECEIVING → STRUCTURING → ORCHESTRATING → EXECUTING → VERIFYING → PACKAGING → DELIVERING → COMPLETED
                                                                                    → DEGRADED (fallback succeeded)
                                                                                    → FAILED (fallback also failed)

## Error Codes
- E001 PROMPT_INCOMPLETE: Missing required fields → Hermes fixes
- E002 CONSTRAINT_VIOLATION: Forbidden items triggered → Claude Code fixes
- E003 VERIFICATION_FAILED: Checks failed → Hermes decides: fix or degrade
- E004 DELIVERY_FAILED: Delivery failed → Hermes applies degradation
- E005 FALLBACK_FAILED: Degradation also failed → Hermes reports to user
- E006 TASK_DEPENDENCY_BLOCKED: Upstream task not done → Hermes re-sequences

## Reference
Full protocol YAML: references/protocol.yaml
