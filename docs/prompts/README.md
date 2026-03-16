# 子 Agent Prompt 汇总

## 文件列表

| Agent | Prompt 文件 | 特点 |
|-------|-------------|------|
| 主 Agent | `main-agent-collaboration.md` | 协调派发任务 |
| Ironman | `subagent-ironman.md` | 复杂问题处理 |
| CaptainAmerica | `subagent-captainamerica.md` | 定时任务执行 |
| Thor | `subagent-thor.md` | 搜索与研究 |
| Hulk | `subagent-hulk.md` | 批量数据处理 |
| BlackWidow | `subagent-blackwidow.md` | 精细敏感任务 |
| Hawkeye | `subagent-hawkeye.md` | 精准定位执行 |

## 添加方式

将对应 prompt 文件内容添加到各 Agent 的 `agent/agent.md` 或 `agent/system-prompt.md` 中。

## 协作流程图

```
┌─────────────┐
│  主 Agent   │
│ (main)      │
└──────┬──────┘
       │ sessions_spawn
       ▼
┌─────────────┐
│  子 Agent   │
│ (ironman/   │
│  thor/... ) │
└──────┬──────┘
       │ sessions_accept
       │ (确认接收)
       ▼
┌─────────────┐
│  执行任务   │
└──────┬──────┘
       │ sessions_send
       ▼
┌─────────────┐
│  主 Agent   │
│  群回复结果 │
└─────────────┘
```

## 核心工具使用

### sessions_spawn (主 Agent 调用)
```json
{
  "agentId": "目标agent",
  "task": "任务描述"
}
```

### sessions_accept (子 Agent 调用)
```json
{
  "task": "任务描述"
}
```

### sessions_send (子 Agent 调用)
```json
{
  "sessionKey": "agent:main:feishu:group:xxx",
  "content": "完成报告"
}
```
