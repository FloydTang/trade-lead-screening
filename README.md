# Trade Lead Screening

把零散搜索结果整理成可继续进入客户背调的结构化线索池。

An open-source Codex skill for normalizing and screening scattered lead clues into a conservative, customer-intel-ready lead pool.

当前状态：可交付

角色定位：`线索初筛员`

这个仓库的开源内容本身即可独立使用，飞书增强入口不是必需安装步骤，而是增强体验选项。

链路角色：

- 在总链路里是 `stage_worker`
- 组合包 / 主代理是 `workflow_owner`
- 单节点默认 `attach_only`
- `feishu_container_creation = forbidden`
- 单节点不独立声明飞书工作容器
- 所有数据最终统一挂到同一个 `Trade Lead Workflow Hub`

上下游关系：

- 上游：[trade-lead-discovery](https://github.com/FloydTang/trade-lead-discovery) 或人工整理线索
- 下游：[trade-customer-intel](https://github.com/FloydTang/trade-customer-intel)

## 公开最小可用说明

这个仓库公开层只解决一个问题：

- 把零散候选线索整理成可继续进入客户背调的标准输入
- 和组合包一样，这个单节点仓库本身就拥有可独立执行的最小功能

## 两种权益

这个 Skill 当前分成两种使用权益：

### 1. 开源权益

- 直接使用当前 GitHub 仓库里的开源内容
- 即插即用，适合先跑通最小版本
- 适合自己阅读 README、运行脚本、替换样例和继续二次改造

### 2. 增强权益

- 在开源最小能力基础上，额外使用半斤九两科技提供的飞书增强执行词
- 更适合龙虾 / OpenClaw 安装和执行
- 使用体验会更精致、更完整，理解障碍和安装试错更少
- 更容易和统一的 `Trade Lead Workflow Hub` 挂接

当前最小能力：

- 统一字段
- 标记缺失项
- 提示人工复核点
- 给出下一步动作建议
- 生成兼容 `trade-customer-intel` 的标准输入

## 飞书增强入口

这个 Skill 的开源版本身就可以单独使用，并能完成当前节点的最小可用功能。

如果你希望在龙虾 / OpenClaw 中获得更精致、更完整的使用体验，建议按下面流程复制增强执行词：

- [飞书增强入口：复制增强执行词给龙虾](https://evenbetter.feishu.cn/wiki/ADmiwiultihx6Yk1p2UcjfmVn6d)

如果链接打不开，请先确认使用和半斤九两科技会员群绑定的飞书账号登录。

如果你暂时还没有绑定过，或当前还没有半斤九两科技的账号，请访问：[evenbetter.tech](https://evenbetter.tech)

仓库内对应的源码基线在：

- `references/00-单节点增强执行词.md`
- `for-openclaw/README.md`
- `for-openclaw/SKILL.md`

## 推荐模型

- `coze/glm-4-7-251222`

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

## Chain Position

推荐链路：

`trade-lead-discovery -> trade-lead-screening -> trade-customer-intel -> trade-outreach-email`

关联仓库：

- 客户搜索 Skill: [trade-lead-discovery](https://github.com/FloydTang/trade-lead-discovery)
- 客户背调 Skill: [trade-customer-intel](https://github.com/FloydTang/trade-customer-intel)
- 开发信 Skill: [trade-outreach-email](https://github.com/FloydTang/trade-outreach-email)

## Agent-First 增强价值

会员增强层当前不是改业务逻辑，而是补这几件事：

- 单节点在龙虾里有明确的 `stage_worker` 角色
- 单节点默认只 attach，不独立建飞书工作容器
- 飞书里提供可直接复制给龙虾的增强执行词
- 与总编排链路保持同一套回挂字段、失败回报和协作口径

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
│   ├── 00-单节点增强执行词.md
│   ├── customer-intel-integration.md
│   ├── input-fields.md
│   ├── output-template.md
│   └── screening-rules.md
├── schemas/
└── for-openclaw/
```

## OpenClaw Variant

`for-openclaw/` 提供和总仓一致口径的单节点 OpenClaw 包装版本：

- 角色固定为 `stage_worker`
- 默认只允许 attach 到 `Trade Lead Workflow Hub`
- 不允许独立创建 Base、主表或平行工作容器

## 作者

半斤九两科技
