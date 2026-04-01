---
name: trade-lead-screening-openclaw
description: Normalize an OpenClaw lead bundle into a conservative lead-screening package with missing-field warnings, manual-review reasons, and customer-intel-ready payloads.
openclaw_role: stage_worker
workspace_owner_skill: trade-active-outreach-combo
single_skill_policy: attach_only
feishu_container_creation: forbidden
requires_master_base: true
requires_master_record: true
---

# 线索整理 / 初筛 Skill for OpenClaw

## Overview

这个变体假设搜索和抓取已经由 OpenClaw 工作流完成。

Python 层只负责：

- 接收线索包
- 统一字段
- 给出初筛提示
- 桥接客户背调输入

## Expected Input

```json
{
  "country_or_market": "Germany",
  "operator_notes": "Use conservative screening.",
  "lead_candidates": [
    {
      "company_name": "Atelier Loom GmbH",
      "company_website": "atelier-loom.de",
      "person_name": "Mira Stein",
      "email": "mira@atelier-loom.de",
      "source_url": "https://atelier-loom.de/about",
      "notes": "Premium table textile positioning."
    }
  ]
}
```

## Feishu Runtime Contract

- 当前角色固定为 `stage_worker`
- 默认只允许附着到 `Trade Lead Workflow Hub`
- 只允许回挂 `Lead Screening Results`
- 不允许独立创建 Base、主表或平行工作容器
- 必须先查 `Lead Workflow Master`

## Output Requirements

- 必须输出标准化字段
- 必须输出缺失项和人工复核原因
- 必须输出推荐下一步动作
- 必须输出客户背调桥接输入和阶段 payload
- 不能继续联网搜客户
- 不能越权创建背调文档或开发信文档
