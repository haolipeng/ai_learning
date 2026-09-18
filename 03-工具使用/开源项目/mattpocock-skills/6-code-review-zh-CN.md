快速开始：

```bash
npx skills add mattpocock/skills --skill=code-review
```

```bash
npx skills update code-review
```

[源代码](https://github.com/mattpocock/skills/tree/main/skills/engineering/code-review)

## 它做什么

`code-review` 会审查 `HEAD` 和你提供的某个固定基点之间的 diff。这个基点可以是 commit、branch、tag，或者 merge-base。它会沿着两条彼此独立的轴来审查：**Standards**（代码是否遵循这个仓库记录下来的约定？）和 **Spec**（代码是否实现了原始 issue 或 spec 要求的内容？）。它会把每条轴交给各自的并行子智能体，并把结果并排报告。它绝不会合并这两组 findings，也不会重新排序；让它们保持分离正是重点所在，因为一个变更可能通过其中一条轴，却在另一条轴上失败，而混成一个结论会让一个问题遮住另一个问题。

## 什么时候用它

输入 `/code-review`，或者当你要求 review 某个 branch、PR、进行中的变更，或任何 "since X" 的内容时，智能体会自动选用它。

当你有一个 diff，需要把它和某个已知良好的基点比较，并希望两个问题被独立回答时，就该用它：*它构建得对吗？* 以及 *它构建的是对的东西吗？* 它会在构建循环的末尾运行；如果你要实际用测试先行的方式写代码，请使用 [tdd](https://aihero.dev/skills-tdd)；如果要把一整份 spec 构建成代码，请使用 [implement](https://aihero.dev/skills-implement)，它会在提交前运行自己的 `/code-review` pass。

## 前置条件

**Spec** 这条轴需要找到原始 spec：它可以来自 commit message 里的 issue 引用、你传入的路径，或者 `docs/` / `specs/` 下的某份 spec。issue tracker 的这套连接配置来自 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills)；如果找不到 spec，Spec 轴会直接跳过并说明原因。**Standards** 这条轴不需要任何预先设置：即使仓库没有记录任何约定，它也始终带着内置的 Fowler 代码坏味道基线。

## 两条轴，永不混合

这里的定义性想法就是 **两条轴**。**Standards** 问的是这个 diff 是否符合这个仓库写代码的方式：它的 `CODING_STANDARDS.md` 或 `CONTRIBUTING.md`，再加上一组固定的约 12 个 Fowler 代码坏味道基线（Mysterious Name、Duplicated Code、Feature Envy、Data Clumps，等等）。两条规则让这个基线保持安全：仓库中写明的标准永远优先于它，而且每个坏味道都是一次判断，不是硬性违规。**Spec** 问的是一个正交问题：代码是否真的做了 issue 或 spec 要求的事情，没有漏掉需求，也没有偷偷塞进范围蔓延。

它们会作为并行子智能体运行，这样谁都不会污染对方的上下文；最终报告会把它们放在独立的 `## Standards` 和 `## Spec` 标题下，并给每条轴各自的摘要。这里刻意不产出一个跨轴的单一胜负结论。

## 它正常工作的样子

- 它先固定并确认基点（`git rev-parse`）；如果 ref 错了或 diff 为空，会快速失败，而不是在子智能体里才失败。
- Standards 和 Spec 的 findings 会出现在两个不同区块中，并且每条都引用自己的来源：前者引用仓库标准或基线坏味道，后者引用 spec 原文。
- 找不到 spec 时，Spec 轴会报告 "no spec available"，而不是凭空发明需求。

## 它适合放在哪

`code-review` 是主构建链条尾部的 review 步骤：

```txt
grill-with-docs -> to-spec -> to-tickets -> implement -> code-review
```

它最近的邻居是 [implement](https://aihero.dev/skills-implement)：后者负责驱动构建，并在提交前把它作为自己的 review pass 来调用。上游方面，它要检查的 spec 由 [to-spec](https://aihero.dev/skills-to-spec) 和 [to-tickets](https://aihero.dev/skills-to-tickets) 产出。如果你不确定该用哪个 skill 或哪条流程，[ask-matt](https://aihero.dev/skills-ask-matt) 会帮你路由。
