# CLAUDE.md

本文件为 Claude Code (claude.ai/code) 在本仓库工作时提供指导。

## 项目概览

**AI PosterGen (智绘海报)** 是一个为基于 SaaS 的 AI 海报生成平台的全面设计文档项目。本仓库包含**约 600 页的专业规范文档**，涵盖产品、设计和架构领域。

**重要说明**：这是一个**文档优先的项目**，不包含生产代码。未来的实现将基于这些规范。

## 仓库结构

```
ai_PostGen_claude/
├── README.md                    # 完整导航的项目入口
├── 00_项目文档索引.md           # 综合文档索引
├── docs/                        # 统一文档管理目录
│   ├── 01_产品规划/             # 产品需求和商业分析
│   ├── 02_设计系统/             # UI/UX 设计和线框图
│   ├── 03_系统架构/             # 后端、前端和交互设计
│   └── 04_实施指南/             # P0/P1 修复方案和实施指南
└── .claude/commands/            # Speckit 工作流命令
```

## 核心文档（优先级顺序）

### 1. **必读文档**（基础文献）
- `docs/01_产品规划/PRD_完整版.md` - 完整产品规范（150 页）
  - 4 个主要模块，包含详细的功能需求
  - 6 个核心数据实体和 API 规范
  - MVP → Phase 2 → Phase 3 路线图

- `docs/03_系统架构/系统架构设计总结.md` - 企业架构概览（40 页）
  - 完整的系统图和数据流
  - 技术栈选择理由（Vue 3、FastAPI、PostgreSQL、Kubernetes）

### 2. **技术架构文档**（深入探讨）
- `docs/03_系统架构/后端系统架构设计.md` - 后端设计（80 页）
  - 6 个微服务：用户、项目、设计、导出、资源、分析
  - Kong API 网关、RabbitMQ、Redis、PostgreSQL 架构
  - 认证/授权流程、API 契约

- `docs/03_系统架构/前端系统架构设计.md` - 前端设计（70 页）
  - Vue 3 Composition API 架构和 Pinia 状态管理
  - 基于 Pixi.js 的编辑工作台设计
  - 性能优化和测试架构

- `docs/03_系统架构/前后端交互设计.md` - API 契约（60 页）
  - 详细的 HTTP/WebSocket API 规范
  - 错误处理和数据同步协议
  - 实时通知架构

### 3. **设计系统**（UI 实现参考）
- `docs/02_设计系统/UI_设计系统和Wireframe.md` - 设计规范（50 页）
  - 12+ 种配色、8 级排版系统
  - 25+ 个组件库规范
  - 5 个核心页面线框图：登录、仪表板、编辑器、导出、管理

- `docs/02_设计系统/用户流程和交互设计.md` - UX 流程（40 页）
  - 7 个主要用户场景及交互详情
  - 入门、编辑工作台、导出工作流

### 4. **实施指南**（开发路线图）
- `docs/04_实施指南/00_架构修复方案索引.md` - 修复方案优先级排序
  - 6 个 P0 关键修复（阻塞 MVP 发布）
  - 3+ 个 P1 高优先级增强
  - 实施时间表（2 周冲刺）

**P0 修复**（MVP 前必须实现）：
1. `P0修复方案_用户头像上传.md` - 头像上传（Pillow + Fastapi-upload + S3）
2. `P0修复方案_邮件通知系统.md` - 邮件服务（SendGrid + RabbitMQ + Celery）
3. `P0修复方案_数据备份与恢复.md` - 备份系统（pg_dump + S3 + WAL + Celery 定时任务）
4. `P0修复方案_分层速率限制.md` - 速率限制（Kong + FastAPI slowapi 中间件）
5. `P0修复方案_数据库优化.md` - 数据库优化（连接池 + 15+ 个索引 + SQLAlchemy 查询优化）

## 技术栈

### 后端
- **运行时**: Python 3.11+
- **框架**: FastAPI 0.104+ (ASGI)
- **ASGI 服务器**: Uvicorn + Gunicorn
- **ORM**: SQLAlchemy 2.0+ with asyncpg
- **API 网关**: Kong
- **消息队列**: RabbitMQ (通过 aio-pika)
- **异步任务**: Celery 5.3+
- **数据库**: PostgreSQL 14+
- **缓存**: Redis 7+ (aioredis)
- **文件存储**: AWS S3 (boto3)
- **容器**: Docker + Kubernetes
- **监控**: Prometheus + Grafana
- **依赖管理**: Poetry
- **代码质量**: Black + Ruff + mypy
- **测试**: pytest + pytest-asyncio

