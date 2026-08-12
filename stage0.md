# Stage 0：Understand What An Agent Is

> 学习目标：搞清楚 Agent 到底是什么、它和 Chatbot / Workflow 有什么区别，以及什么时候应该使用 Agent。

---

## 1. Chatbot、Workflow、Agent、Multi-Agent

### Chatbot

Chatbot 的主要任务是根据用户输入生成回答。

基本形式：

```text
用户输入
   ↓
  LLM
   ↓
生成回答
```

Chatbot 可以分析、推理、写代码，但如果它只是接收问题然后生成一次回答，并没有持续与外部环境交互，就不能简单认为它是 Agent。

**核心：回答问题。**

---

### Workflow

Workflow 是开发者提前规定好执行流程，程序按照固定路径一步一步运行。

例如 PDF 问答：

```text
用户上传 PDF
     ↓
提取文字
     ↓
文本分块
     ↓
向量化
     ↓
检索
     ↓
LLM 回答
```

无论用户上传什么 PDF，主要路径都已经提前设计好了。

即使 Workflow 中使用了：

* LLM
* RAG
* Tools
* API

也不代表它就是 Agent。

**核心：程序员提前决定流程。**

---

### Agent

Agent 不只是回答问题，而是围绕用户目标，根据当前情况动态决定下一步应该做什么。

例如 AI 修复代码：

```text
读取代码
   ↓
运行程序
   ↓
发现报错
   ↓
分析错误
   ↓
修改代码
   ↓
重新运行
   ↓
根据新结果继续处理
```

程序员可以规定 Agent 有哪些工具、权限和基本规则，但是具体每一步采取什么行动，可以由模型根据当前状态动态决定。

**核心：根据反馈动态决定行动。**

---

### Multi-Agent

Multi-Agent 是多个 Agent 分工合作完成一个复杂任务。

例如：

```text
Research Agent
负责搜索资料
      ↓
Writer Agent
负责撰写内容
      ↓
Reviewer Agent
负责检查
      ↓
Writer Agent
根据反馈修改
```

Multi-Agent 的重点不是“Agent 越多越高级”，而是解决任务分工与协调问题。

---

## 2. Workflow 和 Agent 最重要的区别

可以这样理解：

```text
Workflow：
程序员决定下一步做什么。

Agent：
程序员规定它能做什么，
模型根据当前情况决定现在具体做什么。
```

因此：

* 固定、可预测的流程 → 更适合 Workflow
* 需要根据结果不断改变下一步 → 更适合 Agent

---

## 3. Agent 的基本循环：Agent Loop

Agent 最重要的运行方式是：

```text
Observe → Think → Act → Observe
```

### Observe：观察

获取当前信息，例如：

* 用户的问题
* 工具返回结果
* Python 报错
* 网页搜索结果
* 文件内容

### Think：思考

模型根据当前信息判断：

* 用户真正想做什么？
* 当前信息够不够？
* 下一步应该调用什么工具？
* 是否需要修改计划？
* 任务是否已经完成？

### Act：行动

Agent 根据判断执行操作，例如：

* 搜索网页
* 查询天气
* 读取文件
* 执行 Python
* 修改代码
* 查询数据库

行动结束以后，会产生新的结果。

新的结果又会成为下一轮的：

```text
Observe
```

因此真正的 Agent Loop 是：

```text
Observe
   ↓
Think
   ↓
Act
   ↓
得到新的结果
   ↓
Observe
   ↓
Think
   ↓
Act
   ↓
……
直到任务完成
```

---

## 4. 为什么 Agent Loop 要设置停止条件？

Agent 不能无限执行，否则可能出现：

```text
搜索
 ↓
资料不够
 ↓
继续搜索
 ↓
还是不够
 ↓
继续搜索
 ↓
……
```

因此真正开发 Agent 时，通常需要设置：

* 最大执行步数
* 超时时间
* 错误处理
* 任务完成条件

例如最多执行 10 次，如果仍然无法完成，就停止并告诉用户。

