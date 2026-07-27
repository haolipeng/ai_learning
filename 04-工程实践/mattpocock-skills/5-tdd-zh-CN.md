快速开始：

```bash
npx skills add mattpocock/skills --skill=tdd
```

```bash
npx skills update tdd
```

[源代码](https://github.com/mattpocock/skills/tree/main/skills/engineering/tdd)

## 它做什么

`tdd` 会用测试先行的方式构建功能或修复 bug：一次只处理一个行为，通过红-绿循环把代码驱动出来。

它**不会**一开始就把所有测试都写完。先批量写测试（"水平切片"）会产出针对 _想象中_ 行为的测试：它们检查的是事物的形状，并且会对真实变化变得迟钝。`tdd` 采用的是垂直切片：先写一个测试，再写刚好足够让它通过的代码，然后写下一个测试。每一轮都会吸收上一轮教给你的东西。测试只针对公共接口，所以底下的实现可以变化，而测试不用跟着移动。

## 什么时候用它

输入 `/tdd`，或者当任务适合时，智能体会自动选用它：比如用测试先行的方式构建功能、修复 bug，或者你说了 "red-green-refactor"（红-绿-重构）。

当你有一个具体行为要构建，并且想要能经受重构的测试时，就该用它。如果行为还没有明确下来，先把 spec 定下来；这时请使用 [to-spec](https://aihero.dev/skills-to-spec)。如果真正的问题是接口形状，而不是测试本身，请使用 [codebase-design](https://aihero.dev/skills-codebase-design)；`tdd` 在规划时会调用它来获得深模块词汇。

## 红-绿，一次一个切片

核心思想是 **红-绿循环（red-green loop）**：写一个失败测试（红），加入刚好足够让它通过的代码（绿），然后为下一个行为重复这个过程。每一轮都由上一轮的发现来指引。第一轮就是一条 **曳光弹（tracer bullet）**：先用一个测试证明单条路径可以端到端工作，然后再从这条路径向外扩展。因为代码是你刚刚亲手写出来的，你很清楚哪个行为重要，以及该如何验证它；你不会跑到自己还看不清的地方，提前承诺一套尚未理解的测试结构。

两条规则会让测试保持诚实。一个好测试读起来像规格说明（"用户可以用有效购物车结账"），并且通过公共 API 触达真实代码路径，所以重命名内部函数不会让它失败。预期值也必须来自一个独立事实来源：一个已知正确的字面量、一个手算示例，或者 spec。它绝不能用和代码相同的方式重新算一遍；那样写出来的是 **同义反复式测试（tautological test）**，它会因为构造方式本身而通过，却什么也证明不了。

只有在测试套件是绿色时才重构；红灯时永远不要重构。

## 它正常工作的样子

- 它先写一个测试，让它通过，然后才写下一个；不是先批量写测试，再批量写代码。
- 测试命名的是行为，而不是内部实现，并且能经受内部重命名。
- 预期值是来自 spec 的字面量，而不是用和代码相同的方法推导出来的数字。

## 它适合放在哪

`tdd` 是主构建链条用来写代码的红-绿循环：

```txt
grill-with-docs -> to-spec -> to-tickets -> implement -> code-review
```

[implement](https://aihero.dev/skills-implement) 是这条链上的构建步骤，它会在内部驱动 `tdd`，以测试先行的方式构建每个 ticket，然后交给 [code-review](https://aihero.dev/skills-code-review)。所以 `tdd` 是这个步骤里面的引擎，而不是一个独立步骤。只要有具体行为要构建，即使没有完整 spec，你也可以直接使用它。它的另一个邻居是 [codebase-design](https://aihero.dev/skills-codebase-design)，`tdd` 会依靠它来找到值得测试的深模块接缝。如果你不确定该用哪个 skill 或哪条流程，[ask-matt](https://aihero.dev/skills-ask-matt) 会帮你路由。
