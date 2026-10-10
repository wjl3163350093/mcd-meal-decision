# 麦麦点餐决策助手

一个供 WorkBuddy 使用的创意 Skill：把「预算、口味、营养目标、可用优惠」整理成三种可解释的麦当劳点餐方案。商品、门店、优惠和实付价格必须来自麦当劳 MCP 的实时结果；本仓库不保存用户 Token，也不自动下单。

## 参赛定位

用户常常在「吃什么、多少钱、是否符合自己的偏好」之间来回切换。本 Skill 在同一轮对话里完成需求澄清、门店菜单查询、优惠查询、方案比价和解释，最后由用户自行决定是否下单。

目标用户：想在麦当劳按预算选餐、比较营养信息与优惠，同时希望知道每个推荐依据的消费者。

项目亮点：

- 同时展示「最省钱」「最贴合目标」「均衡选择」三档方案，并说明各自的取舍。
- 价格只以 `calculate-price` 的结果为准；营养只引用 `list-nutrition-foods` 能匹配到的餐品，不猜热量。
- 缺少价格、营养或优惠数据时明确标注，不把估算包装成实时结论。
- 不调用下单或积分扣减工具；需要购买时交还给用户确认并使用官方渠道。

## 文件

- [`skill/SKILL.md`](skill/SKILL.md)：可导入 WorkBuddy 的 Skill。
- [`skill/references/decision-rules.md`](skill/references/decision-rules.md)：筛选和解释规则。
- [`MCP_INTEGRATION.md`](MCP_INTEGRATION.md)：实际调用的麦当劳 MCP 工具与流程。
- [`mcp-config.example.json`](mcp-config.example.json)：只含环境变量占位符的公开配置示例。
- [`CONTEST_DECLARATION.md`](CONTEST_DECLARATION.md)：官方原文参赛声明，发布前请参赛者自行阅读。
- [`demo.md`](demo.md)：演示对话与验收清单。
- [`submission.md`](submission.md)：赛前核对和提交清单。

## 使用步骤

1. 在[麦当劳 MCP 开放平台](https://open.mcd.cn/mcp)申请个人 Token，并依照[官方接入说明](https://github.com/M-China/mcd-mcp-server)在 WorkBuddy 添加麦当劳 MCP 连接器。Token 只填在 WorkBuddy 的私有配置中。
2. 在 WorkBuddy 的「专家·技能·连接器 → 技能 → 添加技能」导入 `skill` 目录，或使用 WorkBuddy 的创建技能功能写入 `SKILL.md`。安装方式见[WorkBuddy 官方技能文档](https://www.workbuddy.cn/docs/workbuddy/From-Beginner-to-Expert-Guide/Function-Description/Skills-Market)。
3. 对 WorkBuddy 说：`我在附近门店想吃一顿不超过 35 元的午餐，尽量高蛋白，帮我比较三种方案，不要下单。`
4. 检查输出是否包含门店、餐品、实时价格依据、营养数据来源、优惠使用条件及无法确认的项目。

## 当前状态

2026-10-10 已在 WorkBuddy 中完成真实 MCP 测试及第二轮推荐复核，并修改了午餐时间、套餐选配、营养匹配和方案排序规则。结果摘要及待复核点见 [`docs/first-run.md`](docs/first-run.md)。[`workbuddy.md`](workbuddy.md) 是可读取上下文的整理稿，不是完整逐字导出；原始工具响应仍需核对。正式提交要求以[麦当劳官方比赛仓库](https://github.com/M-China/mcd-developer-innovation-challenge)为准。

## 数据与授权

用户的地址、会员优惠、订单和 Token 均不得提交到公开仓库。麦当劳 MCP 的使用须遵守[官方说明和服务规则](https://github.com/M-China/mcd-mcp-server)。本项目不暗示获得麦当劳或腾讯官方背书。

