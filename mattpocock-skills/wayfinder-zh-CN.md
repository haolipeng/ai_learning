快速开始：

```bash
npx skills add mattpocock/skills --skill=wayfinder
```

```bash
npx skills update wayfinder
```

[源代码](https://github.com/mattpocock/skills/tree/main/skills/engineering/wayfinder)

## 它做什么

`wayfinder` 处理的是大到一个智能体会话承载不下、而且路线还被迷雾包住的工作：从现在到目标该怎么走，还看不清。它会把这类工作绘制成一张由 **决策 tickets** 组成的 **共享地图**，放到你的 issue tracker 上，然后一次解决一个决策，直到路线清楚为止。它**只规划，不执行**：每张 ticket 解决的都是一个决策，也就是一个需要定下来的问题，而不是一段要动手构建的功能切片。等到有人可以去构建之前已经没有问题需要决定，这张地图就完成了。所以它产出的是决策，不是交付物。

## 什么时候用它

你通过输入 `/wayfinder` 来调用它。智能体不会主动选用这个 skill。

当一件事 **超过一个智能体会话能承载的范围**，并且通往 **目的地** 的路线仍然模糊时，就该用它：你能感觉到工作的轮廓，但还写不出 spec 或计划。如果要把一段 *已经清楚* 的讨论变成 spec，请使用 [to-spec](https://aihero.dev/skills-to-spec)；如果要把一个已经理解清楚的计划切成可构建的 tickets，请使用 [to-tickets](https://aihero.dev/skills-to-tickets)。Wayfinder 位于它们的上游：当迷雾太重，还不能直接写 spec 时，就先运行它。

## 前置条件

地图和它的 tickets 会放在仓库的 issue tracker 上，所以 wayfinder 需要 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 建好的 tracker 连接配置。这个配置会生成一个 "Wayfinding operations" 小节，说明在 GitHub、GitLab 或 local-markdown 中，地图、子 tickets、阻塞关系和前沿队列查询分别如何表达。如果没有这份文档，wayfinder 会默认使用 local-markdown 地图。

## 地图是索引，迷雾是前沿

**地图** 是一个 `wayfinder:map` issue，它的 tickets 是它的子 issues。整个团队只需要盯住这一条共享 URL。它是 **索引，不是仓库**：每个决策只存在于一个地方，也就是它自己的 ticket；地图只做摘要和链接，不重复记录完整内容。一个会话会先用低分辨率加载地图，需要时再放大到具体 ticket。

活跃 tickets 之外，就是 **战争迷雾**：你能感觉到后面会有一些决策，但现在还没办法准确界定。判断某个东西应该成为 ticket，还是仍然属于迷雾，标准不是你现在能不能回答它，而是你现在能不能把问题说清楚。解决一个 ticket 会清掉它前方的一片迷雾，把现在已经能说清楚的问题转化为新的 tickets。**前沿队列** 是那些开放、没有被阻塞、也还没人领取的 tickets，也就是已知区域的边缘。issue tracker 的原生阻塞关系会把这条边缘可视化出来，所以你不用打开地图，也能看到哪些 ticket 可以领取。迷雾只会朝 **目的地** 的方向聚集；超过目的地的工作会被判定为 **超出范围**，直接关闭，不会再转化成 ticket。

每张 ticket 都是 **HITL**（human in the loop，人在回路中，例如追问、原型）或 **AFK**（智能体可以独立处理，例如研究）。HITL ticket 只能通过实时交流解决，所以智能体永远不会自问自答。研究仍然是一张真实 ticket：它是一个共享阻塞项，下游决策要依赖它。但因为它是 AFK，一个会话不会停下来读研究材料；它会启动一个 `/research` **子智能体** 并行处理这张 ticket，让前沿队列保持快速推进，并把研究结果记录到一个临时的 `research/<name>` 分支上。

## 它正常工作的样子

- 第一件事是命名 **目的地**，而且必须在任何 ticket 出现之前完成，因为目的地决定了每张 ticket 的范围。
- 一张地图就是一个 `wayfinder:map` issue；tickets 是它的子 issues，并且用 **名字** 引用，而不是裸写 `#42`。
- 一个会话 **最多解决一张 ticket**（研究 tickets 除外），把答案记录成 resolution comment，关闭 ticket，并在 *Decisions so far* 里追加一行指针。
- 如果开场追问发现 **没有迷雾**，它会停下来告诉你：这趟旅程足够小，不需要建地图。

## 它适合放在哪

`wayfinder` 是处理大想法的 **入口匝道**：如果一件事太大、太模糊，没办法一次写成 spec，它会先生成一张已经清理过的决策地图，然后再接入主构建流程。当迷雾被推开、路线已经清楚时，就把它交给 [to-spec](https://aihero.dev/skills-to-spec)，安排多会话构建；如果最后发现事情其实很小，也可以直接实现。它依靠 [grilling](https://aihero.dev/skills-grilling) 和 [domain-modeling](https://aihero.dev/skills-domain-modeling) 来解决单个 tickets，也会在需要时使用 [prototype](https://aihero.dev/skills-prototype) 和 [research](https://aihero.dev/skills-research)。如果你不确定该用哪个 skill 或哪条流程，[ask-matt](https://aihero.dev/skills-ask-matt) 会帮你路由。
