# LLM-Driven Intelligent Dialogue

lemon_ai — 一个由 LLM 驱动的智能客服对话系统框架。

核心思想：用 LLM 理解用户意图，然后按照写好的"业务流程图"（Flow）自动完成对话。

## 核心概念

1. Flow（对话流程）
* 像一个"业务流程图"，定义了机器人要走的步骤。
```bash
取消订单 Flow:
  步骤1: 展示订单列表，让用户选一个
  步骤2: 显示订单详情
  步骤3: 问"确认取消吗？"
  步骤4: 执行取消
```

2. Slot（槽位）
* 对话过程中收集的"变量"，比如用户选择的 `order_id`、输入的 `receiver_name`。

3. Tracker（对话状态追踪器）
* 记录每个用户的对话历史、已填的`Slot`、当前执行到哪个`Flow` 哪一步。

4. LangGraph 执行图
* 一条消息进来后，系统按固定的节点顺序处理：

```bash
用户发消息
  ↓
understand（LLM理解意图，生成命令）
  ↓
policy（决定执行哪个 Flow 的哪个步骤）
  ↓
action（执行具体动作，比如查数据库）
  ↓
guard（检查是否需要继续循环）
  ↓
response（生成回复）
```

5. Domain（领域定义）
* 定义系统能说什么话（responses）、有哪些`Slot`、有哪些 `Action`。

## 主要文件
文件 | 概览
--|--|
ecs_demo/config.yml	|整个系统用什么 LLM，哪些策略
ecs_demo/data/flows/flow_order.yml	|Flow 的写法：步骤、条件分支、槽位收集
ecs_demo/domain/domain_order.yml	|Domain 的写法：机器人的话术模板、Slot 定义


文件 | 概览
--|--|
lemon_ai/dialogue_understanding/flow/flow.py|	Flow、FlowStep 数据结构
lemon_ai/core/tracker.py	|Tracker 如何保存对话状态
lemon_ai/dialogue_understanding/stack/dialogue_stack.py	|对话栈，记录"当前在执行哪个 Flow"


文件 | 概览
--|--|
lemon_ai/agent/graph/builder.py	|图的整体结构（START → 5个节点 → END）
lemon_ai/agent/graph/nodes/understand.py	|LLM 如何理解用户意图
lemon_ai/agent/graph/nodes/policy.py	|如何决定下一步执行什么
lemon_ai/agent/graph/nodes/action.py	|如何执行 Action
lemon_ai/agent/graph/nodes/response.py	|如何生成回复


文件 | 概览
--|--|
lemon_ai/agent/agent.py	| Agent.load() 和 handle_message() — 整个系统的入口



用户发一条消息 → LLM 理解意图 → 系统找到对应的 Flow → 按 Flow 步骤收集信息、执行动作 → 返回回复。