---

## 5. 什么是 Augmented LLM？

Augmented LLM 可以理解为：

> 增强型 LLM。

普通 LLM 主要负责语言理解和生成。

增强以后，可以加入：

```text
        Retrieval
           ↑
Tools ←   LLM   → Memory
```

也就是让 LLM 获得：

* Retrieval：检索能力
* Tools：工具调用能力
* Memory：记忆能力

但是：

> **Augmented LLM 不一定就是 Agent。**

模型即使拥有这些能力，如果整个执行流程仍然由开发者提前写死，它仍然可能只是一个 Workflow。

---

## 6. Agent 的三个基础组成部分

可以把一个 Agent 简单理解为：

```text
Model + Tools + Instructions
```

### Model

相当于 Agent 的“大脑”。

负责：

* 理解任务
* 推理
* 判断
* 选择下一步行动

### Tools

相当于 Agent 的“手脚”。

例如：

```text
search()
calculator()
read_file()
get_weather()
run_python()
```

Tools 让 Agent 可以和外部世界发生交互。

### Instructions

相当于 Agent 的“工作规则”。

规定：

* Agent 应该怎么做
* 哪些事情允许做
* 哪些事情不能做
* 遇到特殊情况怎么办

例如：

```text
退款金额 ≤ 100 元：
允许自动处理

退款金额 > 100 元：
必须人工确认
```

---

## 7. 什么时候不应该使用 Agent？

Agent 并不是越多越好，也不是所有任务都应该 Agent 化。

判断一个任务时，可以先问三个问题。

### 问题一：流程能不能提前确定？

如果可以，例如：

```text
每天凌晨 2 点
↓
备份数据库
↓
上传服务器
```

普通程序或者 Workflow 就足够了。

---

### 问题二：下一步是否需要根据上一步结果动态改变？

例如：

```text
运行代码
↓
出现报错
↓
根据错误决定怎么修改
↓
重新运行
```

这种任务比较适合 Agent。

---

### 问题三：Agent 带来的灵活性值得吗？

如果简单代码就能解决：

```python
nums.sort()
```

就没有必要加入 LLM、Tool Calling、Agent Loop。

Agent 更灵活，但同时会带来：

* 更高成本
* 更慢响应
* 更多不确定性
* 更复杂的调试
* 更多错误可能

因此应该：

```text
简单问题
→ 普通程序

固定复杂流程
→ Workflow

需要动态决策
→ Agent

需要多个角色协调
→ 再考虑 Multi-Agent
```

---

## 8. 有循环不一定就是 Agent

例如：

```text
Writer
  ↓
写文章
  ↓
Reviewer
  ↓
检查
  ↓
不通过
  ↓
Writer 修改
```

虽然它存在循环，但如果：

* 谁负责生成
* 谁负责检查
* 不合格后做什么

都已经由开发者提前规定，那么它依然可以是 Workflow。

所以判断 Agent 的关键并不是：

> “有没有循环？”

而是：

> **模型是否根据当前环境反馈动态决定下一步行动。**

---

## 9. Single Agent 和 Multi-Agent

学习和开发 Agent 时，应该优先尝试：

```text
Single Agent
```

如果一个 Agent 效果不好，应该先检查：

* Instructions 是否清楚
* Tools 是否合适
* 上下文是否足够
* Workflow 是否设计合理
* 任务是否应该拆分

而不是马上增加更多 Agent。

只有当任务确实存在明显的：

* 角色分工
* 专业能力差异
* 上下文隔离
* 协调需求

时，再考虑 Multi-Agent。

---

## 10. Guardrails：Agent 的安全边界

Agent 可以调用工具，因此必须考虑工具风险。

例如一个财务 Agent：

### 查询余额

```text
风险较低
→ 可以考虑自动执行
```

### 转账

```text
涉及资金
→ 必须人工确认
```

### 删除交易记录

```text
可能不可恢复
→ 应该人工确认
甚至可以直接不给 Agent 这个权限
```

