# AI Learning Notes

个人 AI 学习笔记。

## 目录结构

- `00-资源导航` - 链接、账单、收藏
- `01-认知与思维` - AI 时代的思维方式与认知
- `02-学习方法论` - 如何借助 AI 高效学习
- `03-工具使用` - AI 编码工程化：产品用法 + 辅助开源项目
- `04-工程实践` - AI 编码工程化：最佳实践
- `05-AI编程实战` - 训练营、开源项目复现
- `06-AI安全` - AI 对齐与安全研究
- `07-翻译文章` - 未消化的译文（Inbox）

## AI 编码工程化（三类）

互斥规则：离开 Cursor / Claude Code / Codex 还读得懂吗？

- 读不懂 → **第 1 类**（某个产品怎么用）
- 换个 Agent 还能用 → **第 3 类**（最佳实践）
- 在学某个有名字、有仓库的开源项目 / Skill 包 → **第 2 类**

OpenSpec、Wayfinder、Ponytail 等先归第 2 类；抽出可复用原则后再单独写入第 3 类。

### 1. 产品使用

目录：`03-工具使用/Cursor`、`Claude Code`、`Codex`

| 位置 | 内容 |
|---|---|
| `Cursor/` | 使用小技巧、Rules 配置与设计、Memory Bank（已过时）、用 Cursor 读开源 |
| `Claude Code/使用技巧/` | 官网文档、使用技巧、状态词中英对照 |
| `Claude Code/Feature-Dev/` | 官方插件 Feature Dev 的 7 阶段工作流 |
| `Claude Code/Skills/` | Skills 学习提纲 |
| `Claude Code/原理分析/` | 原理剖析草稿 |
| `Codex/` | 一次 hello world 请求的 trace |

### 2. 辅助 AI 编码的开源项目

目录：`03-工具使用/开源项目`

| 位置 | 学的项目 |
|---|---|
| `Openspec/` | OpenSpec |
| `ponytail.md` | Ponytail |
| `code-review-graph代码审计.md` | code-review-graph |
| `mattpocock-skills/` | Wayfinder 及 grill / spec / tickets / implement / TDD / review |
| `golang 最佳实践Skills.md` | samber/cc-skills-golang |

不要和「用 AI 去复现 Elkeid 等」混在一起，那一类在 `05-AI编程实战`。

### 3. 最佳实践

目录：`04-工程实践`

| 位置 | 内容 |
|---|---|
| `Harness/` | Agent 流水线、`AGENTS.md` 写法、跨会话连续性 |
| `AI时代如何建立代码质量护栏.md` | 测试与质量门禁 |
| `AI做大型项目的经验.md` | 大仓库维护、熵减 |
| `专业工程师的AI编码实战-从需求到自动提交的完整工作流  AI Engineer.md` | 深模块、可测性、任务切分 |

## 不放进这三类的

| 位置 | 原因 |
|---|---|
| `01-认知与思维/Netflix演讲.md` | 理解力不能外包，偏认知 |
| `00-资源导航/主流AI Agent学习.md` | 自己做 Agent，不是用 Agent 写代码 |
| `00-资源导航/好的资料和博文.md` | 收藏链接 |
| `07-翻译文章/` | 尚未消化的译文 |
| `05-AI编程实战/` | 练习场：训练营、开源复现 |
| `03-工具使用/notebooklm/` | 知识管理工具，暂放此处 |
