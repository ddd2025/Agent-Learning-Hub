# Stage 1 - Build A Minimal Agent Loop

## 1. LLM API

Python 通过 OpenAI SDK 调用 Qwen：

用户消息
→ API
→ Model
→ Response

---

## 2. JSON

模型返回的 JSON 本质上首先是字符串：

JSON str
→ json.loads()
→ Python dict

---

## 3. Tool

Tool 本质上可以是普通 Python 函数。

例如：

calculator(a, b, operation)

模型本身不执行 Python 函数，
真正执行 Tool 的是 Python Agent 程序。

---

## 4. tools 和 tool_calls

tools：

Python → Model

告诉模型有哪些工具可以使用。

tool_calls：

Model → Python

模型决定调用哪个工具以及使用什么参数。

---

## 5. Tool Result

消息链：

user
→ assistant(tool_call)
→ tool(result)
→ assistant(final answer)

Tool Result 必须通过 role="tool" 回传给模型。

tool_call_id 用来告诉模型：
这个结果属于哪一次工具调用。

---

## 6. Agent Loop

核心结构：

Observe
→ Think
→ Act
→ Observe
→ Think
→ ...

代码中通过 for 循环不断：

调用模型
→ 判断 tool_calls
→ 执行工具
→ 回传结果
→ 再次调用模型

---

## 7. 停止条件

正常停止：

模型不再返回 tool_calls
→ 返回最终答案
→ break / return

---

## 8. max_steps

max_steps 是 Agent 最大执行轮数。

作用：

防止无限循环
防止 Token 浪费
控制 API 成本

max_steps 是上限，
并不代表 Agent 必须跑满这么多轮。

---

## 9. Error Handling

Tool 失败不等于整个 Agent 必须失败。

例如：

calculator
→ ValueError
→ try / except
→ 转换成 Tool Result
→ 回传 Model
→ Model 继续判断

错误也可以成为 Agent 的 Observation。

---

## 10. Timeout

max_steps：

防止 Agent 一直循环。

timeout：

防止某一次请求一直等待。

模型 API 超时时可以捕获：

APITimeoutError

然后让 Agent 安全停止。

---

## 11. Minimal Agent

最终 minimal_agent.py 已经具备：

- 接收用户问题
- 调用模型
- 自动选择 Tool
- 执行 Tool
- Tool Result 回传
- 多轮 Agent Loop
- max_steps
- error handling
- timeout
- 自动停止
- 返回最终答案

Stage 1 完成。
