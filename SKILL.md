---
name: trade-lead-screening
description: Normalize and screen scattered foreign-trade lead clues into a conservative lead pool with missing-field warnings, manual-review reasons, and customer-intel-ready payloads.
---

# 线索整理 / 初筛 Skill

## Overview

用这个 Skill 把搜索阶段得到的零散客户名单、网址、联系人线索和备注，整理成可继续进入客户背调的标准输入。

角色定位：

- `线索初筛员`
- 负责字段统一、缺失识别、初步分类和下一步建议
- 不负责公开网页深度背调
- 不负责最终邮件文案输出

## Chain Role

- 在总链路中固定作为 `stage_worker`
- 默认单节点策略：`attach_only`
- 默认不独立声明飞书工作容器
- 所有数据最终统一挂到 `Trade Lead Workflow Hub`

## Agent-First Installation Notes

这个仓库默认提供两层说明：

- 公开层：根目录 `README.md`，保证最小可用
- 增强层：`for-openclaw/README.md` 和 `references/00-单节点增强执行词.md`

如果你要在龙虾里使用这个节点，优先复制增强执行词给龙虾，而不是先看教程型长文。

## Standard Input

输入统一为 JSON：

```json
{
  "default_country_or_market": "Germany",
  "operator_notes": "Use conservative screening.",
  "leads": [
    {
      "company_name": "Atelier Loom GmbH",
      "company_website": "atelier-loom.de",
      "person_name": "Mira Stein",
      "email": "mira@atelier-loom.de",
      "source_url": "https://atelier-loom.de/about"
    }
  ]
}
```

## Workflow

1. Normalize raw lead clues into the standard screening shape.
2. Identify missing fields and conflicting clues.
3. Produce manual-review reasons and a conservative next action.
4. Build `customer_intel_input` payloads for leads that can continue downstream.

## Output Requirements

- 必须输出标准化字段结果
- 必须输出缺失项
- 必须输出人工复核原因
- 必须输出推荐下一步动作
- 必须输出客户背调桥接输入
- 不能越权替代 `trade-customer-intel` 做证据驱动背调
- 不能越权替代 `trade-outreach-email` 生成邮件草稿

## Main Scripts

- [build_lead_screening_report.py](./scripts/build_lead_screening_report.py)
- [build_customer_intel_batch_input.py](./scripts/build_customer_intel_batch_input.py)

### Example

```bash
python3 ./scripts/build_lead_screening_report.py --input-json ./examples/sample-leads.json
```

```bash
python3 ./scripts/build_customer_intel_batch_input.py --input-json ./examples/sample-output.json
```

```bash
python3 ./scripts/run_regression_checks.py
```

## Defaults

- 首版不继续联网搜索
- 首版优先保守整理和桥接
- 首版输出优先衔接客户背调 Skill
- OpenClaw 单节点默认只 attach，不单独建表

## References

- [00-单节点增强执行词.md](./references/00-单节点增强执行词.md)
- [for-openclaw/README.md](./for-openclaw/README.md)
- [for-openclaw/SKILL.md](./for-openclaw/SKILL.md)
