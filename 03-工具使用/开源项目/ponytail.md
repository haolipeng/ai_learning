````
# Ponytail：给 AI 生成的代码做“减法”，解决过度设计与代码膨胀

随着 Codex、Claude Code、Cursor 等 AI 编程工具越来越成熟，“让 AI 把功能写出来”已经不是一件困难的事情。

真正开始困扰程序员的，反而是另一个问题：

> **AI 写得太多了。**

一个原本几十行就能完成的功能，AI 可能为了扩展性、通用性以及“未来可能存在的需求”，增加大量当前并不需要的抽象。

代码确实能运行，测试也能通过。

但项目越来越复杂，程序员越来越难理解。

[Ponytail](https://github.com/dietrichgebert/ponytail) 正是针对这个问题出现的一个项目。

它的目标并不是让 AI **写更多代码**，而是反过来约束 AI：

> **少写代码、少做抽象、少造轮子、不要为不存在的未来需求设计系统。**

---

# 一、AI 编程时代的新问题：代码不是写不出来，而是写得太多

Codex、Claude Code、Cursor 等 AI 编程工具已经可以快速完成大量编码工作。现在的问题逐渐从：

> **“这个功能怎么写？”**

变成：

> **“AI 为什么写了这么多？”**

例如，一个原本只需要 **“从配置文件读取数据库地址并启动服务”** 的需求，AI 可能为了所谓的“扩展性”，额外设计出配置接口、配置加载器、配置工厂、默认实现以及多层转换逻辑。

原本可能只需要：

```go
cfg, err := LoadConfig("config.yaml")
if err != nil {
    return err
}

server.Start(cfg)
```

最后却变成：

```text
ConfigProvider
      ↓
ConfigLoader
      ↓
ConfigFactory
      ↓
DefaultConfigProvider
      ↓
ServerConfigAdapter
      ↓
server.Start()
```

这些代码未必是错的，甚至看起来“设计得很好”，但如果当前需求只有一种配置文件、一个 Server，那么这些抽象并没有解决真实问题，反而增加了代码阅读和维护成本。

最终很容易形成这样的循环：

```text
AI 生成代码
      ↓
代码量和抽象增加
      ↓
理解与维护成本增加
      ↓
程序员更加依赖 AI
      ↓
AI 又生成更多代码
```

**Ponytail 想解决的正是这个问题：约束 AI 的过度设计，让代码回归当前真实需求。**

---

# 二、Ponytail 是什么？怎么使用？

Ponytail 可以理解成：

> **一个专门约束 AI 过度设计的 Coding Skill / Ruleset。**

它并不是传统的静态代码扫描器。

它关注的重点也不是：

```text
有没有空指针？
有没有 SQL 注入？
有没有内存泄漏？
```

而是：

```text
这段代码真的需要存在吗？

这个抽象现在有实际价值吗？

标准库是不是已经实现了？

这个 helper 是不是多余的？

这个 Interface 是不是只有一个实现？

这个字段是不是保存了重复的信息？

这个“未来可能需要”的功能真的需要现在实现吗？
```

所以 Ponytail 的核心思想可以浓缩成一句话：

> **代码的价值不在于写了多少，而在于为了实现需求，最少需要多少。**

Ponytail 提供了下面几个核心命令：

| 命令 | 作用 |
| --- | --- |
| `/ponytail [lite \| full \| ultra \| off]` | 设置 Ponytail 的约束强度，或查看当前级别 |
| `/ponytail-review` | Review 当前 diff，寻找本次修改中的过度设计 |
| `/ponytail-audit` | Audit 整个代码仓库，而不仅仅是当前 diff |
| `/ponytail-debt` | 收集之前暂缓处理的问题，形成技术债清单 |
| `/ponytail-gain` | 查看 Ponytail benchmark 中减少代码、成本等收益 |
| `/ponytail-help` | 查看命令帮助 |

其中 `/ponytail` 可以控制约束强度：

```text
/ponytail lite
/ponytail full
/ponytail ultra
/ponytail off
```

可以简单理解为：

```text
lite
 ↓
轻度克制

full
 ↓
正常的完整约束

ultra
 ↓
极度克制，强烈反对过度设计

off
 ↓
关闭 Ponytail
```

对于日常开发，可以使用 `full`。

如果正在让 AI 大规模重构、生成新模块，担心它增加大量不必要的抽象，则可以考虑：

```text
/ponytail ultra
```

实际开发中非常重要的一个命令是：

```text
/ponytail-review
```

它针对当前 Git diff 进行检查。

也就是说，你刚刚让 AI 实现完一个功能之后，可以立即让 Ponytail 回头检查：

> **刚才这些代码里，有多少其实不应该写？**

例如：

```text
delete:
Registry.SendMessage 没有任何调用者，
只转发给 SendMessageWithTimeout(..., 0)。

替代：无。
```

或者：

```text
shrink:
CLI 的私有 config 完整复制 server.Config，
随后又逐字段搬运。

替代：
直接将 flags 绑定到 server.Config。
```

如果想检查的不只是本次修改，而是整个项目，则可以使用：

```text
/ponytail-audit
```

它更适合接手旧项目、AI 已经开发了一段时间的项目，或者大型重构之前使用。

因此可以简单记成：

```text
/ponytail-review
        ↓
检查这一次修改

/ponytail-audit
        ↓
检查整个项目
```

---

# 三、理解 Ponytail 的核心：代码问题分类标签

Ponytail Review 最有价值的地方之一，是它并不会简单告诉你：

> “这里代码不好。”

而是会进一步对问题进行分类。

实际结果中经常会看到：

```text
delete
shrink
yagni
stdlib
native
```

这些标签代表了不同的“代码减法策略”。

## 1. `delete`：这段代码根本不需要存在

`delete` 是最直接的一类：

> **这部分代码没有足够的存在价值，可以直接删除。**

例如：

```text
delete:

Registry.SendMessage 没有任何调用者，
只转发给 SendMessageWithTimeout(..., 0)。

替代：无。
```

代码可能是：

```go
func (r *Registry) SendMessage(msg Message) error {
    return r.SendMessageWithTimeout(msg, 0)
}
```

但整个项目没有任何地方调用 `SendMessage()`。

那么最好的重构方案不是：

```text
优化 SendMessage
```

也不是：

```text
给 SendMessage 补测试
```

而是：

```text
删除 SendMessage
```

类似的还有一种非常典型的情况：

```text
delete:

部署测试大量断言配置文件和 shell 脚本
“包含某个字符串”，只会锁死文本写法。

替代：
保留 bash -n、可执行权限和真正的 smoke test。
```

这类测试虽然增加了代码量和测试数量，却没有真正验证系统行为。

所以 `delete` 最核心的判断标准是：

> **如果删除这段代码，系统真正需要的行为有没有发生变化？**

如果没有，它就值得被删除。

---

## 2. `shrink`：功能需要，但实现得太胖了

`shrink` 和 `delete` 不一样。

它并不是说：

> 这个功能不需要。

而是：

> **这个功能需要，但根本不需要这么复杂。**

例如：

```text
shrink:

CLI 的私有 config 完整复制 server.Config，
随后又逐字段搬运。

替代：
直接将 flags 绑定到 server.Config。
```

假设代码存在：

```go
type CLIConfig struct {
    DatabaseURL string
    APIToken    string
    Port        int
}

type ServerConfig struct {
    DatabaseURL string
    APIToken    string
    Port        int
}
```

然后又进行一次转换：

```go
serverConfig := ServerConfig{
    DatabaseURL: cliConfig.DatabaseURL,
    APIToken:    cliConfig.APIToken,
    Port:        cliConfig.Port,
}
```

这里的问题不是 Config 不应该存在。

而是：

```text
CLIConfig
    ↓
字段复制
    ↓
ServerConfig
```

这一层没有提供足够的价值。

因此 Ponytail 会建议收缩实现。

另外一个例子：

```text
shrink:

策略 payload 先 json.Valid 再 json.Unmarshal，
做了两次完整解析。

替代：
只检查 json.Unmarshal 及结果是否为 nil。
```

原来的逻辑可能是：

```go
if !json.Valid(data) {
    return err
}

if err := json.Unmarshal(data, &payload); err != nil {
    return err
}
```

既然 `json.Unmarshal` 本身就可以发现非法 JSON，那么第一次 `json.Valid` 就可能是重复工作。

所以：

```text
delete = 整块没有价值，删除

shrink = 功能有价值，但实现可以更小
```

---

## 3. `yagni`：不要为了“以后可能需要”提前写代码

`YAGNI` 全称：

> **You Aren't Gonna Need It**

它是 Ponytail 非常核心的思想之一。

例如：

```text
yagni:

databaseResource 只有 *pgxpool.Pool 一个实现，
openDatabase 也只是单行转发。

替代：
字段直接使用 *pgxpool.Pool，
调用 storage.OpenPostgreSQL。
```

项目现在明明只有 PostgreSQL，但代码可能已经开始设计：

```go
type DatabaseResource interface {
    // ...
}

type PostgreSQLResource struct {
    // ...
}
```

为什么？

通常理由是：

> “以后可能需要支持其他数据库。”

于是继续出现更多围绕“多数据库”的抽象。

问题在于：

**现在根本没有其他数据库的需求。**

YAGNI 的观点是：

```text
今天只有 PostgreSQL
        ↓
那就把 PostgreSQL 写清楚
        ↓
以后真的出现第二种数据库
        ↓
再根据真实需求抽象
```

而不是：

```text
今天只有 PostgreSQL
        ↓
预测以后可能支持 MySQL
        ↓
提前设计抽象
        ↓
未来几年一直只有 PostgreSQL
```

另一个典型例子是：

```text
yagni:

canceled 状态存在于常量、数据库约束和终态判断中，
却没有任何生产转换入口。

替代：
删除，等真正实现取消功能时再加。
```

这种代码就是典型的：

> **功能还不存在，但代码已经提前给它留好了位置。**

AI Coding 特别容易出现这种问题，因为 AI 很擅长想象：

```text
未来可能……
为了扩展性……
考虑到以后……
为了更加通用……
```

这些话后面往往就跟着更多代码。

---

## 4. `stdlib`：标准库已经有了，不要自己造轮子

例如 Ponytail 给出的建议：

```text
stdlib:

三个手写去重 helper 可由
slices.Sort、
slices.Compact、
slices.DeleteFunc
和 slices.Contains 替代。
```

项目可能自己实现：

```go
func contains(items []string, target string) bool {
    for _, item := range items {
        if item == target {
            return true
        }
    }

    return false
}
```

如果当前 Go 版本已经提供成熟的标准库能力，那么 Ponytail 会问：

> **为什么还要自己维护一份？**

因为自己实现意味着：

```text
自己实现
+
自己测试
+
自己维护
+
别人阅读
+
潜在 Bug
```

而标准库：

```go
slices.Contains(items, target)
```

程序员看到基本立即知道它在做什么。

因此 `stdlib` 的核心思想就是：

> **标准库能够清晰解决的问题，优先使用标准库。**

减少的不仅是代码量，也是整个团队的认知成本。

---

## 5. `native`：语言或框架本身已经支持

`native` 和 `stdlib` 比较相似，但侧重点有所不同。

例如：

```text
native:

删除手工 HTTP method 判断、
路径前缀裁剪和层层路由分发。

替代：
使用 Go http.ServeMux 的 method pattern
与 Request.PathValue。
```

原来的代码可能自己处理：

```go
if r.Method == "GET" {
    if strings.HasPrefix(r.URL.Path, "/users/") {
        // ...
    }
}
```

也就是自己完成：

```text
HTTP Method 判断
        ↓
路径解析
        ↓
路由分发
        ↓
参数提取
```

但现代 Go HTTP 路由本身已经可以：

```go
mux.HandleFunc("GET /users/{id}", handler)
```

Handler 里直接：

```go
id := r.PathValue("id")
```

既然语言或者框架已经提供这种能力，就没有必要再维护自己的半套路由系统。

因此：

> **`native` 强调优先使用语言、框架、运行时已有的原生能力。**

---

## 五种标签放在一起理解

最终可以把 Ponytail 的这套分类总结成：

| 标签 | Ponytail 在问什么？ | 建议动作 |
| --- | --- | --- |
| `delete` | 这段代码真的需要存在吗？ | 删除 |
| `shrink` | 真的需要这么多代码才能实现吗？ | 收缩实现 |
| `yagni` | 这个“未来需求”现在真的存在吗？ | 暂时不做 |
| `stdlib` | 标准库是不是已经实现了？ | 使用标准库 |
| `native` | 语言/框架是不是原生支持？ | 使用原生能力 |

当 Ponytail 看到一段复杂代码时，它的思考方式可以简单理解成：

```text
                一段复杂代码
                     │
                     ▼
              真的需要存在吗？
                     │
             ┌───────┴───────┐
             │               │
            不需要            需要
             │               │
             ▼               ▼
           delete       能不能更简单？
                             │
                             ▼
                           shrink

然后继续检查：

是不是为了未来需求？
        ↓
      yagni

是不是标准库已经有了？
        ↓
      stdlib

是不是语言/框架已经支持？
        ↓
      native
```

这实际上已经不仅仅是一套 Ponytail 标签。

它本身就是一套非常实用的 **Code Review 思维框架**。

---

# 四、Ponytail 对 AI 编程最大的价值，不只是“少写代码”

很多人第一次看到 Ponytail 可能会觉得：

> 不就是帮我删代码吗？

实际上它真正重要的价值，是**降低项目的认知复杂度**。

例如 AI 写了：

```text
HTTP Handler
     ↓
UserService
     ↓
UserManager
     ↓
UserProvider
     ↓
UserRepository
     ↓
PostgreSQL
```

每层可能只有：

```go
return next.DoSomething()
```

代码完全正确，测试也完全通过。

但程序员为了理解一次请求，却需要：

```text
打开 Handler
      ↓
跳 Service
      ↓
跳 Manager
      ↓
跳 Provider
      ↓
跳 Repository
      ↓
终于找到 SQL
```

如果 Ponytail 最终把它收缩成：

```text
HTTP Handler
     ↓
Repository
     ↓
PostgreSQL
```

真正减少的并不只是几百行代码，而是程序员脑子里的：

```text
5 个概念
3 个接口
4 层调用
若干数据转换
```

**减少代码只是表象，减少程序员理解系统所需要维护的“心智模型”才是关键。**

---

# 五、推荐一种 Ponytail + AI Coding 工作流

以前我们使用 AI Coding Agent，通常是：

```text
提出需求
   ↓
AI 分析
   ↓
AI 写代码
   ↓
AI 跑测试
   ↓
测试通过
   ↓
提交代码
```

引入 Ponytail 之后，可以增加一个专门的“做减法”阶段：

```text
提出需求
        ↓
AI 分析需求
        ↓
AI 实现
        ↓
运行测试
        ↓
/ponytail-review
        ↓
分析
delete / shrink / yagni
stdlib / native
        ↓
执行合理的删减
        ↓
再次运行测试
        ↓
人工阅读关键代码
        ↓
提交
```

这里有一个非常重要的原则：

> **Ponytail 的结果不应该无脑接受。**

比如它说：

```text
yagni:
删除某个 Interface
```

程序员仍然需要判断：

> 这个 Interface 是真的没有必要，还是它背后存在 Ponytail 不知道的真实业务规划？

因此 Ponytail 更适合作为一个 **Review Agent**，而不是最终决策者。

---

# 六、程序员还可以反过来利用 Ponytail 学习代码设计

Ponytail 还有一种很有价值的使用方式：

**不要让 AI 看到 Review 结果以后立即修改代码。**

例如执行：

```text
/ponytail-review
```

之后继续要求 AI：

```text
不要修改代码。

逐条解释每个建议：

1. 原代码解决了什么问题
2. 为什么认为它是过度设计
3. 删除后系统结构发生什么变化
4. 什么情况下这段设计反而应该保留
5. 对应的软件工程原则是什么
```

这时候 Ponytail 就从：

> **代码删除器**

变成了：

> **
````





```
func (c *Client) Connect() error {
	return c.ConnectContext(context.Background())
}

// ConnectContext connects, sends the client protocol version, and reads the
// server acknowledgement. Cancellation closes an in-flight connection.
func (c *Client) ConnectContext(ctx context.Context) error {
}
```

上述中，  - Connect()只是调用 ConnectContext(context.Background())。生产代码使用 ConnectContext(ctx)，能响应超时和取消；无 Context 版本既重复，又容易让调用者意外创建无法主动取消的操作。







