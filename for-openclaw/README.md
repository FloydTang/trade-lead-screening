# 线索整理 / 初筛 Skill for OpenClaw

当前状态：可演示

这个目录提供 OpenClaw-native 的单节点包装版本，目标不是单独造一套飞书工作容器，而是把这个节点稳定挂到总链路里。

角色定位：

- `stage_worker`
- `线索初筛员`

当前职责：

- 接收候选线索 JSON
- 做字段标准化和缺失识别
- 输出初筛结果和客户背调桥接输入

## 安装归口说明

当前仓库采用两层结构：

- 根目录 README：公开最小可用说明
- 当前目录：龙虾 / OpenClaw 的单节点运行说明
- 这个单节点仓库本身也保留可独立执行的最小功能

这个 Skill 的开源版本身就可以单独使用，并能完成当前节点的最小可用功能。

如果你希望在龙虾 / OpenClaw 中获得更精致、更完整的使用体验，建议按下面流程复制增强执行词：

- [飞书增强入口：复制增强执行词给龙虾](https://evenbetter.feishu.cn/wiki/ADmiwiultihx6Yk1p2UcjfmVn6d)

如果链接打不开，请先确认使用和半斤九两科技会员群绑定的飞书账号登录。

如果你暂时还没有绑定过，或当前还没有半斤九两科技的账号，请访问：[evenbetter.tech](https://evenbetter.tech)

仓库内源码基线：

- `../references/00-单节点增强执行词.md`
- `./SKILL.md`

## Feishu 接入约束

当前这个 OpenClaw 变体如果要接飞书，默认必须挂到同一个主 Base 下运行。

固定要求：

- 先查主 Base，再查 `Lead Workflow Master`
- 初筛结果默认写入同一个 Base 下的 `Lead Screening Results`
- 不因为单点运行就新建一个新的多维表格
- 后续进入客户背调或其他节点时，继续复用原主记录和原工作容器
- 当前角色固定为 `stage_worker`
- 当前节点固定 `attach_only`
- `feishu_container_creation = forbidden`

统一目标：

- 所有数据最终统一挂到 `Trade Lead Workflow Hub`
- 单节点不独立声明飞书工作容器

## 推荐模型

- `coze/glm-4-7-251222`

## 会员增强价值

增强层重点不是换业务逻辑，而是减少安装试错：

- 给龙虾一个可以直接复制的单节点执行词
- 明确这个节点只做字段整理和初筛桥接
- 明确它只能回挂 `Lead Screening Results`
- 明确失败后要把结果交回主代理统一回写

## 快速运行

```bash
python3 ./scripts/build_lead_screening_from_openclaw.py \
  --input-json ./examples/sample-input.json
```

## 作者

半斤九两科技
