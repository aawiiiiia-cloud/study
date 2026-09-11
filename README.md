<think>
The user's frustration is clear here, so I need to clarify my ambiguous statement — I meant the workflow's cost and uncertainty are an order of magnitude lower than the agent's, not some absolute comparison, and I should back that up with concrete examples. I also need to address that they already have Codex and Claude Code installed, so my answer should account for that existing setup.

For the "when to build an agent" question, the honest answer is: for personal one-off tasks, you don't build a separate agent — Claude Code itself already is your agent, you just configure it with tools, skills, MCP, and a CLAUDE.md file. You actually need to build a standalone agent when it must run for other users who lack your terminal/API keys/judgment, run unattended on a schedule or event trigger, operate at scale with predictable costs, get embedded in a product's UX, need guardrails around silent costly failures touching production data, require auditability across multiple tenants, or when latency/cost budgets make an exploratory Claude Code session impractical. The key insight worth emphasizing is that 90% of "I want to build an agent" requests from a single person are really just "configure Claude Code properly" — skills, MCP, subagents, slash commands, hooks.

I want to compare a fixed workflow versus an agentic approach for a concrete task like scraping and categorizing social media posts daily. The scripted workflow makes one or two predictable LLM calls with bounded token costs and clear failure points, while an agent loops through tool calls an unpredictable number of times, accumulating context and token costs that can balloon dramatically compared to the simple version.

Beyond cost, the failure modes differ too: the scripted version fails in reproducible, debuggable ways, while an agent can loop indefinitely, skip steps, or hallucinate parameters, with the same input sometimes producing different outputs. So the real question becomes whether the workflow can be diagrammed in advance with few branches — if so, hardcode it; if the branching is genuinely unpredictable, that's when agentic flexibility earns its cost.

I also want to reframe the second point: Claude Code or Codex is already a general agent, so most of what people call "building an agent" on their own machine is really just configuring one — adding tools via MCP, encoding domain knowledge through CLAUDE.md or skills, or fixing workflows into slash commands or subagents, all of which take hours, not weeks. The cases that genuinely require writing your own harness, managing your own loop, and deploying it yourself are much rarer, and I want to lay out the specific triggers for those.

The list I'm building includes: when the users aren't you and lack your terminal or credentials, when nothing is actively watching the process, when you need high concurrency with cost controls, when multi-tenant auditing and permissions matter, when the agent needs to live inside a product UI rather than a chat box, and when silent failures could corrupt production data — pointing back to that 403 example with Muse as a case of missing server-side guardrails.

If none of these apply, the answer is simple: just use Claude Code directly instead of building something custom. A middle path exists too — use Claude Code as a prototyping tool first, manually run through the workflow, capture what happens, then decide whether to formalize it into a product feature. There's also a "half build it yourself" route where Claude Code can be invoked programmatically in headless mode via something like an SDK, which avoids rewriting the whole harness from scratch. And going back to the Muse example, the real constraint there is obvious: Muse's end users could never be expected to install Claude Code themselves.

Now I want to structure the fuller answer: first, clarify the vague distinction I made earlier with a proper A vs B comparison table and a one-line decision rule, plus a note that agents aren't inherently bad — you're just paying a premium for not knowing what your next step is. Second, for people who already have Codex or Claude Code, I should correct the framing that Claude Code already is an agent, so often what you actually need is configuration rather than building anything new. I'll lay out three tiers — configure, half-build via headless SDK, or fully build — followed by a seven-point checklist of trigger conditions, circling back to the Muse example, and closing with a single decisive line.

Given the user's annoyance with vagueness, I want to stay concrete, use tables, avoid sounding preachy, and not over-apologize while still owning the ambiguity in my earlier answer. Length should be medium-long but tightly structured, with real numbers and examples throughout.



</think>

## 1. 我那句话确实写含糊了,补清楚

"低一个数量级"的**比较对象是:同一个任务,用 Agent 实现 vs 用写死的工作流实现**。是工作流比 Agent 低,不是跟别的什么东西比。

拿同一个任务对照:**"搜 20 张日式极简客厅的图,分类后存进素材库"**

| | 写死的工作流 | Agent |
|---|---|---|
| 实现 | `search(q)` → 取前 20 条 → **1 次 LLM 调用**批量分类 → `upload_by_url()` → 完 | 给它工具和目标,它自己决定先搜什么、够不够、要不要换关键词再搜、怎么分组 |
| LLM 调用次数 | 固定 1~2 次 | 不确定,可能 5 次,也可能 25 次 |
| token 消耗 | ~3k,可精确预算 | 每一轮都要重带全部对话历史和工具返回 → **累积增长**,常见 50k~200k |
| 延迟 | 几秒 | 30 秒到几分钟 |
| 同一输入跑两次 | 结果一致 | 结果可能不同(搜的词不同、分类命名不同、张数不同) |
| 出错时 | 报错停在具体某行,看日志 | 可能自己"绕过去"了、可能死循环、可能编个不存在的参数,要读整条 trace |
| 成本能不能报价 | 能。"每单 0.002 美元" | 不能。只能给区间,还可能被某个死循环打穿 |

