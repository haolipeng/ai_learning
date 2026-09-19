# AI Learning Notes

个人 AI 学习笔记。

## 目录结构

- `00-资源导航` - 链接、账单、收藏、学习清单
- `01-认知与方法` - AI 时代怎么想、怎么学（原 01 认知与思维 + 02 学习方法论）
- `03-工具使用` - AI 编码工程化：产品用法 + 辅助开源项目
- `04-工程实践` - AI 编码工程化：最佳实践
- `05-AI编程实战` - 训练营、开源项目复现
- `06-AI安全` - AI 对齐与安全研究
- `07-翻译文章` - 未消化的译文（Inbox）

## `00` 收藏怎么写

- 频道 / 博主 / 长期关注的人 → `关注的人.md`
- 单篇博客 / 视频 / 飞书文档 → `好的资料和博文.md`
- 读完并写成自己的笔记后，从收藏里删掉或标「已消化」，正文进主题目录

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
| `Codex/` | 一次 hello world 请求的 trace |

### 2. 辅助 AI 编码的开源项目

目录：`03-工具使用/开源项目`

| 位置 | 学的项目 |
|---|---|
| `Openspec/` | OpenSpec（主文档 `openspec-learning-guide.md`，速查 `OpenSpec-Workflow-Guide.md`） |
| `ponytail.md` | Ponytail |
| `code-review-graph代码审计.md` | code-review-graph |
| `mattpocock-skills/` | Wayfinder 及 grill / spec / tickets / implement / TDD / review |

不要和「用 AI 去复现 Elkeid 等」混在一起，那一类在 `05-AI编程实战`。Go Skills 等链接见 `00-资源导航/AI开源项目列表.md`。

### 3. 最佳实践

目录：`04-工程实践`

| 位置 | 内容 |
|---|---|
| `Harness/` | Agent 流水线、`AGENTS.md` 写法、跨会话连续性 |
| `AI时代如何建立代码质量护栏.md` | 测试与质量门禁 |
| `AI做大型项目的经验.md` | 大仓库维护、熵减 |
| `专业工程师的AI编码实战-从需求到自动提交的完整工作流  AI Engineer.md` | 深模块、可测性、任务切分 |
| `跨平台前端技术选型-Tauri-Electron-Fyne.md` | 与 coso 谈话中的选型摘录 |

## 不放进这三类的

| 位置 | 原因 |
|---|---|
| `01-认知与方法/Netflix演讲.md` | 理解力不能外包，偏认知 |
| `00-资源导航/AI助我学习安全开源项目.md` | 安全开源学习进度清单 |
| `00-资源导航/主流AI Agent学习.md` | 自己做 Agent，不是用 Agent 写代码 |
| `00-资源导航/关注的人.md` | 长期关注的人 |
| `00-资源导航/好的资料和博文.md` | 单篇收藏链接 |
| `07-翻译文章/` | 尚未消化的译文 |
| `05-AI编程实战/` | 练习场：训练营、开源复现 |
