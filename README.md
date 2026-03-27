# Trade Lead Screening

把零散搜索结果整理成可继续进入客户背调的结构化线索池。

An open-source Codex skill for normalizing and screening scattered lead clues into a conservative, customer-intel-ready lead pool.

当前状态：可交付

## 这个仓库适合谁

- 已经搜到一批公司名、邮箱、网址、联系人，但信息很乱的人
- 想先整理线索再进入客户背调的人
- 想把搜索结果标准化成可复用 JSON 的团队
- 想搭建 `线索整理 -> 客户背调 -> 开发信` 主动开发链路的人

## Why This Exists

外贸主动开发里，很多时间不是花在“找不到客户”，而是花在“搜到一堆结果却没法继续判断”。

这个 Skill 专门解决这个中间层问题：

- 统一字段
- 标记缺失项
- 提示人工复核点
- 给出下一步动作建议
- 生成兼容客户背调 Skill 的标准输入

## What It Produces

输入可以是一批很稀疏的线索，例如：

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
    },
    {
      "company_name": "",
      "company_website": "",
      "person_name": "",
      "email": "hello@gmail.com",
      "source_url": "https://marketplace.example/nordhaus"
    }
  ]
}
```

输出默认包含：

- `summary`
- 标准化后的 `leads`
- `missing_fields`
- `manual_review_reasons`
- `recommended_next_action`
- `customer_intel_input`

## Recommended Workflow

1. 把搜索阶段得到的候选线索整理成 JSON
2. 运行本 Skill 做字段规范化和初筛
3. 从结果中挑出：
   - `enter_customer_intel`
   - 或人工确认后的 `enrich_then_customer_intel`
4. 送入客户背调 Skill
5. 背调完成后，再进入开发信 Skill

## Chain Position

这个 Skill 当前处于主动开发链路中间层：

- 上游：`客户搜索 / 线索发现`
- 下游：客户背调 Skill
- 后续可继续承接：开发信 Skill、跟进优先级 Skill

推荐链路：

`线索整理skill -> 客户背调skill -> 开发信skill`

关联仓库：

- 客户背调 Skill: [trade-customer-intel](https://github.com/FloydTang/trade-customer-intel)
- 开发信 Skill: [trade-outreach-email](https://github.com/FloydTang/trade-outreach-email)

## Current Scope

- 首版先聚焦“线索整理 + 初筛提示”
- 先不做复杂 CRM、提醒系统或长期数据库
- 先保证输出能稳定进入客户背调 Skill

## Repository Structure

```text
.
├── README.md
├── SKILL.md
├── 立项方案.md
├── 验收记录.md
├── scripts/
│   ├── build_lead_screening_report.py
│   ├── build_customer_intel_batch_input.py
│   ├── run_regression_checks.py
│   └── run_pre_release_gate.py
├── examples/
├── references/
├── schemas/
└── for-openclaw/
```

## Verification Status

当前已完成的验证：

- 固定样例输入输出已生成
- `run_regression_checks.py` 已通过
- `run_pre_release_gate.py` 已通过
- OpenClaw 最小样例已通过

当前边界：

- 这是线索整理与初筛工具，不替代客户背调
- 初筛结果只作辅助，不直接判断客户价值高低
- 与客户背调、开发信的字段衔接已打通，但整条链路仍建议按业务场景继续做集成验证

## Quick Start

```bash
python3 ./scripts/build_lead_screening_report.py \
  --input-json ./examples/sample-leads.json \
  --markdown-out /tmp/lead-screening.md \
  --json-out /tmp/lead-screening.json
```

```bash
python3 ./scripts/build_customer_intel_batch_input.py \
  --input-json ./examples/sample-output.json \
  --json-out /tmp/customer-intel-batch.json
```

```bash
python3 ./scripts/run_regression_checks.py
```

```bash
python3 ./scripts/run_pre_release_gate.py
```

## Feishu / OpenClaw Stage Export

如果你要把这个 Skill 的初筛结果直接接入飞书主表或初筛结果表，可在生成主输出后再运行：

```bash
python3 ./scripts/build_feishu_stage_payload.py \
  --input-json ./examples/sample-output.json \
  --combo-run-id demo-run
```

这个脚本不会重新做线索整理判断。

它只负责把已有的 screening 输出转换成 OpenClaw 可消费的阶段 payload，用于：

- 写入 `Lead Screening Results`
- 回写 `Lead Workflow Master`
- 让后续客户背调或单点使用时复用同一条主记录

## Release Process

发布前固定执行：

1. `python3 ./scripts/run_pre_release_gate.py`
2. 如有规则或模板改动，重新生成受影响的 `examples/*-output.md` 和 `examples/*-output.json`
3. 确认 `README.md`、`验收记录.md` 和 `for-openclaw/README.md` 没有状态漂移

## OpenClaw Variant

`for-openclaw/` 是这个 Skill 的 OpenClaw-native 包装版本：

- 保留当前本地版的保守整理原则
- 假设上游搜索结果已经由 OpenClaw 工作流整理成线索包
- Python 包装脚本只负责字段规范化、初筛提示和下游背调桥接字段生成

## License

Released under the MIT License. See [LICENSE](./LICENSE).

## 作者

半斤九两科技