### 前端
- **框架**: Vue 3.4+ (Composition API) + TypeScript 5.0+
- **构建工具**: Vite 5.0 (原生支持，极速 HMR)
- **状态管理**: Pinia 2.x (Vue 3 官方推荐)
- **API 层**: Axios + @tanstack/vue-query (VueQuery)
- **UI 组件库**: Element Plus (企业级)
- **编辑器**: Pixi.js 7.3 (WebGL 画布)
- **样式**: Tailwind CSS + Scoped CSS
- **工具集**: @vueuse/core (Vue Composition 工具)
- **测试**: Vitest (Vite 原生) + Vue Test Utils + Playwright

### 基础设施
- **IaC**: Kubernetes 清单 / Terraform
- **CI/CD**: GitLab CI / GitHub Actions / Jenkins
- **可观测性**: Prometheus、Grafana、ELK Stack

## 关键设计模式

### Vue 3 核心模式
**Composition API 模式**:
- **ref/reactive**: 响应式状态（基本类型用 ref，对象用 reactive）
- **computed**: 派生状态（自动缓存）
- **watch/watchEffect**: 副作用（API 调用、DOM 操作）
- **生命周期钩子**: onMounted、onBeforeUnmount 等
- **Provide/Inject**: 跨层级依赖注入

**Composable 模式**（类似 React Hooks）:
- 可复用的业务逻辑抽象
- 清晰的逻辑组织（按功能而非选项）
- 完美的 TypeScript 类型推导
- 示例：`useAuth()`、`useProject()`、`useEditor()`、`usePixiCanvas()`

**Pinia Store 模式**:
- Setup Store 语法（类似 Composition API）
- 自动类型推导，无需复杂类型定义
- 直接修改 state，无需 mutations
- 插件支持（持久化、DevTools）

## 关键架构模式

### 微服务架构
- **用户服务**（端口 3001）：认证、用户资料、订阅管理
- **项目服务**（端口 3002）：项目 CRUD、所有权管理
- **设计服务**（端口 3003）：AI 生成、缓存、版本控制
- **导出服务**（端口 3004）：格式转换、异步作业处理
- **资源服务**（端口 3005）：图片/资源存储
- **分析服务**（端口 3006）：使用跟踪、指标收集
- **邮件服务**（端口 3007）：异步邮件通知

### 前端架构（Vue 3 原生设计）
- **视图层**：Vue 3 Single File Components (SFC) - `<template>` + `<script setup>` + `<style scoped>`
- **逻辑层**：Composables（组合式函数）- 可复用业务逻辑
- **状态管理层**：Pinia Stores - 模块化全局状态，完整 TypeScript 支持
- **服务层**：API 服务（Axios）+ 业务服务 + 数据转换
- **编辑工作台**：Pixi.js Canvas（通过 Composable 集成）+ 属性面板 + 图层面板 + 工具栏
- **路由系统**：Vue Router 4（嵌套路由、路由守卫、懒加载）

### 数据流模式
- **认证**：JWT 令牌加刷新机制
- **实时更新**：WebSocket 订阅（项目、设计变化）
- **异步处理**：RabbitMQ 队列（导出、邮件、图像生成）
- **缓存**：Redis 用于会话、用户偏好、设计模板
- **数据同步**：乐观更新加服务器协调

## 按角色的文档导航

### 🏗️ 架构/后端开发
起点：`系统架构设计总结.md` → `后端系统架构设计.md` → `前后端交互设计.md`

### 🎨 前端开发（Vue 3）
起点：
1. `前端架构_Vue3快速参考.md` （快速入门，15分钟）
2. `前端系统架构设计_Vue3原生版.md` （完整架构，60分钟）
3. `前端代码示例_Vue3完整实现.md` （代码示例，45分钟）
4. `前端配置_Vue3项目配置和工具链.md` （工具链配置，30分钟）

**核心要点**:
- Vue 3 Composition API（非 Options API）
- Pinia 状态管理（非 Vuex）
- `<script setup>` 语法
- TypeScript 完整支持
- Composables 逻辑复用

### 📊 产品/项目管理
起点：`PRD_完整版.md` → `系统架构设计总结.md` → `00_架构修复方案索引.md`

### 🚀 DevOps/基础设施
起点：`系统架构设计总结.md`（部署部分）→ `部署指南.md` → `监控和告警设置.md`

## 重要设计决策

