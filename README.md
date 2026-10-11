# 麦麦点餐决策助手

一个供 WorkBuddy 使用的创意 Skill：把「预算、口味、营养目标、可用优惠」整理成最多三种可解释的麦当劳点餐方案。商品、门店、优惠和实付价格必须来自麦当劳 MCP 的实时结果；本仓库不保存用户 Token，也不自动下单。

**核心问题：为什么花 ¥32.50 的推荐，可能不如花 ¥14.90？** 本项目会把实际结算价、能可靠匹配的营养数据和用户目标放在同一张表里；如果更贵方案没有可核实的收益，就明确把它移出推荐。

## 一眼看懂：一次真实测试

2026-10-10，WorkBuddy 使用麦当劳 MCP 查询上海徐家汇附近门店，以 **11:00 到店自取、一人、预算 ¥35、尽量高蛋白** 为条件，报告了以下比较。价格由 `calculate-price` 返回；下表是当次时段的记录，**不是今天的实时菜单或价格**。完整原始 MCP 响应尚未独立核对，详情见[实测记录](docs/first-run.md)。

| 已比较候选中的定位 | 餐品 | 当次实付 | 可核实蛋白质 | 决策依据 |
| --- | --- | ---: | ---: | --- |
| 最省钱 | 那么大鸡排（椒盐风味）＋锡兰红茶 | ¥13.90 | 未核实 | 已算价候选中最低；不拿名称近似的营养项硬匹配 |
| 价格与蛋白质权衡 | 双层吉士汉堡＋锡兰红茶 | ¥14.90 | 27g | 比 ¥32.50 方案少花 ¥17.60，报告的蛋白质多 1g |
| 尽量高蛋白 | 薄皮焦香V翅 ×2 | ¥32.00 | 34g | 已核实候选中蛋白质最高，但餐品结构较单一 |

这里的「权衡」不代表营养结构均衡。用户没有提出热量目标，因此 Skill 不把热量低自动判为更好。以上「最省钱」「最高蛋白」只针对当次查询并算价的候选，不宣称穷尽全店菜单。

## 参赛定位

用户常常在「吃什么、多少钱、是否符合自己的偏好」之间来回切换。本 Skill 在同一轮对话里完成需求澄清、门店菜单查询、优惠查询、方案比价和解释，最后由用户自行决定是否下单。

目标用户：想在麦当劳按预算选餐、比较营养信息与优惠，同时希望知道每个推荐依据的消费者。

项目亮点：

- 在证据足够时展示最多三档方案，并解释为什么某个更贵方案被淘汰；证据不足时少给方案。
- 价格只以 `calculate-price` 的结果为准；营养只引用 `list-nutrition-foods` 能匹配到的餐品，不猜热量。
- 缺少价格、营养或优惠数据时明确标注，不把估算包装成实时结论。
- 不调用下单或积分扣减工具；需要购买时交还给用户确认并使用官方渠道。

### 已验证与待验证

| 能力 | 状态 |
| --- | --- |
| 附近门店、预约午餐时段、菜单、套餐详情、营养查询、实时算价 | WorkBuddy 已报告一次真实调用；原始响应待独立核对 |
| 发现并纠正不合理的 ¥32.50 主推荐 | 已完成第二轮复核与规则修改 |
| 门店公开券查询及带券重新算价 | WorkBuddy 已报告真实调用；两次带券算价均失败，未核实出可用券；原始响应待独立核对 |
| 第二轮套餐营养复核 | 发现蛋白质加总错误及同名套餐值冲突，已标记待核实 |
| 下单、领券、扣积分 | 不在本 Skill 的常规流程内，未执行 |

第二轮[脱敏测试记录](docs/second-run.md)覆盖优惠券不可用时的无券方案；[原测试提示词](docs/next-test.md)留作复现。套餐营养冲突需要继续核对原始 MCP 响应。

## 文件

- [`skill/SKILL.md`](skill/SKILL.md)：可导入 WorkBuddy 的 Skill。
- [`skill/references/decision-rules.md`](skill/references/decision-rules.md)：筛选和解释规则。
- [`MCP_INTEGRATION.md`](MCP_INTEGRATION.md)：实际调用的麦当劳 MCP 工具与流程。
- [`mcp-config.example.json`](mcp-config.example.json)：只含环境变量占位符的公开配置示例。
- [`CONTEST_DECLARATION.md`](CONTEST_DECLARATION.md)：官方原文参赛声明，发布前请参赛者自行阅读。
- [`demo.md`](demo.md)：演示对话与验收清单。
- [`workbuddy.md`](workbuddy.md)：WorkBuddy 可读取上下文的整理稿，已标注不完整之处。

## 使用步骤

1. 在[麦当劳 MCP 开放平台](https://open.mcd.cn/mcp)申请个人 Token，并依照[官方接入说明](https://github.com/M-China/mcd-mcp-server)在 WorkBuddy 添加麦当劳 MCP 连接器。Token 只填在 WorkBuddy 的私有配置中。
2. 在 WorkBuddy 的「专家·技能·连接器 → 技能 → 添加技能」导入 `skill` 目录，或使用 WorkBuddy 的创建技能功能写入 `SKILL.md`。安装方式见[WorkBuddy 官方技能文档](https://www.workbuddy.cn/docs/workbuddy/From-Beginner-to-Expert-Guide/Function-Description/Skills-Market)。
3. 对 WorkBuddy 说：`我在附近门店想吃一顿不超过 35 元的午餐，尽量高蛋白，帮我比较三种方案，不要下单。`
4. 检查输出是否包含门店、餐品、实时价格依据、营养数据来源、优惠使用条件及无法确认的项目。

## 当前状态

2026-10-10 已在 WorkBuddy 中完成午餐场景测试及推荐复核；2026-10-11 又完成门店公开券不可用场景测试，发现套餐营养算术错误与官方同名套餐值冲突。详见 [`docs/first-run.md`](docs/first-run.md) 和 [`docs/second-run.md`](docs/second-run.md)。[`workbuddy.md`](workbuddy.md) 是可读取上下文的整理稿，不是完整逐字导出；原始工具响应仍需核对。本项目已通过[官方比赛 Issue #374](https://github.com/M-China/mcd-developer-innovation-challenge/issues/374)报名。

## 数据与授权

用户的地址、会员优惠、订单和 Token 均不得提交到公开仓库。麦当劳 MCP 的使用须遵守[官方说明和服务规则](https://github.com/M-China/mcd-mcp-server)。本项目不暗示获得麦当劳或腾讯官方背书。

