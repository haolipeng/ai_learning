快速开始：

```bash
npx skills add mattpocock/skills --skill=improve-codebase-architecture
```

```bash
npx skills update improve-codebase-architecture
```

[源代码](https://github.com/mattpocock/skills/tree/main/skills/engineering/improve-codebase-architecture)

## 它做什么

`improve-codebase-architecture` 会扫描一个代码库，寻找 **深化机会（deepening opportunities）**：也就是那些目前还是浅模块，但有机会变成深模块的地方。浅模块的问题是，它暴露出来的接口几乎和它隐藏的实现一样复杂；深模块则能把更多复杂度收进内部，只留下更小、更稳定的接口。这个 skill 会把候选项整理成一份自包含的可视化 HTML 报告，然后围绕你选中的那一项展开追问。

它**不会**丢给你一张扁平的重构清单。每个候选项都必须通过 **删除测试（deletion test）**：如果删掉这个模块，复杂度会不会被聚拢到一个更小的接口背后？还是只是被挪到了别处？只有能真正聚拢复杂度的候选项，才值得出现在报告卡片里。这个过滤器能避免报告变成泛泛的清理建议。

除非你指定某个区域，否则它还会把范围收束到最近真正发生变更的地方：读取近期提交，把注意力偏向你还在频繁修改的代码。深化一个模块的价值，在于让未来围绕它的变更更容易；所以它会特别关注仓库里最近变化过的部分。

## 什么时候用它

你通过输入 `/improve-codebase-architecture` 来调用它。智能体不会主动选用这个 skill。

把它当成一次周期性的健康检查：每隔几天跑一次，或者当你感觉理解一个概念需要在一堆小模块之间来回跳时，就该用它。它会读取现有架构，并建议哪里值得深化。如果你已经知道想重新设计哪个模块，只是需要一套词汇来想清楚设计，请改用 [codebase-design](https://aihero.dev/skills-codebase-design)。这个 skill 是负责找候选项的巡检；那个 skill 才是真正展开设计的工作台。

## 深化机会

整个 skill 围绕一个概念展开：**深度**。深模块会用一个小而稳定的接口隐藏大量功能；浅模块则会通过几乎和底层代码一样宽的接口泄露实现细节。报告会寻找这些浅的迹象：只为了可测试性而抽出来的纯函数，但真正的 bug 藏在调用方式里（缺少 **局部性**）；跨越 **接缝** 泄露实现的模块；或者一个概念必须打开五个文件才能理解。然后它会提出能修复这些问题的深化方案。

它会使用共享的设计词汇（**module**、**interface**、**depth**、**seam**、**adapter**、**leverage**、**locality**），也会使用你项目在 `CONTEXT.md` 里的领域语言。所以一个候选项读起来会像是"深化 Order intake 模块"，而不是"重构 FooBarHandler"。

## 先报告，再追问

输出是一份可直接在浏览器打开的 HTML 文件，写到操作系统的临时目录里；不会写进仓库。每个候选项都是一张卡片，包含相关文件、摩擦点、用普通语言描述的解决方案、从局部性和杠杆效应角度说明的收益、前后对比图，以及 `Strong` / `Worth exploring` / `Speculative` 徽标。报告最后还会指出它最建议先处理哪一项。

然后它会停下来，问你想探索哪一个。选中一项之后，它会围绕这个设计运行 [grilling](https://aihero.dev/skills-grilling) 追问循环：约束是什么、接缝背后应该放什么、哪些测试应该继续成立。随着决策逐渐成形，它会同步更新领域模型。

## 它适合放在哪

`improve-codebase-architecture` 是 **周期性维护**：每隔几天运行一次，而不是构建链条中的固定步骤。它的邻居包括 [codebase-design](https://aihero.dev/skills-codebase-design)、[grilling](https://aihero.dev/skills-grilling) 和 [domain-modeling](https://aihero.dev/skills-domain-modeling)：`codebase-design` 提供候选项使用的深度与接缝词汇；`grilling` 会在你选定候选项后带你走过决策树；`domain-modeling` 会在重新设计逐步稳定时，让 `CONTEXT.md` 和 ADR 保持最新。如果你不确定该用哪个 skill 或哪条流程，[ask-matt](https://aihero.dev/skills-ask-matt) 会帮你路由。