1. **微服务而非单体架构**：支持 AI 生成、异步导出、媒体处理的独立扩展
2. **使用 Pinia 管理状态**：Vue 3 官方推荐，完美的 TypeScript 支持和自动代码补全
3. **Pixi.js 编辑器**：硬件加速画布，实现流畅的海报设计交互
4. **Kong API 网关**：为 7 个微服务提供集中式速率限制、认证、路由
5. **PostgreSQL + Redis + RabbitMQ + Celery**：
   - 关系数据持久化 + 快速缓存
   - 异步任务处理与分布式任务调度
   - 支持高并发设计生成和导出
6. **Python FastAPI 后端**：
   - 原生异步支持，单实例吞吐量 ~10,000 req/s
   - 自动 OpenAPI 文档生成
   - Pydantic V2 强大的数据验证
   - 丰富的异步生态（asyncpg、aioredis、aio-pika）
7. **Kubernetes 部署**：无状态服务的水平扩展；用户项目的持久存储

## 非功能需求

- **性能**：API P95 < 500ms、数据库 P95 < 100ms、页面加载 < 3s
- **可用性**：99.9% 可用时间、RTO 4 小时、RPO 24 小时
- **安全**：JWT 认证、基于角色的访问控制、HTTPS、SQL 注入防护、CORS 控制
- **可扩展性**：支持 1 万并发用户、100 万个项目、水平 pod 自动扩展
- **合规性**：GDPR 数据删除、审计日志、备份

## 工作流命令（Speckit 集成）

项目在 `.claude/commands/` 中使用 Speckit 模板：
- `/speckit.plan` - 从需求生成实施计划
- `/speckit.specify` - 创建/更新功能规范
- `/speckit.tasks` - 生成具有依赖关系的可执行任务列表
- `/speckit.implement` - 执行实施工作流
- `/speckit.analyze` - 验证 spec/plan/tasks 的一致性
- `/speckit.clarify` - 识别当前规范中的歧义

## 常见开发任务

### 理解新功能
1. 在 `PRD_完整版.md` 中查找功能（功能需求）
2. 检查 `用户流程和交互设计.md` 的用户交互流程
3. 查阅 `UI_设计系统和Wireframe.md` 的组件规范
4. 参考相关架构文档（后端/前端）
5. 参考 `前后端交互设计.md` 的 API 契约
6. 检查 `P0/P1修复方案_*.md` 的相关修复需求

### 实现后端功能
1. 在 `前后端交互设计.md` 中定义数据模型（API 契约部分）
2. 检查 PRD 中的数据库架构（数据实体）
3. 按照 `后端系统架构设计.md` 的模式实现微服务
4. 如果需要，为异步任务添加 RabbitMQ 队列
5. 在 Kong 中注册新端点的路由
6. 参考 `分层速率限制` 配置速率限制

### 实现前端功能（Vue 3）
1. 在 `UI_设计系统和Wireframe.md` 中查看组件规范
2. 查阅 `前端架构_Vue3快速参考.md` 快速查找代码模式
3. 参考 `前端代码示例_Vue3完整实现.md` 中的完整实现
4. 使用 Composable 模式组织逻辑（`useAuth()`、`useProject()`等）
5. 使用 Pinia Store 管理全局状态
6. 通过 `usePixiCanvas()` Composable 集成 Pixi.js
7. 使用 VueQuery 管理服务端状态
8. 如果需要实时同步，添加 WebSocket 订阅
9. 针对 `用户流程和交互设计.md` 中的交互流程进行测试（Vitest + Playwright）

**Vue 3 关键模式**:
- 组件：`<script setup lang="ts">` + TypeScript Props/Emits
- 状态：Pinia Store（Setup Store 语法）
- 逻辑复用：Composables（如 `useEditor()`）
- 生命周期：`onMounted()`、`onBeforeUnmount()` 等
- 响应式：`ref()`（基本类型）、`reactive()`（对象）

## 架构质量评分

**当前**：82/100（系统架构师审查后）
- ✅ 优势：微服务架构、全面的安全性、清晰的 API 契约
- ⚠️ 改进空间：图像处理性能瓶颈、缓存策略、数据库索引
- 🔴 关键问题：6 个阻塞 MVP 的 P0 问题（已在修复方案中记录）

## 关键统计

- **总文档量**：跨 15 个文档约 600 页
- **核心实体**：6 个（用户、项目、设计、导出、资源、审计）
- **API 端点**：50+ 个（详见 PRD）
- **微服务**：7 个
- **数据库表**：20+ 个
- **前端组件**：25+ 个
