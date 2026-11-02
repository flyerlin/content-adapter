---
name: content-adapter
description: 当用户或上游 Agent 需要将一段原始营销文案适配到不同社交媒体平台（如小红书、抖音、微信公众号、淘宝），并自动进行平台违禁词过滤时使用。自动调整字数、格式、Emoji 和标签。
whenToUse: 用户提供了一段原始文本（如产品介绍、干货文章），并要求将其转化为特定平台（如小红书种草文、抖音脚本、淘宝详情页）的格式时。
modelInvocable: true
userInvocable: true
---

# 多平台内容合规与自适应适配器

## 输入要求

- `raw_content`（String，必填）：原始的文案内容或核心卖点。
- `target_platform`（String，必填）：目标平台。支持的值：`xiaohongshu`（小红书）、`douyin`（抖音）、`wechat`（微信公众号）、`taobao`（淘宝/天猫）。
- `tone`（String，可选）：期望的语气的，默认为 `engaging`（吸引人）。

## 执行步骤

### 1. 违禁词预检（通用）
扫描 `raw_content`，过滤掉《广告法》明令禁止的极限词（如：最、第一、绝对、100%、根治、顶级）。如果发现，将其替换为温和词汇（如“最”替换为“很”），并在输出中记录。

### 2. 按平台进行适配
根据 `target_platform` 执行以下不同的处理逻辑：

- **如果 target_platform == "xiaohongshu"**：
  - **字数**：控制在 800 字以内，段落短小。
  - **排版**：每段开头加上相关的 Emoji（如 🌟、✅、💡）。
  - **结构**：痛点引入 + 3-4 个核心卖点（带符号排版）+ 结尾互动引导 + 3-5 个带 # 的标签。
  - **语气**：像闺蜜分享，多用“谁懂啊”、“挖到宝了”、“绝绝子”。

- **如果 target_platform == "douyin"**：
  - **结构**：黄金3秒钩子（反转/疑问/痛点） + 快节奏分点陈述 + 强行动呼吁（点赞/关注）。
  - **字数**：口语化，语速感强，控制在 300 字以内。

- **如果 target_platform == "wechat"**：
  - **结构**：正式、有深度的长文排版。主标题 + 引言 + 小标题（1、2、3）+ 总结。
  - **字数**：可以适当扩充，逻辑严密，不带过多 Emoji。

- **如果 target_platform == "taobao"**：
  - **结构**：纯产品导向。标题（含核心搜索词） + 卖点列表（参数化、数据化） + 售后承诺。
  - **语气**：客观、专业、促单。

### 3. 输出格式

严格输出以下 JSON 结构，不要添加任何解释性文字或 Markdown 围栏：

{
  "success": true,
  "platform": "目标平台名",
  "adapted_content": "适配后的最终文案内容（包含排版和标签）",
  "banned_words_replaced": ["被替换掉的违禁词列表"],
  "word_count": 0
}

字段说明：
- `success`（Boolean）：是否成功适配。
- `adapted_content`（String）：经过平台规则修改、带了 Emoji 或排版的完整文案。
- `banned_words_replaced`（Array）：在预检中被过滤掉的违禁词。
- `word_count`（Integer）：最终生成的字数。
