/////////////////////////////////////////////////////////////////////////////

| 缩写          | 全称               | 中文理解   | 主要负责                                                |
| ------------- | ------------------ | ---------- | ------------------------------------------------------- |
| **PM**        | Project Manager    | 项目经理   | 组织流程、分配任务、推进阶段、控制是否可以进入下一步    |
| **BA**        | Business Analyst   | 业务分析师 | 把用户的自然语言需求整理成清晰、可验证的需求            |
| **SA**        | Solution Architect | 方案架构师 | 根据需求设计技术方案、模块关系、接口、数据流等          |
| **RR**        | Readiness Reviewer | 就绪评审员 | 在开发前检查“需求和设计是否已经足够完整，可以开工”      |
| **Developer** | Developer          | 开发工程师 | 根据 requirements/design 实际修改代码、写测试、实现功能 |
| **CR**        | Code Reviewer      | 代码审查员 | 独立检查 Developer 写的代码，判断是否应该 PASS / REJECT |
| **TE**        | Test Engineer      | 测试工程师 | 从测试角度验证功能是否真的满足需求                      |

你可以把它理解为一个真实的软件团队：

```
用户提出需求
    ↓
BA：你到底想要什么？
    ↓
SA：技术上准备怎么实现？
    ↓
RR：需求和方案够清楚了吗？现在能不能开工？
    ↓
Developer：开始写代码
    ↓
CR：代码写得对不对？
    ↓
TE：功能真的能用吗？
    ↓
PM：确认流程完成、进入交付
```

这里最容易不理解的是 **RR**。

它不是代码 Review，而是**开发前的 Review**。

RR = 防止“需求还没想清楚，AI 就开始写代码”。



传统开发里面，人容易犯这个错误：

看到一个需求 → 马上开始写代码。

AI 时代这个问题会被放大。

因为 AI 写代码速度非常快。



https://github.com/walkinglabs/learn-harness-engineering



通过如下的课程列表去学习：

https://walkinglabs.github.io/learn-harness-engineering/zh/



第一课 实验

https://walkinglabs.github.io/learn-harness-engineering/zh/projects/project-01-baseline-vs-minimal-harness/



限定30分钟时间和20轮次，从15：00开始实验



好的范例是长什么样的？

feature_list.json文件

```
{
  "project": "project-01",
  "description": "Baseline Electron knowledge base with minimal harness",
  "features": [
    {
      "id": "window-launch",
      "name": "Window Launch",
      "description": "Electron app opens a BrowserWindow with correct dimensions and preload script",
      "status": "pass",
      "evidence": "npm run dev launches window at 1200x800 with contextIsolation=true and nodeIntegration=false",
      "testedAt": "2026-03-30T10:00:00Z"
    },
    {
      "id": "document-list",
      "name": "Document List Panel",
      "description": "Left sidebar shows imported documents with empty state message",
      "status": "pass",
      "evidence": "DocumentList component renders empty state when no documents, shows document cards when data present",
      "testedAt": "2026-03-30T10:05:00Z"
    },
    {
      "id": "question-panel",
      "name": "Question Panel",
      "description": "Bottom input bar accepts questions and submits via IPC",
      "status": "pass",
      "evidence": "QuestionPanel renders text input and Ask button, submits to window.knowledgeBase.qa.ask on Enter or click",
      "testedAt": "2026-03-30T10:08:00Z"
    },
    {
      "id": "data-directory",
      "name": "Data Directory",
      "description": "PersistenceService creates and manages userData/knowledge-base-data directory",
      "status": "pass",
      "evidence": "PersistenceService constructor calls ensureDirectories() creating data, documents, and index subdirectories",
      "testedAt": "2026-03-30T10:10:00Z"
    }
  ]
}
```

四个具体功能是窗口启动、文档列表、问答面板、本地数据目录。



起码包括上述内容：

- 单元测试 — 能跑 make test 或 cargo test 或 go test
- 集成测试/端到端测试 — 能跑完整流程
- Linter — 比如 clippy、golangci-lint、静态分析工具
- 编译检查 — 确保能编译通过（这个肯定有，但我想确认你是否会在 CI 或本地脚本里显式检查）
- CI/CD — GitHub Actions / GitLab CI / Jenkins 之类的



**AI Coding Agent Readiness Checklist**

https://gist.github.com/gmoigneu/a963b595ac238ad2d2260ebb8b29f048



**Making Your Repository AI-Ready**

https://medium.com/@chalyi/making-your-repository-ai-ready-1ab45b05222b



**项目仓库地址搜集**

https://github.com/agent-next/agent-ready



https://github.com/superduck-ai/agent-readiness



# 2026年最新AIAgent应用开发学习路线零基础到精通

https://github.com/liyupi/codefather/issues/51