因此：

> 操作造成的影响越严重、越难恢复，就越应该限制 Agent 的权限。

Guardrails 不只是“确认按钮”，还包括：

* 工具权限
* 操作范围
* 人工确认
* 金额限制
* 最大执行次数
* 禁止执行的操作

---

# 11. 我的 Agent 场景：GitHub 开源项目学习助手

## 场景

我想做一个 **GitHub 开源项目学习助手**，主要帮助第一次使用 GitHub 的初学者一步一步学习开源项目。

这个助手可以：

* 阅读 README
* 分析项目结构
* 帮助用户规划学习路线
* 解释知识点
* 根据用户回答判断理解程度
* 帮助运行项目
* 分析代码报错
* 根据用户学习情况调整下一步

---

## 为什么普通 Workflow 不够？

如果使用 Workflow，就需要提前规定：

```text
读取 README
↓
解释项目
↓
学习代码
↓
完成练习
↓
进入下一章
```

但是不同用户学习过程中遇到的问题完全不同。

例如：

```text
用户 A：
没有理解某个概念

用户 B：
运行代码出现错误

用户 C：
已经掌握当前内容

用户 D：
连 GitHub 的 Fork 都不会使用
```

如果始终按照固定流程教学，就很难根据用户当前的问题调整学习路线和教学方法。

这样不利于个性化学习。

---

## 哪些地方需要动态决策？

用户的：

* 回答
* 学习进度
* 项目运行结果
* 操作过程
* 报错信息

都会反馈给模型。

模型根据这些新信息判断下一步。

例如：

```text
用户学习一个知识点
       ↓
    是否理解？
     ↙    ↘
   是      否
   ↓       ↓
下一节   分析问题
           ↓
       换一种讲法
           ↓
        再次练习
```

如果项目运行失败：

```text
运行项目
   ↓
出现错误
   ↓
读取报错
   ↓
分析原因
   ↓
帮助修改
   ↓
重新运行
```

整个学习路径会不断受到上一轮结果影响。

---

## 为什么 Agent 更适合？

GitHub 开源项目学习并不是一条固定流水线。

Agent 可以根据：

* 用户的基础
* 用户当前的理解程度
* 项目结构
* 代码运行情况
* 用户提出的问题

实时调整学习路线和教学方法。

当用户已经理解时：

```text
继续下一节
```

当用户没有理解时：

```text
换一种解释方式
```

当代码报错时：

```text
暂停正常学习
↓
先解决代码问题
```

因此，这个场景需要一个能够不断：

```text
Observe → Think → Act → Observe
```

的动态系统。

所以，相比固定 Workflow，**Agent 更适合用于 GitHub 开源项目个性化学习助手。**

---

# 12. Stage 0 最终总结

学习完 Stage 0 后，我目前对 Agent 的理解是：

> **Agent 是一个围绕目标运行的系统。模型能够观察当前情况，根据已有信息判断下一步行动，通过工具与外部环境交互，并根据工具返回的新结果继续调整自己的行动，直到任务完成。**

需要特别记住：

```text
用了 LLM ≠ Agent

用了 RAG ≠ Agent

用了 Tools ≠ Agent

有循环 ≠ Agent
```

真正关键的是：

> **模型能否根据环境反馈，动态决定下一步行动。**

我的判断原则：

```text
普通代码能解决
→ 用普通代码

流程固定
→ 用 Workflow

需要根据反馈不断调整
→ 用 Agent

确实需要多个独立角色协调
→ 再考虑 Multi-Agent
```

---

## Stage 0 完成情况

* [x] 区分 chatbot、workflow、agent、multi-agent
* [x] 理解 agent 的基本循环：observe → think → act → observe
* [x] 明白什么时候不该使用 agent
* [x] 学习 Anthropic：Building effective agents
* [x] 学习 OpenAI：A practical guide to building agents
* [x] 完成「为什么我的场景需要 Agent，而不是普通 Workflow？」学习笔记