所以"低一个数量级"具体指这三项:**token 成本(约 10~50 倍差距)、延迟(约 10 倍)、结果方差(从 0 到不可忽略)**。数字是示意量级,不同任务会浮动。

**判定方法只有一句话:你能不能事先把流程图画出来?**

- 能画出来,分支不超过三条 → 写死。你付不着那个溢价。
- 画不出来,因为"下一步取决于上一步返回什么" → 才需要 Agent。

Agent 不是更高级,它是**你为"事先不知道步骤和顺序"这件事付的溢价**。不确定性不存在的时候,溢价就是纯浪费。

---

## 2. 电脑上已经有 Codex 和 Claude Code,什么时候才需要"做"一个 Agent

**先纠正一个概念:Claude Code 本身就是一个 Agent。**它有 LLM、有工具(读写文件、跑命令、搜网页)、有循环。所以你说"我想做一个 XX Agent"的时候,九成情况下真实需求是下面三层里的前两层,而这两层都**不叫"做",叫"配置"**:

| 层次 | 做什么 | 成本 |
|---|---|---|
| **L1 配置**(绝大多数情况) | 给 Claude Code 写好领域规则文件、接上 MCP 工具、存成可复用的命令/技能 | 几小时 |
| **L2 半自建** | 把 Claude Code / Codex 以 headless、SDK 的方式**被你的程序调用**,外面套一层触发器、重试、状态记录 | 几天 |
| **L3 全自建** | 自己写 loop、自己管 context、自己定义工具协议、自己部署 | 几周起 |

你贴的 Muse 那段属于 L2/L3。

### 什么时候必须往 L2/L3 走

下面 7 条,**命中任意一条**才需要;**一条都不中,就老老实实用 Claude Code,不要动手做。**

1. **使用者不是你。**Muse 的用户是设计师,他们不会为了整理素材库去装 Claude Code、配 API key、看终端报错。你必须把能力包成产品里的一个按钮。——这就是 Muse 为什么非做不可。
2. **跑的时候没人看着。**定时任务、webhook 触发、后台批处理。没人在旁边纠正,你就得自己写重试、幂等、超时、失败告警、断点续跑。
3. **要并发跑很多次。**一天 10 次可以手动;一天 1000 次,你需要成本可预算、可限流、可熔断。
4. **需要权限和审计。**多租户,谁能碰哪些数据,出事要查是谁的哪次操作干的。
5. **要嵌进产品 UI。**交付形态不是聊天框,而是进度条、结果卡片、可分享链接。
6. **失败是静默的,而且写生产数据。**这是最容易被低估的一条。Claude Code 出错你当场就看见了;后台 Agent 把 20 条脏数据写进数据库,你三天后才发现。这时候你需要的是**服务端硬护栏**——就是 Muse 那个对 Agent 直接返 403 的设计。
7. **延迟或成本有硬预算。**用户等不了两分钟,或者单次不能超过某个金额。Claude Code 那种"自由探索"的开销你控不住。

### 反过来,这些情况明确不要做

- 只有你自己用 → 配 L1
- 一周跑几次,你都在电脑前 → 配 L1
- 流程你已经完全清楚了 → 写脚本,连 Agent 都不用
- "我想学怎么做 Agent" → 那是另一回事,但别拿真实业务当练习场

---

## 一个实操建议

**先用 Claude Code 手动把这件事跑通三五遍。**

跑的过程里你会自然得到三样东西:该有哪些工具、哪几步是固定的、哪几步真的需要临场判断。然后:

- 发现全程都是固定的 → 写成脚本,收工。
- 发现只有一两步需要判断 → 工作流 + 那一两步塞个 LLM 调用。
- 发现步骤数和顺序每次都不一样,而且**要给别人用或者要无人值守** → 这时候才开始做 Agent,而且你手上已经有了现成的工具清单和红线清单(就是 Muse 那段东西的来源)。

顺便一提,2026 年这会儿具体有哪些 SDK、MCP 生态到什么程度、Claude Code 的哪些扩展机制叫什么名字,我的信息可能已经过时了,这部分你直接查最新文档。但上面这套判定逻辑不依赖具体工具。

---

想更具体的话,你告诉我你打算做的那个"XX Agent"是干什么的、给谁用、多久跑一次,我直接告诉你该停在 L1 还是必须做到 L3。
