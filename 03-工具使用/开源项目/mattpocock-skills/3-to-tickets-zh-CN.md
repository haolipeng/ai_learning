快速开始：

```bash
npx skills add mattpocock/skills --skill=to-tickets
```

```bash
npx skills update to-tickets
```

[源代码](https://github.com/mattpocock/skills/tree/main/skills/engineering/to-tickets)

## 它做什么

`to-tickets` 的作用是：把已经想清楚的工作，拆成一张张可以执行、可以验证、可以交给智能体处理的任务卡。

它可以读取一份计划、spec，或者当前对话，然后把内容拆成一组 **tickets**，并发布到你配置好的 issue tracker。每个 ticket 都是一条端到端的垂直切片，也会说明它依赖哪些其他 tickets。

这样的 ticket 完成后，可以独立演示或验证，所以适合交给智能体处理。

## 什么时候用它

你通过输入 `/to-tickets` 来调用它。智能体不会主动选用这个 skill。

当你已经知道“要做什么”，但还没有把它拆成具体任务时，就该用它。

你可以让它读取当前对话，也可以传入 spec 或 issue 引用；它会先抓取正文和评论。如果变更还没有写成 spec，先用 [to-spec](https://aihero.dev/skills-to-spec) 生成 spec。`to-tickets` 不负责想清楚需求，它负责把已经定下来的方案拆成 tickets。

## 前置条件

`to-tickets` 会发布到你的 issue tracker，所以 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 必须先为这个仓库做好配置。

简单说，它需要先知道：这个仓库的任务要发到哪里，以及发出去后应该打哪些 triage 标签。在真实 tracker 上发布时，它会给 ticket 加上 ready-for-agent 标签。

## 一个产物，两种读法

`to-tickets` 产出的始终是同一组 tickets。区别只在于：你把它们发布到哪里，以及那里的工具能不能自动理解 ticket 之间的依赖关系。

每个 ticket 都会写清楚：它开始之前，哪些 ticket 必须先完成。这就是阻塞依赖关系。

- **本地文件** -> 每个 ticket 是 `.scratch/<feature>/issues/` 下的一个文件，并按“先做阻塞项”的顺序编号。依赖关系只是一段文本，所以你通常按文件顺序从上到下手动处理。
- **真实 tracker（GitHub、Linear）** -> 每个 ticket 是一个 issue，依赖关系会变成 tracker 里的阻塞链接（或 sub-issues）。当某个 ticket 依赖的其他 tickets 都完成后，它就变成可以领取的下一项工作；因此多个智能体可以同时推进不同的未阻塞 tickets。

所以，“一个产物，两种读法”的意思是：tickets 本身不变；本地文件适合按顺序人工处理，真实 tracker 则可以利用阻塞关系来支持并行执行。

## 垂直切片，不是水平切片

整个 skill 的关键就在这一区分上。

**水平**切片，是按技术层拆任务。比如做登录功能：第一张 ticket 只建用户表，第二张只写登录接口，第三张只做登录页面，第四张才补测试。

问题是，前几张 ticket 看起来都完成了，但用户还不能真的登录。团队也很难确认这些层能不能顺利拼在一起。真正的问题往往要等最后一层做完才暴露：字段对不上、接口缺参数、页面流程走不通，或者测试才发现前面几张 ticket 的假设不一致。

**垂直**切片则是先做通一条最小可验证路径。比如先支持“用户输入正确邮箱和密码后能成功登录”这一种场景：它可能暂时不支持忘记密码、验证码、错误提示优化，但会从数据存储、登录接口、页面交互到测试一起打通。

这样一张 ticket 完成后，团队就能看到一个可演示、可验证的结果，而不是只看到一堆还拼不起来的零件。

![水平切片和垂直切片对比图](../../../diagram/horizontal-vs-vertical-slices/horizontal-vs-vertical-slices.svg)

图中左侧展示水平切片如何推迟集成风险；右侧展示垂直切片如何先跑通一条最小登录路径。

有时，在正式做功能之前，先做一点准备工作会更划算。

这叫预重构（prefactoring）：先把代码整理到更容易改的状态，再开始真正的功能改动。拆分之前，`to-tickets` 会寻找这类机会，并把这些准备工作排在前面。

然后它会围绕拆分结果向你追问：颗粒度是否合适、阻塞依赖关系是否正确、哪些应该合并或拆开。只有确认之后，它才会发布任何东西，并且会先发布阻塞项，这样每个 ticket 的 "Blocked by" 都能引用到真实 ticket。

## 大范围重构例外

有一种情况会打破垂直切片规则：**大范围重构**。

比如你要把一个被全仓库使用的字段改名。这个改动本身很机械，但影响范围很大：很多文件可能会同时坏掉。这时，很难让某个垂直切片独立完成并保持 CI 绿色。

面对这种情况，`to-tickets` 不会要求你一次性改完。它会把工作拆成更稳妥的三步：

- 先把新写法加进去，但先不删旧写法。
- 再一批一批把旧调用改成新写法，每一批都是一个 ticket。因为旧写法还在，没改到的地方也不会坏。
- 等所有地方都迁完了，再删掉旧写法。

如果连这些批次都无法单独保持绿色，它们会共享一个集成分支，并共同阻塞最后的“集成并验证” ticket。只有到了这个最终 ticket，才承诺整体保持绿色。

## 它在整个流程里的位置

`to-tickets` 是主构建链条中的一步：

```txt
grill-with-docs -> to-spec -> to-tickets -> implement -> code-review
```

它接收 [to-spec](https://aihero.dev/skills-to-spec) 产出的 spec，把其中的 user stories 切成 tickets，再交给 [implement](https://aihero.dev/skills-implement) 逐张实现。

执行 tickets 时，每处理一张 ticket，就开启一个新的上下文。不要把上一张 ticket 的上下文带到下一张里。
