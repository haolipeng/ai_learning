快速开始：

```bash
npx skills add mattpocock/skills --skill=implement
```

```bash
npx skills update implement
```

[源代码](https://github.com/mattpocock/skills/tree/main/skills/engineering/implement)

## 它做什么

`implement` 的作用是：把已经写清楚的 spec 或 tickets，真正变成代码。

它会按测试驱动开发（TDD）的方式推进实现：先写测试，再写代码，过程中频繁做类型检查和运行测试。完成后，它会交给 review，并把改动提交到当前分支。

它**不会**决定要构建什么。到了 `implement` 这一步，spec 应该已经定下来，测试边界也应该已经约定好。`implement` 负责执行计划，不负责重新讨论方案；真正的需求判断发生在上游。



## 什么时候用它

你通过输入 `/implement` 来调用它。智能体不会主动选用这个 skill。

当工作已经写成 spec，或者已经拆成 tickets，并且你准备把它变成代码时，就该用它。

如果 spec 还不存在，先用 [to-spec](https://aihero.dev/skills-to-spec) 写 spec。

如果已经有 spec，但还没有拆成具体任务，可以用 [to-tickets](https://aihero.dev/skills-to-tickets) 把它拆成 tickets。

如果你只是想用“测试先行”的方式构建某个东西，但不需要完整 spec，就直接使用 [tdd](https://aihero.dev/skills-tdd)。



## 预先约定的测试边界

在开始写代码之前，`implement` 需要先知道一件事：最后应该用什么方式判断这个功能已经做好了。

比如，一个功能可以通过 API 测试来验证，也可以通过页面交互测试来验证，还可以通过某个模块的公开接口来验证。这个“从哪里验证”的位置，就是 **测试边界（seam）**。

这个边界通常会在 [to-spec](https://aihero.dev/skills-to-spec) 阶段先定好。`implement` 不会一边写代码，一边临时决定该测哪里。

实际执行时，它会按这个顺序推进：

- 先根据约定好的测试边界，写一个会失败的测试。
- 再写刚好足够的代码，让这个测试通过。
- 测试通过后，整理一下代码，再进入下一个测试。
- 过程中经常做类型检查，并反复运行相关测试。
- 最后再跑完整测试套件，做一轮 review，然后提交到当前分支。

这种一小步一小步推进的方式，就是经典的 Red-Green-Refactor 循环。

这样做的好处是：测试验证的是功能行为，而不是某段具体实现。只要对外行为没变，内部代码就可以放心调整。

## 它在整个流程里的位置

`implement` 是主构建链条接近尾声的构建步骤，就在 review 之前：

```txt
grill-with-docs -> to-spec -> to-tickets -> implement -> code-review
```

只有在工作已经写成 spec，并且执行顺序已经排好之后，才该使用它。

[to-tickets](https://aihero.dev/skills-to-tickets) 会产出 `implement` 要处理的 tickets，并在每个 ticket 里声明阻塞依赖关系。[tdd](https://aihero.dev/skills-tdd) 则是 `implement` 内部使用的工作方式：先围绕测试边界写测试，再实现代码。

实现完成后，`implement` 会运行自己的 [code-review](https://aihero.dev/skills-code-review) 流程，然后提交改动。如果你不确定该用哪个 skill 或哪条流程，[ask-matt](https://aihero.dev/skills-ask-matt) 会帮你路由。
