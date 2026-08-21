# Stage 1 - Build A Minimal Agent Loop

## 1. 本阶段目标

亲手实现一个最小 Agent，使其能够：

用户输入
→ 模型判断
→ 调用 Tool
→ Python 执行 Tool
→ Tool Result 回传模型
→ 模型继续推理
→ 最终回答

并加入：

- max_steps
- error handling
- timeout

---

## 2. LLM API

使用 OpenAI Python SDK 调用 Qwen：

- Model：qwen-plus
- API Key：通过环境变量读取
- 不把真实 Key 写进代码

基本流程：

Python
→ API
→ Qwen
→ response

---

## 3. JSON

模型返回 JSON 时：

JSON 文本
↓
Python str
↓
json.loads()
↓
Python dict

---

## 4. Tool

Tool 本质上可以是普通 Python 函数。

本阶段实现：

calculator(a, b, operation)

支持：

- add
- subtract
- multiply
- divide

---

## 5. tools 和 tool_calls

tools：

Python → Model

告诉模型“有哪些工具可以使用”。

tool_calls：

Model → Python

告诉程序：

- 调哪个工具
- 使用什么参数

---

## 6. Tool Result

完整消息链：

user
↓
assistant(tool_call)
↓
tool(result)
↓
assistant(final answer)

Tool Result 使用：

role = "tool"

并通过：

tool_call_id

和前面的 Tool Call 对应。

---

## 7. Agent Loop

最小循环：

for step in range(max_steps):

    调用模型

    如果没有 tool_calls：
        返回最终答案

    如果有 tool_calls：
        执行工具
        把 Tool Result 加入 messages
        继续下一轮

## 8. max_steps

作用：

限制 Agent 最大执行轮数。

例如：

max_steps = 5

不代表一定运行 5 轮。

如果第 2 轮任务已经完成：

→ break / return

如果跑到第 5 轮还没完成：

→ 强制停止

主要防止：

- 无限循环
- Token 浪费
- API 成本失控

- ## 9. Error Handling

Tool 报错不应该直接让整个 Agent 崩溃。

例如：

10 ÷ 0

calculator
↓
ValueError
↓
try / except
↓
错误转换成 Tool Result
↓
回传 Model
↓
Model 继续回答

关键理解：

Tool Failure ≠ Agent Failure

错误也可以成为 Agent 的 Observation。


## 10. Timeout

max_steps：

防止 Agent 一直循环。

timeout：

防止一次 API 请求一直等待。

Qwen API 超时时：

APITimeoutError
↓
except
↓
Agent 安全停止

## 11. Debug / 踩坑记录

### 1. API Key 中文占位符

问题：

复制了“你的百炼API Key”。

导致：

UnicodeEncodeError

原因：

Authorization Header 中出现中文。

解决：

使用真实 API Key，并通过环境变量读取。

---

### 2. import 自动执行测试代码

calculator_tool.py 顶层曾存在：

result = calculator(...)
print(...)

导致：

from calculator_tool import calculator

时自动打印测试结果。

解决：

把测试代码放进：

if __name__ == "__main__":

---

### 3. messages=message

第二次模型调用曾错误传入：

messages=message

正确应该是：

messages=messages

原因：

模型需要完整消息历史：

user
→ assistant(tool_call)
→ tool(result)

## 12. 我现在应该能解释

- Agent 和 Workflow 有什么区别？
- tools 和 tool_calls 有什么区别？
- 为什么 function.arguments 要 json.loads()？
- 为什么 Tool Result 要回传模型？
- 为什么要保存 assistant 的 tool call？
- tool_call_id 有什么作用？
- 为什么需要 Agent Loop？
- max_steps 为什么不是必须执行次数？
- max_steps 和 timeout 有什么区别？
- 为什么 Tool 报错不一定导致 Agent 失败？
