# OpenSpec 学习指南

> 适合技术分享和团队学习的完整教程。  
> 速查请看同目录 `OpenSpec-Workflow-Guide.md`；个人实践问答见 `openspec工具使用.md`。

## 目录

1. [什么是 OpenSpec](#什么是-openspec)
2. [为什么需要 OpenSpec](#为什么需要-openspec)
3. [核心概念](#核心概念)
4. [目录结构](#目录结构)
5. [完整工作流程](#完整工作流程)
6. [实战示例](#实战示例)
7. [常用命令](#常用命令)
8. [最佳实践](#最佳实践)

---

## 什么是 OpenSpec

**OpenSpec 是一个规范驱动的开发流程框架**，帮助团队：

- 📝 **提案先行**：在写代码前先写清楚要做什么
- 🔄 **变更可追溯**：每个功能从提案到实施到归档都有完整记录
- ✅ **任务可验证**：将大功能拆解为可验证的小任务
- 📚 **知识可沉淀**：系统真相（specs）与历史记录（archive）分离存储

**一句话总结**：OpenSpec 让你的项目开发像提交 Pull Request 一样规范和可追溯。

---

## 为什么需要 OpenSpec

### 传统开发流程的痛点

```
❌ 没有提案 → 直接写代码 → 功能设计不清晰
❌ 需求口头沟通 → 容易遗漏和误解
❌ 任务不明确 → 不知道进度到哪里了
❌ 文档和代码脱节 → 文档过时无人维护
```

### OpenSpec 的解决方案

```
✅ Proposal（提案） → 明确 Why/What/Impact
✅ Design（设计） → 技术方案和架构决策
✅ Tasks（任务） → 可执行的任务清单
✅ Specs（规范） → 系统功能的完整需求
✅ Archive（归档） → 历史变更的完整记录
```

---

## 核心概念

### 1. 三个核心目录

OpenSpec 使用三个目录来组织项目文档：

| 目录 | 含义 | 用途 | 类比 |
|------|------|------|------|
| **`changes/`** | 工作区 | 正在进行的变更 | 📝 你的工作台 |
| **`specs/`** | 真相源 | 系统当前的完整规范 | ✅ 项目的"真相" |
| **`archive/`** | 历史库 | 已完成变更的历史记录 | 📚 项目的"日记本" |

#### 形象理解

```
changes/  = "我正在做什么？"     （工作进行时）
specs/    = "系统现在是什么样？"  （当前状态）
archive/  = "我们做过什么？"      （历史记录）
```

### 2. 两种 Spec 文件

这是 OpenSpec 最容易混淆的地方！

#### Delta Spec（增量规范）

**位置**：`changes/[change-id]/specs/[capability]/spec.md`

**特点**：包含 `ADDED`、`MODIFIED`、`REMOVED` 标记

**用途**：描述"这次变更"要改什么

```markdown
## ADDED Requirements

### Requirement: HTTP API Server
The system SHALL provide an HTTP API server...

## MODIFIED Requirements

### Requirement: Session Tracking
The system SHALL track up to 100k sessions (previously 50k)...

## REMOVED Requirements

### Requirement: Old Feature
**Reason**: Deprecated
```

#### Complete Spec（完整规范）

**位置**：`specs/[capability]/spec.md`

**特点**：无 `ADDED`/`MODIFIED` 标记，纯净的功能规范

**用途**：描述"系统当前"有什么功能

```markdown
# Capability: HTTP API

### Requirement: HTTP API Server
The system SHALL provide an HTTP API server...

### Requirement: Session Tracking
The system SHALL track up to 100k sessions...
```

**重要**：Complete Spec 是通过 `archive` 命令自动从 Delta Spec 合并生成的！

### 3. 四个核心文件

每个变更（change）包含四个文件：

| 文件 | 作用 | 回答的问题 |
|------|------|-----------|
| **`proposal.md`** | 提案 | 为什么做？做什么？影响是什么？ |
| **`design.md`** | 设计 | 怎么做？技术方案是什么？ |
| **`tasks.md`** | 任务清单 | 具体要做哪些事？进度如何？ |
| **`specs/*/spec.md`** | 规范增量 | 功能需求是什么？如何验证？ |

---

## 目录结构

### 完整的目录树

```
project-root/
├── openspec/
│   ├── project.md                    # 项目元信息
│   ├── AGENTS.md                     # AI 助手指南
│   │
│   ├── changes/                      # 📝 工作区
│   │   ├── add-control-plane-api/    # 正在进行的变更
│   │   │   ├── proposal.md           # 提案文档
│   │   │   ├── design.md             # 设计文档
│   │   │   ├── tasks.md              # 任务清单
│   │   │   └── specs/                # 规范增量（Delta）
│   │   │       ├── control-plane-api/
│   │   │       │   └── spec.md       # ADDED/MODIFIED 需求
│   │   │       └── policy-management/
│   │   │           └── spec.md
│   │   │
│   │   └── archive/                  # 📚 历史库
│   │       ├── 2025-10-30-implement-session-tracking/
│   │       │   ├── proposal.md       # 已完成的提案
│   │       │   └── tasks.md          # 全部 [x] 完成
│   │       ├── 2025-10-30-implement-policy-matching/
│   │       └── README.md             # 归档总结
│   │
│   └── specs/                        # ✅ 真相源（完整规范）
│       ├── session-tracking/
│       │   └── spec.md               # 纯净的完整规范
│       ├── policy-matching/
│       │   └── spec.md
│       └── control-plane-api/
│           └── spec.md               # archive 后自动生成
│
└── src/                              # 实际代码
    └── ...
```

### 目录演变过程

```
阶段 1: Proposal（提案）
changes/add-api/
├── proposal.md     ← 创建
├── design.md       ← 创建
├── tasks.md        ← 创建
└── specs/api/
    └── spec.md     ← 创建（Delta，有 ADDED 标记）

阶段 2: Apply（实施）
changes/add-api/
├── proposal.md     ← 不变
├── design.md       ← 不变
├── tasks.md        ← 更新进度 [ ] → [x]
└── specs/api/
    └── spec.md     ← 不变

阶段 3: Archive（归档）
┌─────────────────────────────────────┐
│ 移动文档到 archive/                 │
└─────────────────────────────────────┘
archive/2025-10-30-add-api/
├── proposal.md     ← 移动到这里
├── design.md       ← 移动到这里
└── tasks.md        ← 移动到这里（全部完成）

┌─────────────────────────────────────┐
│ 合并增量到 specs/                   │
└─────────────────────────────────────┘
specs/api/
└── spec.md         ← 新建（Complete，无 ADDED 标记）

changes/add-api/    ← 删除整个目录
```

---

## 完整工作流程

### 三阶段流程图

```
┌──────────────┐      ┌──────────────┐      ┌──────────────┐
│   PROPOSAL   │ ───> │     APPLY    │ ───> │   ARCHIVE    │
│   (提案)     │      │    (实施)     │      │   (归档)     │
└──────────────┘      └──────────────┘      └──────────────┘
   创建文档              编写代码              合并规范
   定义需求              更新任务              移动到历史
```

### 阶段 1: PROPOSAL（提案）

#### 目标

创建变更提案，明确要做什么、为什么做、怎么做。

#### 操作

```bash
# 方式 1: 使用 OpenSpec 命令（推荐）
/openspec:proposal add-control-plane-api

# 方式 2: 手动创建目录和文件
mkdir -p openspec/changes/add-control-plane-api/specs
cd openspec/changes/add-control-plane-api
touch proposal.md design.md tasks.md
```

#### 创建的文件

**1. proposal.md** - 提案文档

```markdown
# Proposal: Add Control Plane API

## Why（为什么）
- 需要提供 REST API 让外部系统管理策略
- 当前只能通过命令行操作，不便于自动化

## What（做什么）
- 实现 HTTP API 服务器（Gin 框架）
- 提供策略 CRUD 端点
- 提供统计查询端点
- 提供健康检查端点

## Impact（影响）
- 新增 API 服务器组件（~3000 行代码）
- 需要新增配置项：API_PORT, API_ENABLE
- 对现有数据平面无影响（只读访问）

## Timeline
- 预计时间：3 周
- 里程碑：
  - Week 1: API 框架和基础端点
  - Week 2: 策略管理端点
  - Week 3: 测试和文档

## Risks
- API 并发性能需要压测
- 需要考虑认证授权（后续功能）
```

**2. design.md** - 设计文档

```markdown
# Design: Control Plane API

## Architecture

┌─────────────┐
│  REST API   │  ← 新增组件
│  (Gin)      │
└──────┬──────┘
       │
       ├─> Policy Manager  ← 现有组件
       ├─> Stats Collector ← 现有组件
       └─> Config Manager  ← 现有组件

## Technical Decisions

### 1. Web 框架选择
- **选择**：Gin
- **原因**：轻量、性能好、社区活跃
- **备选**：Echo、Chi

### 2. 并发控制
- **选择**：sync.RWMutex
- **原因**：读多写少场景
- **考虑**：未来可升级到 sync.Map

### 3. API 版本化
- **选择**：URL 路径版本化 (/api/v1/...)
- **原因**：简单明确，易于路由
- **备选**：Header 版本化

## API Design

### 策略管理
- POST   /api/v1/policies      # 创建策略
- GET    /api/v1/policies      # 列出策略
- GET    /api/v1/policies/:id  # 获取策略
- PUT    /api/v1/policies/:id  # 更新策略
- DELETE /api/v1/policies/:id  # 删除策略

### 统计查询
- GET /api/v1/stats            # 总体统计
- GET /api/v1/stats/packets    # 数据包统计
- GET /api/v1/stats/sessions   # 会话统计

## Performance Goals
- API 响应延迟: < 10ms
- 并发处理: 1000 req/s
- 数据平面影响: < 1% CPU
```

**3. tasks.md** - 任务清单

```markdown
# Tasks: Add Control Plane API

## Phase 1: Framework Setup
- [ ] Install Gin framework
- [ ] Create API server structure
- [ ] Add configuration loading
- [ ] Implement graceful shutdown

## Phase 2: Core Endpoints
- [ ] Implement health check endpoint
- [ ] Implement status endpoint
- [ ] Add middleware (logging, CORS)
- [ ] Add error handling

## Phase 3: Policy Management
- [ ] Implement POST /policies
- [ ] Implement GET /policies
- [ ] Implement GET /policies/:id
- [ ] Implement PUT /policies/:id
- [ ] Implement DELETE /policies/:id
- [ ] Add input validation
- [ ] Add policy syntax checking

## Phase 4: Stats Endpoints
- [ ] Implement GET /stats
- [ ] Implement GET /stats/packets
- [ ] Implement GET /stats/sessions
- [ ] Add stats caching

## Phase 5: Testing
- [ ] Unit tests for handlers
- [ ] Integration tests for API
- [ ] Performance tests
- [ ] Load tests (1000 req/s)

## Phase 6: Documentation
- [ ] API documentation (OpenAPI/Swagger)
- [ ] Usage examples
- [ ] Configuration guide
```

**4. specs/control-plane-api/spec.md** - 规范增量

```markdown
# Capability: Control Plane API

## ADDED Requirements

### Requirement: HTTP API Server
The system SHALL provide an HTTP API server that:
- Listens on a configurable port (default 8080)
- Uses the Gin web framework
- Supports graceful shutdown
- Logs all API requests

#### Scenario: API server starts successfully
- GIVEN the API is enabled in configuration
- WHEN the agent starts
- THEN the HTTP server MUST listen on the configured port
- AND log "API server started on :8080"

#### Scenario: API server handles concurrent requests
- GIVEN the API server is running
- WHEN 100 concurrent requests are sent
- THEN all requests MUST complete within 1 second
- AND no request MUST fail

### Requirement: Policy Management Endpoints
The API SHALL provide RESTful endpoints for policy management:
- POST /api/v1/policies - Create policy
- GET /api/v1/policies - List policies
- GET /api/v1/policies/:id - Get policy
- PUT /api/v1/policies/:id - Update policy
- DELETE /api/v1/policies/:id - Delete policy

#### Scenario: Create policy via API
- GIVEN a valid policy JSON payload
- WHEN POST /api/v1/policies is called
- THEN return 201 Created
- AND return the created policy with ID
- AND the policy MUST be applied to the data plane

#### Scenario: Invalid policy rejected
- GIVEN an invalid policy JSON
- WHEN POST /api/v1/policies is called
- THEN return 400 Bad Request
- AND return error details
- AND no policy MUST be created

### Requirement: Health Check
The API SHALL provide a health check endpoint:
- GET /api/v1/health

#### Scenario: Health check returns OK
- WHEN GET /api/v1/health is called
- THEN return 200 OK
- AND return {"status": "ok", "uptime": <seconds>}
```

#### 验证提案

```bash
# 验证提案格式和完整性
openspec validate add-control-plane-api --strict

# 查看活动变更列表
openspec list

# 输出示例：
# add-control-plane-api  0/25 tasks  (proposed)
```

---

### 阶段 2: APPLY（实施）

#### 目标

按照 tasks.md 逐项实施功能，更新任务完成状态。

#### 操作

```bash
# 方式 1: 使用 OpenSpec 命令
/openspec:apply add-control-plane-api

# 方式 2: 手动实施
# 1. 查看任务清单
cat openspec/changes/add-control-plane-api/tasks.md

# 2. 编写代码
vim src/agent/api/server.go

# 3. 更新任务状态
# 将 [ ] 改为 [x]
```

#### 实施过程

```markdown
# tasks.md 的变化

初始状态（0/25）:
- [ ] Install Gin framework
- [ ] Create API server structure
- [ ] Add configuration loading

实施中（12/25）:
- [x] Install Gin framework          ← 已完成
- [x] Create API server structure    ← 已完成
- [x] Add configuration loading      ← 已完成
- [ ] Implement graceful shutdown
- [ ] Implement health check endpoint

全部完成（25/25）:
- [x] Install Gin framework
- [x] Create API server structure
- [x] Add configuration loading
- [x] Implement graceful shutdown
- [x] Implement health check endpoint
... (所有任务都是 [x])
```

#### 查看进度

```bash
# 查看进度
openspec list

# 输出示例：
# add-control-plane-api  12/25 tasks  (in progress)
#                        ↑
#                        进度实时更新
```

#### 代码实现示例

```go
// src/agent/api/server.go
package api

import (
    "github.com/gin-gonic/gin"
    "net/http"
)

type Server struct {
    router *gin.Engine
    port   string
}

func NewServer(port string) *Server {
    r := gin.Default()

    // Health check endpoint
    r.GET("/api/v1/health", func(c *gin.Context) {
        c.JSON(http.StatusOK, gin.H{
            "status": "ok",
            "uptime": getUptime(),
        })
    })

    // Policy endpoints
    r.POST("/api/v1/policies", createPolicy)
    r.GET("/api/v1/policies", listPolicies)
    r.GET("/api/v1/policies/:id", getPolicy)
    r.PUT("/api/v1/policies/:id", updatePolicy)
    r.DELETE("/api/v1/policies/:id", deletePolicy)

    return &Server{router: r, port: port}
}

func (s *Server) Start() error {
    return s.router.Run(s.port)
}
```

---

### 阶段 3: ARCHIVE（归档）

#### 目标

功能开发完成后，将变更归档并合并规范到系统真相。

#### 前提条件

- ✅ tasks.md 中所有任务都标记为完成 `[x]`
- ✅ 代码已提交
- ✅ 测试已通过

#### 操作

```bash
# 方式 1: 使用 OpenSpec 命令
/openspec:archive add-control-plane-api --yes

# 方式 2: 手动归档
openspec archive add-control-plane-api --yes
```

#### Archive 做了什么

**操作 A: 移动文档到 archive/**

```
之前：
changes/add-control-plane-api/
├── proposal.md
├── design.md
└── tasks.md

之后：
archive/2025-10-30-add-control-plane-api/
├── proposal.md    ← 移动到这里
├── design.md      ← 移动到这里
└── tasks.md       ← 移动到这里
```

**操作 B: 合并 Delta Spec 到 Complete Spec**

```
之前（Delta Spec）:
changes/add-control-plane-api/specs/control-plane-api/spec.md
┌────────────────────────────────────────┐
│ ## ADDED Requirements                  │
│                                        │
│ ### Requirement: HTTP API Server      │
│ The system SHALL...                   │
│                                        │
│ ### Requirement: Policy Endpoints     │
│ The API SHALL...                      │
└────────────────────────────────────────┘

之后（Complete Spec）:
specs/control-plane-api/spec.md
┌────────────────────────────────────────┐
│ # Capability: Control Plane API       │
│                                        │
│ ### Requirement: HTTP API Server      │  ← 无 ADDED 标记
│ The system SHALL...                   │
│                                        │
│ ### Requirement: Policy Endpoints     │
│ The API SHALL...                      │
└────────────────────────────────────────┘
```

**操作 C: 删除 changes/ 中的变更目录**

```
changes/add-control-plane-api/  ← 整个目录被删除
```

#### 归档后的状态

```bash
# 查看活动变更（应该为空）
openspec list
# 输出: No active changes found.

# 查看完整规范
openspec list --specs
# 输出:
# control-plane-api    ← 新增的完整规范
# session-tracking
# policy-matching

# 查看归档历史
ls -la openspec/changes/archive/
# 输出:
# 2025-10-30-add-control-plane-api/
# 2025-10-29-implement-session-tracking/
# ...
```

---

## 实战示例

### 示例 1: 添加新功能（从零开始）

**场景**：为系统添加 FQDN（域名）过滤功能

#### Step 1: 创建提案

```bash
# 使用斜杠命令创建提案
/openspec:proposal add-fqdn-filtering
```

创建的文件结构：

```
openspec/changes/add-fqdn-filtering/
├── proposal.md
├── design.md
├── tasks.md
└── specs/
    └── fqdn-filtering/
        └── spec.md
```

#### Step 2: 编写提案内容

**proposal.md**:

```markdown
# Proposal: Add FQDN Filtering

## Why
- 用户需要基于域名（而非 IP）进行访问控制
- 支持 *.example.com 通配符匹配
- 解决动态 IP 场景（如 CDN）

## What
- 实现 FQDN 映射表（域名 → IP）
- DNS 拦截和学习
- 策略规则支持 FQDN 字段

## Impact
- 新增 FQDN 匹配模块（~2000 行代码）
- 需要拦截 DNS 流量
- 内存占用增加（估计 10MB，10k 域名）
```

**design.md**:

```markdown
# Design: FQDN Filtering

## Data Structure

┌────────────────────┐
│  FQDN Map          │
│  (RCU Hash Table)  │
└─────────┬──────────┘
          │
    ┌─────┴─────┐
    │           │
┌───▼───┐   ┌───▼───┐
│Domain │   │  IP   │
│→ IP   │   │→ FQDN │
└───────┘   └───────┘

## DNS Interception
- Hook DNS responses (port 53)
- Extract A/AAAA records
- Update FQDN → IP mapping

## Policy Matching
1. Extract dst_ip from packet
2. Lookup IP → FQDN mapping
3. Match FQDN against policy rules
4. Apply action (ALLOW/DENY)
```

**tasks.md**:

```markdown
# Tasks: Add FQDN Filtering

- [ ] Define FQDN data structures
- [ ] Implement FQDN hash table (RCU)
- [ ] Add DNS parser
- [ ] Hook DNS response packets
- [ ] Implement FQDN → IP learning
- [ ] Support wildcard matching (*.example.com)
- [ ] Update policy matching logic
- [ ] Add FQDN field to policy API
- [ ] Unit tests for FQDN matching
- [ ] Integration tests
- [ ] Performance tests (10k domains)
- [ ] Documentation
```

#### Step 3: 验证提案

```bash
openspec validate add-fqdn-filtering --strict

# 输出:
# ✅ proposal.md found
# ✅ design.md found
# ✅ tasks.md found
# ✅ specs/ directory found
# ✅ All tasks follow format
# Validation passed!
```

#### Step 4: 实施功能

```bash
/openspec:apply add-fqdn-filtering
```

编写代码并更新任务：

```bash
# 1. 实现 FQDN 数据结构
vim src/ebpf/fqdn.h
# 完成后更新 tasks.md: [x] Define FQDN data structures

# 2. 实现 DNS 解析
vim src/ebpf/dns_parser.c
# 完成后更新 tasks.md: [x] Add DNS parser

# ... 依次完成所有任务
```

#### Step 5: 归档变更

所有任务完成后：

```bash
/openspec:archive add-fqdn-filtering --yes

# 结果:
# ✅ Moved to archive/2025-10-30-add-fqdn-filtering/
# ✅ Merged to specs/fqdn-filtering/spec.md
# ✅ Removed from changes/
```

---

### 示例 2: 修改已有功能

**场景**：优化会话跟踪性能（功能已存在于 specs/）

#### Step 1: 创建优化提案

```bash
/openspec:proposal optimize-session-tracking
```

#### Step 2: 编写增量规范

**specs/session-tracking/spec.md** (Delta):

```markdown
# Capability: Session Tracking

## MODIFIED Requirements

### Requirement: Session Storage
The system SHALL store up to **200k** sessions (previously 100k)
using LRU_HASH map...

**Changes**:
- Increase capacity: 100k → 200k
- Add per-CPU statistics
- Optimize lookup path

## ADDED Requirements

### Requirement: Session Metrics Export
The system SHALL export session metrics via Ring Buffer:
- Total sessions count
- Active sessions per protocol (TCP/UDP/ICMP)
- Session creation/deletion rate

#### Scenario: Export metrics every 10 seconds
- GIVEN the system is running
- WHEN 10 seconds elapsed
- THEN session metrics MUST be exported to ring buffer
- AND user space MUST receive the metrics

## REMOVED Requirements

### Requirement: Session Cleanup Timer
**Reason**: Replaced by LRU automatic eviction
**Migration**: No manual cleanup needed
```

#### Step 3: 实施优化

```bash
/openspec:apply optimize-session-tracking

# 修改代码
vim src/ebpf/session.h    # 增加 map 容量
vim src/ebpf/session.c    # 添加指标导出
vim src/ebpf/stats.c      # 实现 ring buffer

# 更新任务
vim openspec/changes/optimize-session-tracking/tasks.md
```

#### Step 4: 归档并合并

```bash
/openspec:archive optimize-session-tracking --yes
```

**归档后，specs/session-tracking/spec.md 变成**:

```markdown
# Capability: Session Tracking

### Requirement: Session Storage
The system SHALL store up to 200k sessions       ← 已更新
using LRU_HASH map...

### Requirement: Session Metrics Export          ← 已添加
The system SHALL export session metrics...

(Session Cleanup Timer 已删除)                  ← 已移除
```

---

### 示例 3: 回顾性归档（已完成的功能）

**场景**：你已经实现了 5 个功能，但没有使用 OpenSpec，现在想补充文档。

#### Step 1: 为每个功能创建提案（回顾性）

```bash
# 创建已完成功能的提案
mkdir -p openspec/changes/archive

# 功能 1: 会话跟踪
mkdir -p openspec/changes/archive/2025-10-28-implement-session-tracking
cat > openspec/changes/archive/2025-10-28-implement-session-tracking/proposal.md <<EOF
# Proposal: Implement Session Tracking

## Why
需要跟踪网络连接状态

## What
实现基于 eBPF LRU_HASH 的会话跟踪

## Impact
- 支持 100k 并发会话
- 内存占用 ~50MB
EOF

cat > openspec/changes/archive/2025-10-28-implement-session-tracking/tasks.md <<EOF
# Tasks: Implement Session Tracking

- [x] Define session data structure
- [x] Create LRU_HASH map
- [x] Implement session lookup
- [x] Implement session creation
- [x] Implement session timeout
- [x] Add unit tests
- [x] Add integration tests
- [x] Performance testing
EOF
```

#### Step 2: 批量归档

重复为所有已完成功能创建文档，然后：

```bash
# 查看归档
ls -la openspec/changes/archive/
# 输出:
# 2025-10-28-implement-session-tracking/
# 2025-10-29-implement-policy-matching/
# 2025-10-29-implement-policy-enforcement/
# 2025-10-30-implement-stats-reporting/
# 2025-10-30-optimize-dataplane-performance/
```

---

## 常用命令

### OpenSpec CLI 命令

```bash
# 查看活动变更
openspec list
# 输出: add-api  12/25 tasks  (in progress)

# 查看完整规范
openspec list --specs
# 输出: control-plane-api, session-tracking, policy-matching

# 验证提案
openspec validate <change-id> --strict

# 归档变更
openspec archive <change-id> --yes

# 查看帮助
openspec --help
```

### 斜杠命令（在支持的环境中）

```bash
# 创建提案
/openspec:proposal <change-id>

# 实施变更
/openspec:apply <change-id>

# 归档变更
/openspec:archive <change-id>
```

### 手动操作

```bash
# 创建变更目录
mkdir -p openspec/changes/<change-id>/specs

# 查看活动变更
ls -la openspec/changes/

# 查看归档历史
ls -la openspec/changes/archive/

# 查看完整规范
ls -la openspec/specs/

# 验证任务格式
grep -E '^\- \[(x| )\]' openspec/changes/<change-id>/tasks.md

# 统计任务进度
grep -c '^\- \[x\]' openspec/changes/<change-id>/tasks.md
grep -c '^\- \[' openspec/changes/<change-id>/tasks.md
```

---

## 最佳实践

### 1. 命名规范

#### Change ID 命名（kebab-case）

```bash
✅ 推荐:
- add-control-plane-api
- implement-session-tracking
- optimize-dataplane-performance
- fix-memory-leak-in-dns
- update-policy-matching-logic

❌ 避免:
- Add_Control_Plane_API       (不要用下划线)
- addControlPlaneAPI          (不要用驼峰)
- api                         (太模糊)
- feature-123                 (用描述性名称，不是编号)
```

#### 动词前缀

使用明确的动词开头：

| 动词 | 用途 | 示例 |
|------|------|------|
| `add-` | 添加新功能 | add-fqdn-filtering |
| `implement-` | 实现新模块 | implement-dns-parser |
| `update-` | 更新现有功能 | update-api-response-format |
| `optimize-` | 性能优化 | optimize-packet-processing |
| `fix-` | 修复 bug | fix-race-condition-in-stats |
| `refactor-` | 重构 | refactor-policy-engine |
| `remove-` | 删除功能 | remove-deprecated-api |

### 2. 提案编写

#### proposal.md 结构

```markdown
# Proposal: <简短标题>

## Why（为什么）
- 业务需求
- 技术痛点
- 用户反馈

## What（做什么）
- 功能 1
- 功能 2
- 功能 3

## Impact（影响）
- 代码变更范围
- 性能影响
- 兼容性影响
- 配置变更

## Timeline（时间线）
- Week 1: ...
- Week 2: ...

## Risks（风险）
- 风险 1 及缓解措施
- 风险 2 及缓解措施
```

#### 好的 Why 示例

```markdown
## Why

✅ 具体的问题：
- 用户无法通过域名配置访问控制规则
- 当前只支持 IP 地址，但很多服务使用动态 IP（CDN）
- 每次 IP 变化都需要手动更新规则，用户反馈体验差

❌ 模糊的描述：
- 需要支持 FQDN
- 用户要求
```

### 3. 任务拆分

#### 任务粒度

```markdown
✅ 好的任务（可验证、粒度适中）:
- [ ] Define FQDN data structure in fqdn.h
- [ ] Implement FQDN hash table with RCU
- [ ] Add DNS response parser
- [ ] Hook DNS packets in TC egress
- [ ] Unit test for wildcard matching

❌ 不好的任务（太大或太小）:
- [ ] Implement FQDN feature           (太大，无法验证)
- [ ] Add comment to code              (太小，没必要)
- [ ] Fix all bugs                     (不明确)
```

#### 任务分组

```markdown
# Tasks: Add FQDN Filtering

## Phase 1: Data Structure (Week 1)
- [ ] Define fqdn_record_t structure
- [ ] Implement fqdn_map hash table
- [ ] Add RCU synchronization

## Phase 2: DNS Interception (Week 1-2)
- [ ] Implement DNS header parser
- [ ] Extract A/AAAA records
- [ ] Update FQDN → IP mapping

## Phase 3: Policy Integration (Week 2)
- [ ] Add FQDN field to policy struct
- [ ] Update policy matching logic
- [ ] Support wildcard patterns

## Phase 4: Testing (Week 2-3)
- [ ] Unit tests (10 test cases)
- [ ] Integration tests (5 scenarios)
- [ ] Performance tests (10k FQDNs)
- [ ] Load tests (1M packets/s)

## Phase 5: Documentation (Week 3)
- [ ] API documentation
- [ ] Configuration guide
- [ ] Performance tuning guide
```

### 4. 规范编写

#### Requirement 格式

```markdown
### Requirement: <简短标题>
<详细描述，使用 SHALL/MUST/SHOULD>

#### Scenario: <场景名称>
- GIVEN <前置条件>
- WHEN <触发动作>
- THEN <预期结果>
- AND <额外验证>

#### Scenario: <另一个场景>
...
```

#### 示例

```markdown
### Requirement: FQDN Wildcard Matching
The system SHALL support wildcard FQDN patterns using asterisk (*) notation.
Wildcard MUST only appear at the beginning of the domain name (e.g., *.example.com).

#### Scenario: Match subdomain with wildcard
- GIVEN a policy rule with FQDN "*.example.com"
- WHEN a DNS response for "api.example.com" is received
- THEN the FQDN MUST match the rule
- AND packets to the resolved IP MUST be subject to the policy

#### Scenario: Wildcard does not match parent domain
- GIVEN a policy rule with FQDN "*.example.com"
- WHEN a DNS response for "example.com" is received
- THEN the FQDN MUST NOT match the rule

#### Scenario: Invalid wildcard pattern rejected
- GIVEN a policy rule with FQDN "api.*.com" (wildcard in middle)
- WHEN the policy is validated
- THEN the validation MUST fail
- AND an error MUST be returned
```

### 5. Delta vs Complete Spec

#### 何时使用 ADDED

```markdown
## ADDED Requirements

✅ 使用场景：全新的功能需求
### Requirement: FQDN Filtering
The system SHALL support domain name based filtering...
```

#### 何时使用 MODIFIED

```markdown
## MODIFIED Requirements

✅ 使用场景：修改现有需求
### Requirement: Session Tracking
The system SHALL track up to **200k** sessions (previously 100k)...

**Changes from previous version:**
- Capacity: 100k → 200k
- Added per-CPU statistics
- Removed manual cleanup (now LRU auto-eviction)
```

#### 何时使用 REMOVED

```markdown
## REMOVED Requirements

✅ 使用场景：删除过时需求
### Requirement: Legacy API v0.x
**Reason**: Replaced by API v1.0
**Migration Guide**: Use /api/v1/* endpoints instead of /v0/*
**Deprecation Timeline**: v0.x removed in version 2.0.0
```

### 6. 归档时机

#### 何时归档

```bash
✅ 应该归档：
- 所有任务完成 [x]
- 代码已合并到主分支
- 测试全部通过
- 文档已更新

❌ 不应归档：
- 任务还有 [ ] 未完成
- 代码还在 PR 阶段
- 测试失败
- 功能还在实验中
```

#### 归档检查清单

```markdown
归档前检查：
□ tasks.md 所有任务标记为 [x]
□ 代码已提交（git log 可查）
□ 单元测试通过（覆盖率 > 80%）
□ 集成测试通过
□ 性能测试达标
□ 文档已更新（API docs, README, etc.）
□ 变更已在团队会议上评审
```

### 7. 团队协作

#### 提案评审流程

```
1. 创建提案
   ↓
2. 提交 PR（仅 openspec/changes/ 目录）
   ↓
3. 团队评审
   - 评审 proposal.md: 需求是否合理？
   - 评审 design.md: 技术方案是否可行？
   - 评审 tasks.md: 任务是否完整？
   - 评审 spec.md: 需求是否明确？
   ↓
4. 修改提案（根据反馈）
   ↓
5. 批准提案（Approve PR）
   ↓
6. 开始实施
```

#### 实施进度同步

```bash
# 每日更新 tasks.md
- 早上：计划今天完成哪些任务
- 晚上：更新完成的任务为 [x]

# 每周同步
- 周五：团队会议展示进度
  - 运行: openspec list
  - 展示: add-api  18/25 tasks (72% done)

# PR 提交策略
- 小功能：一个 PR 包含代码 + tasks 更新
- 大功能：分阶段 PR，每个 Phase 一个 PR
```

#### 并行开发

```bash
# 场景：两人同时开发不同功能

开发者 A:
openspec/changes/add-api/
├── proposal.md
├── design.md
└── tasks.md

开发者 B:
openspec/changes/add-fqdn/
├── proposal.md
├── design.md
└── tasks.md

# 互不冲突，独立进行
# 完成后分别归档
```

---

## 常见问题（FAQ）

### Q1: OpenSpec 适合什么样的项目？

**A**: OpenSpec 适合：

✅ **适合**：
- 中大型项目（10k+ 行代码）
- 多人协作项目
- 需要长期维护的项目
- 功能复杂的系统
- 需要严格需求管理的项目

❌ **不太适合**：
- 个人玩具项目（过度工程）
- 一次性脚本
- 快速原型验证

### Q2: 必须使用 OpenSpec CLI 吗？

**A**: 不是必须的。

OpenSpec 核心是**目录结构和文档约定**，可以：
- ✅ 手动创建目录和文件
- ✅ 使用自己的脚本
- ✅ 集成到现有工具链

CLI 只是辅助工具，帮你自动化。

### Q3: 能否跳过某些文件？

**A**: 根据项目规模灵活调整。

**小功能**（< 1 周）：
- proposal.md：必须
- tasks.md：必须
- design.md：可选
- spec.md：可选

**大功能**（> 1 周）：
- 全部文件都建议创建

**关键是保持一致性**，整个团队统一标准。

### Q4: specs/ 和 changes/*/specs/ 的区别？

**A**: 这是最容易混淆的地方！

```
changes/add-api/specs/api/spec.md
├─ 这是增量（Delta）
├─ 包含 ADDED/MODIFIED/REMOVED 标记
├─ 描述"这次变更"要改什么
└─ 归档后会被删除

specs/api/spec.md
├─ 这是完整规范（Complete）
├─ 没有 ADDED/MODIFIED 标记
├─ 描述"系统当前"的完整功能
└─ 通过 archive 命令自动生成/更新
```

记住：**Delta → Complete 的转换是自动的**。

### Q5: 能否修改已归档的文档？

**A**: 可以，但不推荐直接修改。

❌ **不要**：直接修改 `archive/` 中的文件（破坏历史记录）

✅ **应该**：创建新的变更来"修正"
```bash
/openspec:proposal fix-documentation-for-session-tracking
```

**原因**：保持历史的真实性和可追溯性。

### Q6: 如何处理紧急 hotfix？

**A**: 紧急情况可以简化流程。

```bash
# 紧急修复可以跳过 proposal 阶段
# 直接创建 archive 文档

mkdir -p openspec/changes/archive/2025-10-30-hotfix-memory-leak

cat > openspec/changes/archive/2025-10-30-hotfix-memory-leak/proposal.md <<EOF
# Hotfix: Memory Leak in DNS Parser

## Why
生产环境出现内存泄漏，系统重启频率增加

## What
修复 dns_parser.c 中未释放的内存

## Impact
- 修改 1 个文件，10 行代码
- 无功能影响
- 内存占用恢复正常

## Timeline
- 立即修复并部署
EOF

cat > openspec/changes/archive/2025-10-30-hotfix-memory-leak/tasks.md <<EOF
- [x] 定位内存泄漏点
- [x] 添加内存释放代码
- [x] 本地测试验证
- [x] 部署到生产
EOF
```

**关键**：事后补充文档，保持记录完整。

### Q7: OpenSpec 和 Git 如何配合？

**A**: OpenSpec 文档和代码一起版本管理。

```bash
# 提案阶段：PR 只包含文档
git add openspec/changes/add-api/
git commit -m "proposal: Add Control Plane API"
git push origin add-api-proposal

# 实施阶段：PR 包含代码 + tasks 更新
git add src/agent/api/
git add openspec/changes/add-api/tasks.md
git commit -m "feat: Implement health check endpoint (task 4/25)"
git push origin add-api-implementation

# 归档阶段：PR 包含归档操作
openspec archive add-api --yes
git add openspec/changes/archive/
git add openspec/specs/
git commit -m "archive: Add Control Plane API"
git push origin add-api-archive
```

### Q8: 如何在现有项目中引入 OpenSpec？

**A**: 分步引入，从新功能开始。

**阶段 1: 回顾性归档**（可选）
```bash
# 为已实现的核心功能补充文档
# 放入 archive/
```

**阶段 2: 新功能使用 OpenSpec**
```bash
# 从下一个新功能开始
# 严格遵循 proposal → apply → archive
```

**阶段 3: 团队培训**
```bash
# 分享这份文档
# 进行内部技术分享
# 统一团队理解
```

**阶段 4: 完善规范**
```bash
# 逐步补充 specs/ 目录
# 形成完整的系统规范
```

---

## 工具和集成

### 编辑器支持

#### VS Code

创建 `.vscode/settings.json`:

```json
{
  "files.associations": {
    "**/openspec/**/*.md": "markdown"
  },
  "markdown.extension.toc.levels": "2..3",
  "[markdown]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode",
    "editor.wordWrap": "on"
  }
}
```

#### Vim

添加到 `.vimrc`:

```vim
" OpenSpec 文件自动识别
autocmd BufNewFile,BufRead */openspec/changes/*/proposal.md setlocal ft=markdown
autocmd BufNewFile,BufRead */openspec/changes/*/design.md setlocal ft=markdown
autocmd BufNewFile,BufRead */openspec/changes/*/tasks.md setlocal ft=markdown

" 任务快速切换
function! ToggleCheckbox()
  let line = getline('.')
  if line =~ '^\- \[ \]'
    call setline('.', substitute(line, '^\- \[ \]', '- [x]', ''))
  elseif line =~ '^\- \[x\]'
    call setline('.', substitute(line, '^\- \[x\]', '- [ ]', ''))
  endif
endfunction

nnoremap <leader>x :call ToggleCheckbox()<CR>
```

### Git Hooks

创建 `.git/hooks/pre-commit`:

```bash
#!/bin/bash
# 检查 tasks.md 格式

for tasks_file in $(git diff --cached --name-only | grep 'tasks.md$'); do
  echo "Checking $tasks_file..."

  # 检查任务格式
  if ! grep -qE '^- \[(x| )\]' "$tasks_file"; then
    echo "Error: Invalid task format in $tasks_file"
    echo "Tasks must start with '- [ ]' or '- [x]'"
    exit 1
  fi
done

exit 0
```

### CI/CD 集成

创建 `.github/workflows/openspec.yml`:

```yaml
name: OpenSpec Validation

on:
  pull_request:
    paths:
      - 'openspec/changes/**'

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Install OpenSpec CLI
        run: |
          # 安装 openspec 命令行工具
          curl -L https://github.com/openspec/cli/releases/latest/download/openspec-linux -o /usr/local/bin/openspec
          chmod +x /usr/local/bin/openspec

      - name: Validate Changes
        run: |
          for change_dir in openspec/changes/*; do
            if [ -d "$change_dir" ] && [ "$change_dir" != "openspec/changes/archive" ]; then
              change_id=$(basename "$change_dir")
              echo "Validating $change_id..."
              openspec validate "$change_id" --strict
            fi
          done

      - name: Check Task Progress
        run: |
          openspec list
```

---

## 总结

### OpenSpec 核心价值

1. **提案先行**：在写代码前想清楚要做什么
2. **变更可追溯**：从提案到实施到归档，全程记录
3. **规范驱动**：用需求（Requirements）和场景（Scenarios）定义功能
4. **知识沉淀**：通过 specs/ 和 archive/ 保留系统演进历史

### 一图总结 OpenSpec

```
┌─────────────────────────────────────────────────────────────┐
│                        OpenSpec 工作流                       │
└─────────────────────────────────────────────────────────────┘

    PROPOSAL                 APPLY                ARCHIVE
   ┌─────────┐            ┌─────────┐          ┌─────────┐
   │ 创建提案 │ ────────> │ 实施功能 │ ──────> │ 归档变更 │
   └─────────┘            └─────────┘          └─────────┘
        │                      │                     │
        ↓                      ↓                     ↓
┌──────────────┐      ┌──────────────┐      ┌──────────────┐
│ changes/     │      │ changes/     │      │ archive/     │
│ add-api/     │      │ add-api/     │      │ YYYY-MM-DD-  │
│ ├─proposal.md│      │ ├─tasks.md   │      │ add-api/     │
│ ├─design.md  │      │ │ [x][x][ ]  │      │              │
│ ├─tasks.md   │      │ │ 进度更新    │      │ specs/       │
│ └─specs/     │      │ └─编写代码    │      │ add-api/     │
│   (Delta)    │      │              │      │ spec.md      │
└──────────────┘      └──────────────┘      │ (Complete)   │
                                            └──────────────┘

┌─────────────────────────────────────────────────────────────┐
│ 三个目录的含义                                               │
├─────────────────────────────────────────────────────────────┤
│ changes/  = 正在做什么    (工作区)                          │
│ specs/    = 系统是什么    (真相源)                          │
│ archive/  = 做过什么      (历史库)                          │
└─────────────────────────────────────────────────────────────┘
```

### 快速开始清单

对于想要在团队中引入 OpenSpec 的你：

```markdown
□ 阅读本文档，理解核心概念
□ 查看项目示例（如果有）
□ 为下一个新功能创建第一个提案
□ 实施功能并更新任务清单
□ 完成后执行归档
□ 在团队会议上分享经验
□ 逐步推广到整个团队
□ 建立团队内部的 OpenSpec 最佳实践
```

### 学习资源

- **官方文档**：OpenSpec 规范说明
- **示例项目**：查看 `openspec/changes/archive/` 中的真实案例
- **技术分享**：本文档适合作为技术分享材料

---

## 附录

### 附录 A: 文档模板

#### proposal.md 模板

```markdown
# Proposal: [功能名称]

## Why
[为什么需要这个功能？]
- 业务需求
- 技术痛点
- 用户反馈

## What
[要做什么？]
- 功能点 1
- 功能点 2
- 功能点 3

## How
[怎么做？简要技术方案]
- 技术选型
- 架构设计
- 实施步骤

## Impact
[影响分析]
- 代码变更：新增/修改哪些模块
- 性能影响：CPU/内存/延迟
- 兼容性：是否有破坏性变更
- 配置变更：新增/修改配置项

## Timeline
[时间规划]
- Week 1: ...
- Week 2: ...
- Week 3: ...

## Risks
[风险和缓解措施]
- 风险 1：描述 + 缓解措施
- 风险 2：描述 + 缓解措施

## Success Criteria
[成功标准]
- [ ] 功能完整性
- [ ] 性能达标
- [ ] 测试覆盖率 > X%
- [ ] 文档完善
```

#### design.md 模板

```markdown
# Design: [功能名称]

## Architecture
[架构图]

## Components
[组件说明]

### Component 1
- 职责
- 接口
- 依赖

### Component 2
...

## Data Structures
[数据结构定义]

## Algorithms
[核心算法]

## API Design
[API 设计]

## Technical Decisions
[技术决策]

### Decision 1: [决策标题]
- **选择**：方案 A
- **原因**：...
- **备选**：方案 B, C
- **权衡**：...

## Performance Considerations
[性能考虑]

## Security Considerations
[安全考虑]

## Testing Strategy
[测试策略]
```

#### tasks.md 模板

```markdown
# Tasks: [功能名称]

## Phase 1: [阶段名称]
- [ ] 任务 1
- [ ] 任务 2
- [ ] 任务 3

## Phase 2: [阶段名称]
- [ ] 任务 4
- [ ] 任务 5

## Phase 3: Testing
- [ ] Unit tests
- [ ] Integration tests
- [ ] Performance tests

## Phase 4: Documentation
- [ ] API documentation
- [ ] User guide
- [ ] Migration guide (if needed)
```

#### spec.md 模板

```markdown
# Capability: [功能名称]

## ADDED Requirements

### Requirement: [需求标题]
[详细描述，使用 SHALL/MUST/SHOULD]

#### Scenario: [场景名称]
- GIVEN [前置条件]
- WHEN [触发动作]
- THEN [预期结果]
- AND [额外验证]

#### Scenario: [另一个场景]
...

### Requirement: [另一个需求]
...

## MODIFIED Requirements
[如果是修改现有功能]

### Requirement: [原有需求标题]
[修改后的完整描述]

**Changes from previous version:**
- 变更点 1
- 变更点 2

## REMOVED Requirements
[如果删除功能]

### Requirement: [被删除的需求]
**Reason**: [删除原因]
**Migration**: [迁移指南]
```

### 附录 B: 命令速查表

```bash
# === 创建和管理变更 ===
/openspec:proposal <change-id>          # 创建提案
/openspec:apply <change-id>             # 开始实施
/openspec:archive <change-id>           # 归档变更

# === 查看状态 ===
openspec list                           # 活动变更列表
openspec list --specs                   # 完整规范列表
openspec validate <change-id> --strict  # 验证提案

# === 手动操作 ===
mkdir -p openspec/changes/<change-id>/specs
cat openspec/changes/<change-id>/tasks.md
ls -la openspec/changes/archive/
ls -la openspec/specs/

# === 任务进度 ===
grep -c '^\- \[x\]' tasks.md            # 已完成任务数
grep -c '^\- \[' tasks.md               # 总任务数
```

### 附录 C: 检查清单

#### 提案评审检查清单

```markdown
□ proposal.md 包含 Why/What/Impact
□ design.md 包含技术方案和架构图
□ tasks.md 任务粒度适中（1-4 小时/任务）
□ spec.md 使用正确的 ADDED/MODIFIED/REMOVED 标记
□ 所有 Requirement 都有至少 2 个 Scenario
□ 性能目标明确且可测量
□ 风险和缓解措施已识别
□ 时间估算合理
```

#### 归档前检查清单

```markdown
□ tasks.md 所有任务标记为 [x]
□ 代码已合并到主分支
□ 单元测试通过（覆盖率 > 80%）
□ 集成测试通过
□ 性能测试达标
□ API 文档已更新
□ README 已更新（如有必要）
□ 变更已在团队评审
```

---

**文档版本**: 1.0
**最后更新**: 2025-10-31
**作者**: eBPF 微隔离项目组
**适用于**: OpenSpec 学习和技术分享

---

**END**
