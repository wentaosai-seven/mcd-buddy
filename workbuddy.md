# workbuddy.md — 开发过程记录（WorkBuddy 积分奖所需）

> 本文件记录使用 WorkBuddy 开发「麦门搭子 / McBuddy」的完整对话与决策过程，作为参赛所需的开发文档。

## 项目缘起
- 时间：2026-10-25（赛事最后一天，报名+排名截止 23:59 北京时间）
- 触发：用户发来 workbuddy.link 活动页，挑战「用 WorkBuddy 开发，展示创意和优势」。
- 目标：做一个基于麦当劳 MCP 的 Agent Skill，开源参赛，按 Star 排名。

## 关键决策（WorkBuddy 推理过程）
1. **先摸清战场，不瞎卷。** 用 WebSearch 拉取实时榜单与规则：104 个项目进榜，前排 `mcd-saver-strategist`(611★)、`mcd-budget-meal`(512★)、`mcd-coupon-strategist`(491★) 全在做单人省钱；`mcd-calorie-butler` 等做营养；`mcd-personality`(MDTI) 靠「可分享人格卡」走传播路线。
2. **定位白空格。** 所有头部作品都是**单人**视角。多人拼单（朋友/家庭/部门）是蓝海，且「得叫上朋友」自带传播链——而排名靠 Star，传播力即排名力。这是模型「多约束推理 + 内容生成」优势的最佳落点，而非再去卷计算器。
3. **定概念：麦门搭子 / 多人拼单决策官。** 价值 = 多人组合优化（券+预算+口味+配送）+ 公平分账 + 可分享战报卡。
4. **守住安全边界。** 明确「只建单不付款、优先只读接口、付款在人」——这是麦当劳 MCP 当前最稳的责任切分，也是参赛作品的合规底线。
5. **五阶段工作流。** 每阶段 STOP 等用户确认，呼应「Agent 不该自作主张跑完」的交互纪律。

## 产出文件
- `SKILL.md`：Skill 定义（名称、触发、五阶段流程、安全边界、赛道差异）
- `README.md`：GitHub 参赛说明 + WorkBuddy 安装步骤
- `sample-card.html`：脱敏 mock 的「拼单战报」分享卡片，展示内容生成优势
- 本文件：开发过程记录

## 关键修正（开发中途发现）
- **MCP 真实地址不是 `open.mcd.cn/mcp`**（那是网页平台），而是 **`https://mcp.mcd.cn`**（Streamable HTTP）。裸调 `open.mcd.cn/mcp` 会被 WAF 返回 405；改用 `mcp.mcd.cn` 后 `initialize` 成功，服务器 `mcd-mcp v1.0.0`，`tools/list` 返回 **35 个真实工具**。
- **官方要求仓库必须含 `CONTEST_DECLARATION.md`（内容不可改）与 `MCP_INTEGRATION.md`**，初版漏了，已补齐（声明文件原样从官方仓库拉取）。
- 报名 Issue 格式有严格校验（标题/正文间不可有换行、地址不可用 Markdown），已将 #1388 改为标准【参赛申请】格式。

## 已完成的最后一英里（2026-10-25 当晚）
- [x] 用户提供 GitHub 账号（wentaosai-seven）+ PAT，已建公开仓库 `wentaosai-seven/mcd-buddy` 并推送
- [x] 用户提供麦当劳 MCP Token，已实测 `mcp.mcd.cn` 连通，`query-my-account` 等只读接口真实返回数据
- [x] 在官方仓库开报名 Issue #1388（标准格式）
- [x] 补齐 CONTEST_DECLARATION.md / MCP_INTEGRATION.md，修正接入地址
- [ ] 拉 Star > 0（GitHub 不允许自星，需朋友/朋友圈助力，23:59 前进榜）

## 验证
- [x] Skill 逻辑自洽、边界清晰（仅建单不付款）
- [x] 安装步骤可执行（含 mcp.mcd.cn 配置）
- [x] 真实 MCP 联调成功（tools/list 35 工具 + query-my-account 只读实测 200）
- [ ] 真实建单→战报卡生成（需用户确认后再调用 create-order，本会话未自动下单）
