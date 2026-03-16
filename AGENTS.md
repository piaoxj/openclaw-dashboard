# AGENTS.md

This file provides guidance to Codex (Codex.ai/code) when working with code in this repository.

## 项目名称

OpenClaw Dashboard（原 OpenClaw Control Center）

## 常用命令

```bash
# 开发与启动
npm run dev           # 运行一次 monitor（不启动 UI）
npm run dev:ui        # 启动本地 UI 服务（推荐）
npm run dev:continuous # 持续监控模式
npm run smoke:ui     # UI 冒烟测试

# 构建与测试
npm run build        # TypeScript 编译
npm test             # 运行测试

# 数据验证
npm run validate:task-store  # 验证任务存储
npm run validate:budget      # 验证预算计算

# 定时任务
npm run command:backup-export    # 备份导出
npm run command:import-validate  # 导入校验
npm run command:acks-prune       # 清理过期 ack
npm run command:task-heartbeat   # 任务心跳

# 健康检查
npm run watchdog:run     # 看门狗
npm run heal:check      # 停滞自动恢复
npm run health:snapshot # 健康快照

# 其他
npm run release:audit   # 发布审计
```

## 项目架构

### 核心组件

- **src/index.ts** - 入口文件，支持命令行模式（`APP_COMMAND`）和 HTTP 服务模式
- **src/ui/server.ts** - HTTP UI 服务器，提供所有 API 端点
- **src/runtime/** - 核心运行时模块集合，每个文件负责特定功能域
- **src/clients/** - 外部客户端适配器（Gateway、OpenClaw、Tool）
- **src/adapters/** - 数据适配器（只读模式）
- **src/mappers/** - 数据映射和解析

### 数据流

1. **Gateway 连接**：通过 WebSocket（默认 `ws://127.0.0.1:18789`）连接 OpenClaw Gateway
2. **数据采集**：monitor.ts 定期轮询 Gateway 获取会话、任务、项目状态
3. **本地存储**：运行时数据保存在 `runtime/` 目录（JSON 文件）
4. **HTTP 服务**：ui/server.ts 提供 UI 和 API 服务（默认端口 4310）

### 主要模块

| 模块 | 功能 |
|------|------|
| monitor.ts | 定期采集 OpenClaw 运行时状态 |
| snapshot-store.ts | 快照存储管理 |
| project-store.ts | 项目本地存储 |
| task-store.ts | 任务本地存储 |
| budget-governance.ts | 预算治理与阈值检查 |
| usage-cost.ts | 用量与成本计算 |
| session-conversations.ts | 会话历史管理 |
| approval-action-service.ts | 审批操作服务 |
| notification-center.ts | 通知中心 |
| audit-timeline.ts | 审计时间线 |

### 安全约束

- `READONLY_MODE=true` - 默认只读模式
- `LOCAL_TOKEN_AUTH_REQUIRED=true` - 默认需要本地 token 认证
- `APPROVAL_ACTIONS_ENABLED=false` - 审批动作默认关闭
- `IMPORT_MUTATION_ENABLED=false` - 导入变更默认关闭

### 环境变量

核心配置项（见 `.env.example`）：
- `GATEWAY_URL` - OpenClaw Gateway 地址
- `OPENCLAW_HOME` - OpenClaw 主目录
- `UI_PORT` - UI 服务端口（默认 4310）
- `LOCAL_API_TOKEN` - 本地认证 token

### 运行时文件

- `runtime/last-snapshot.json` - 最新快照
- `runtime/projects.json` - 项目数据
- `runtime/tasks.json` - 任务数据
- `runtime/budgets.json` - 预算配置
- `runtime/acks.json` - 确认记录
- `runtime/timeline.log` - 时间线日志
- `runtime/digests/YYYY-MM-DD.json` - 每日摘要

### UI 页面

访问 `http://127.0.0.1:4310/?section=<name>&lang=zh|en`

- `overview` - 总览
- `usage-cost` - 用量与花费
- `staff` - 员工状态
- `collaboration` - 协作
- `memory` - 记忆工作台
- `docs` - 文档工作台
- `tasks` - 任务板
- `settings` - 设置
