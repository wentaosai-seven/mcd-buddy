# MCP 集成说明（MCP_INTEGRATION.md）

## 实际使用的麦当劳 MCP Server
- **服务地址**：`https://mcp.mcd.cn`
- **传输方式**：Streamable HTTP（MCP 协议 `2024-11-05`）
- **鉴权**：请求头 `Authorization: Bearer <MCP_TOKEN>`（Token 在 `open.mcd.cn/mcp` 控制台「激活」获取，绑定个人会员账号）
- **WorkBuddy 自定义连接器配置（JSON）**：

```json
{
  "mcpServers": {
    "mcd-mcp": {
      "type": "streamablehttp",
      "url": "https://mcp.mcd.cn",
      "headers": {
        "Authorization": "Bearer YOUR_MCP_TOKEN"
      }
    }
  }
}
```

## 实际使用的 Tools（节选，共 35 个可用）
| 工具 | 用途 | 麦门搭子中的角色 |
|------|------|----------------|
| `query-my-coupons` | 查询卡包优惠券资产 | 拉取可用券，按到期日排序 |
| `available-coupons` | 可领取券列表 | 提醒用户先领券再拼单 |
| `auto-bind-coupons` | 自动领取可领券 | 一键补齐券池 |
| `query-store-coupons` | 指定门店+订单类型可用券 | 精确匹配到店/外送可用券 |
| `query-my-account` | 查询积分账户 | 展示积分、识别即将过期积分 |
| `query-meals` / `query-meal-detail` | 餐品列表 / 详情 | 读取菜单与实时价格 |
| `calculate-price` | 计算价格（含优惠） | 多人组合算价，求最优分摊 |
| `query-nearby-stores` | 到店可点餐门店 | 选定自取门店 |
| `delivery-query-addresses` / `delivery-query-stores` | 外送地址 / 可配送门店 | 多人外送场景 |
| `create-order` | 创建订单 | **仅建单**，付款由人在 App 完成 |
| `order-list` | 历史订单 | 识别个人口味偏好 |
| `list-nutrition-foods` | 营养成分数据 | 拼单营养提示 |
| `campaign-calendar` | 当月营销活动 | 推荐当前可参与活动 |
| `now-time-info` | 当前时间 | 券有效期判断 |

> 已通过 `tools/list` 实测确认以上工具可用；`query-my-account` 等只读接口已实测成功返回真实数据。

## 调用流程（对应 SKILL.md 五阶段）
1. **组队**：收集人数、口味/忌口/预算、是否自取、必用券。
2. **拉数据**：`query-my-coupons` + `query-my-account` + `order-list` + `query-meals`（均只读）。
3. **优化**：以 `calculate-price` 对每种组合试算，最大化优惠并公平分摊；优先消耗即将过期券，规避「凑单陷阱」。
4. **确认**：表格复述方案，等用户确认。
5. **建单与战报**：`create-order` 仅生成订单（不付款），随后输出可分享「拼单战报」卡片。

## 业务价值
- **赛道差异**：现有参赛作品多为单人省钱 / 营养，多人拼单是蓝海；且「需叫上朋友一起用」自带传播链，利于 Star 增长（契合按 Star 排名）。
- **能力契合**：麦当劳 MCP 提供券、账户、菜单、算价、建单、营养等 35 个工具，完整支撑多人组合优化与分账；WorkBuddy 负责多约束推理与战报内容生成。
- **合规边界**：只读接口优先；`create-order` 仅建单，支付由用户完成，责任切分清晰。
