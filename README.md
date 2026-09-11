## ⚠⚠⚠ 首要规则 · 图片入库必须"先下载再上传"(违反直接算失败)

**小红书 / pinterest / 微博 / 抖音 / 百度图片 / Google Images 等平台返回的图片 URL 都是临时链接** —— 有的带防盗链 referrer 校验,有的是短时签名(几分钟到几天就失效),甚至看起来正常的 CDN 也可能后端删源图。

**绝对禁止**的做法:
- ❌ 拿到平台 URL 直接 POST /assets/register-url(**后端已经对 Agent 拒绝这个接口,返回 403,别浪费尝试**)
- ❌ 把平台 URL 存到 asset.sourceUrl 字段
- ❌ 相信"这个链接看起来是长期的"—— 除非是 Muse 已配置的内部 OSS 长期签名 URL,否则一律当临时处理

*唯一正确**的做法(两选一,优先 A):
- ✅ **A · 服务端下载(推荐)**:拿到 URL 列表后 POST /assets/upload-by-url  { urls, folderId? } —— 服务端自己 fetch,存本地/OSS,前端拿签名 URL 读,原 URL 挂了也不影响。
- ✅ **B · Agent 侧下载**:自己 fetch 图片二进制 → base64 → POST /assets(multipart,字段 files)。 只在 A 拿不到(需要登录 cookie / 特殊 header)时用,开销更大。

**Agent 收到 URL → 到调用 upload-by-url 之间不能超过 30 秒**,否则可能已经过期。批量采集时抓一批立刻上传一批,别囤到最后一起传。

看下段提示词是干嘛的
traceId：6a3a70e317891180854771000d51fc

claude-opus-5
用户问“看下段提示词是干嘛的”，意思是让我看看上面那段提示词内容，分析它是做什么用的。那段内容就是他们贴出来的prompt本身，我需要分析并解释它的用途和作用。
这段是一个AI Agent系统提示中关于素材（图片）接入的强制规则片段，属于产品"Muse"（可能是设计/媒体管理工具）的一部分。它规定从社交平台抓取的图片必须由服务端下载并存储，而不能直接引用原始URL，因为平台URL往往带有防盗链、签名时效或CDN失效等风险。规则中详细约束了禁止行为（比如直接把平台URL存为sourceUrl）、要求的两种上传方案（服务端代抓取或Agent端下载后转base64上传），以及严格的时间窗口（30秒内完成上传）和批处理要求，语气上用⚠等符号强调违反即视为失败。

我还注意到这体现了工程设计上"先下载后上传"以保证数据持久性，避免产品后续出现失效图片的问题，同时403拦截也构成了提示层面之外的服务端防御。另外我发现规则里存在一些细节矛盾或空白，比如方案B里base64编码后又用multipart上传的表述略显重复，且没有说明失败重试、去重、文件大小或格式限制等情况，这些可能是值得补充说明的点。最终我打算用结构化、适度篇幅的方式来组织回答。

这段是某个「Muse」产品里 Agent 的系统提示词片段,专门管一件事:Agent 采集到的外部图片,怎样正确入库。

它在解决什么问题
社交平台(小红书 / Pinterest / 微博 / 抖音 / 百度、Google 图片)返回的图片 URL 都是不可长期依赖的:

有 referer 防盗链 → 换个域名加载就 403
是短时签名 URL → 几分钟到几天就失效
就算是看着正常的 CDN → 源图也可能被删
如果 Agent 只把这些 URL 存进数据库,用户过几天回来看素材库就是一片裂图。所以规则的核心目标是:让图片二进制真正落到自己的存储里(本地/OSS),数据库只存自己的资产 ID。

规则本身拆开看
禁止项(3 条)

全屏
复制
禁止	原因
直接 POST /assets/register-url	那是"只登记链接不搬文件",后端已针对 Agent 返回 403
把平台 URL 写进 asset.sourceUrl	等于埋了个定时炸弹
自行判断"这链接看着挺长期"	一律按临时处理,除非是 Muse 自己的 OSS 签名 URL
允许项(2 条,有优先级)

A(首选)POST /assets/upload-by-url { urls, folderId? } —— 把 URL 丢给服务端,服务端自己 fetch 并落盘。Agent 不用搬二进制,开销最小。
B(兜底) 自己 fetch 二进制 → POST /assets(multipart,字段 files)。只在 A 失败时用,比如图片需要登录 cookie 或特殊 header 才能取到。
时效约束
拿到 URL 后 30 秒内必须调 upload-by-url;批量采集要"抓一批传一批",不能囤到最后统一提交——否则前面的签名早过期了。

这段提示词的写法特点
几个明显是踩过坑之后加的:

前置化 + 强标记:放在最前面,用 ⚠ 和"违反直接算失败",因为 LLM 对开头/结尾内容更敏感。
提前堵死错误路径:明确写"后端已经返回 403,别浪费尝试"——省掉 Agent 试错的一轮 tool call 和 token。
给优先级而不是并列选项:A/B 排序 + B 的触发条件,避免 Agent 随机选贵的那条。
纠正模型的"合理推测"倾向:专门写了"别相信链接看起来是长期的",这是在压制模型自作聪明。
提示词 + 服务端硬拦截双保险:光靠 prompt 约束不住,所以 API 层直接对 Agent 拒绝 register-url。
如果要挑毛病
B 方案表述矛盾:"base64 → POST multipart"——multipart 一般传原始二进制,base64 是 JSON body 的做法,这里容易让 Agent 编出错误请求。
30 秒无法自测:Agent 感知不到自己的耗时,不如改成硬性流程约束(如"每次 search 后下一个 tool call 必须是 upload-by-url,最多 20 个 URL 一批")。
缺失兜底逻辑:没说部分 URL 下载失败怎么办(重试?跳过?回报用户?)、去重规则、大小/格式限制。
folderId 何时该传没说明。
需要的话我可以帮你把这段改写得更严谨(补失败处理、批量上限、字段示例)。

回答评价:
这段是某个「Muse」产品里 Agent 的系统提示词片段；具体怎么理解？ 怎么定义【产品】，你说里面有一个agent，指的是像Claude code或 openclaw 这种吗？
例如我想要做一个某某 agent，具体是指什么？
traceId：c7c1b24917891183579791001d51fc

claude-opus-5
The user's asking a conceptual question about what "产品" and "Agent" mean in this context, and whether it's similar to something like Claude Code or an open-source assistant project — they want a clear explanation of these terms.
I want to clarify the layered architecture: Muse is a product (an asset/inspiration library SaaS with its own frontend, API, and database), and the Agent is a feature embedded inside that product, using domain-specific tools like search and the product's own API endpoints, with a system prompt encoding business rules. This is different from general-purpose agent products like Claude Code where the tool itself is generic and the "product" is the agent — Muse's agent is a vertical, embedded agent whose tools are the product's own APIs, versus workflow-style automation.

So when someone says "I want to build an X agent," this really means specifying concrete deliverables: the job to be done (who, in what scenario, delivering what), the toolset that defines the agent's capabilities, the system prompt with rules and constraints, the runtime/harness handling the loop and context management, state and memory, observability and evaluation, permissions and security guardrails, and the final delivery format like chat, background task, or CLI.

The key insight worth emphasizing is that most of the actual work in building an agent isn't in the prompt — it's in tool/API design, the harness, and evaluation. The Muse example is proof: that prompt rule exists because the tool boundary was poorly designed initially, so they patched it with a prompt constraint plus a hard 403 block server-side.

I should give a concrete filled-in example of this template, maybe using something like a "competitive intelligence agent" to make it tangible for the user.

I'll note that I should mention MCP as the current standard way to expose tools to agents, while hedging that my knowledge has a cutoff and the specific 2026 tooling landscape may have shifted since then — keeping this brief rather than over-hedging.

I also want to be careful about how I characterize "OpenClaw" — I have some awareness of it as a lineage (Clawdbot → Moltbot → OpenClaw), an open-source personal AI assistant project, but I should frame it loosely as a general-purpose personal assistant agent rather than asserting confident specifics.

For structuring the response, I'm planning to break down the layers of product/agent/prompt relationships (maybe with a diagram or table), define what a "product" means and where the agent sits within it, outline the three forms an agent can take and classify Claude Code, OpenClaw, and Muse accordingly, then give a checklist/template for defining "I want to build an X agent," followed by a filled-in example, and end with a reminder that the focus shouldn't be on the prompt. Given this is a conceptual teaching question, it deserves a well-structured answer with headings and decent length.

Adding table comparison...

Now I'm sketching a checklist for building a vertical agent: job definition, toolset, constraints, operating form (trigger + UI), state/memory, deliverable and success criteria with eval, and permission guardrails. I want to fill this template with a concrete example, maybe a moodboard agent for designers to keep the Muse theme going.

I'll fill the template with the Muse example since it ties back to existing context, then add one line noting readers can swap in their own domain by just changing the toolset and constraints. I should also close with a note that regardless of ecosystem shifts, the invariant is tool boundaries plus guardrails, with a light hedge about my knowledge cutoff.

一、先把那段提示词所处的层级摆清楚
你贴的那段,不是一个独立的"Agent 产品",而是某个 SaaS 产品内部,一个功能模块的行为规则。大致结构:

Muse(产品 = 一个素材/灵感库 SaaS)
├─ 前端:素材库、文件夹、画板、搜索
├─ 后端 API:/assets、/assets/upload-by-url、/assets/register-url、/folders …
├─ 存储:本地磁盘 或 OSS + 数据库(asset 表里有 sourceUrl 字段)
└─ Agent(产品里的一个功能,不是产品本身)
     ├─ 一个 LLM
     ├─ 工具集:网页搜索 / 平台采集 / **以及上面那套自家 API**
     ├─ System Prompt ←(你贴的那段只是其中一小节)
     └─ Loop:想 → 调工具 → 看结果 → 再想 → 直到任务完成
它服务的用户动作大概是这样一句话:"帮我去小红书找 20 张日式极简客厅的参考图,存到『客厅』文件夹里。"

Agent 收到后自己拆解:搜索 → 拿到一堆图片 URL → 调 upload-by-url 入库 → 回报结果。而那段提示词就是在管其中最容易出错的一步:别偷懒只存链接,要把图搬进自己家。

所以这里的"产品"= Muse 这个素材库工具;"Agent" = 它内置的一个会自己调 API 干活的助手功能。

二、所以它和 Claude Code / OpenClaw 是同类吗?
不是同一类。它们的差别不在技术,而在"谁提供工具、谁定义任务"。

全屏
复制
通用型 Agent(Claude Code、个人助理型开源项目等)	嵌入式垂直 Agent(Muse 这个)
Agent 和产品的关系	Agent 就是产品	Agent 是产品里的一个功能
工具来源	通用能力:shell、读写文件、浏览器、外接 MCP	主要是自家后端 API
任务来源	用户每次自带,无限开放	产品预设的少数几类 job
System Prompt 长什么样	长,讲通用方法论(怎么探索代码库、怎么规划)	短,塞满业务规则和红线(就像你贴那段)
出错的代价	用户当场看见,自己纠正	脏数据静默进生产库,几天后才炸
因此的设计取向	尽量放权	Prompt 约束 + 服务端硬拦截(那个 403 就是)
能不能做 eval	难,场景太泛	容易,场景固定,能刷成功率
一个判别小技巧:看它的工具清单是"通用的"还是"某家公司的"。 通用 → 它是个 Agent 产品;某家公司自己的 API → 它是那家产品的 Agent 功能。

(顺带说,还有第三类容易混:n8n / 工作流那种。流程是人画死的,LLM 只在某个节点里做分类或改写。那个严格说不叫 Agent,因为没有"自己决定下一步调哪个工具"的循环。这个循环才是 Agent 的分界线。)

三、"我想做一个 XX Agent" 到底要定义什么
先记一个式子,它能帮你判断自己是不是在做 Agent:

Agent = LLM + 工具集 + 循环 + 目标与约束(+ 记忆)

然后,当你说"我要做一个 XX Agent",实际上要交付的是下面 7 件东西。注意:prompt 只占其中 1 件,而且往往是最不费力的那件。

1. Job:一句话说清"谁、在什么场景、替他做什么、产出什么"
不合格:"做一个营销 Agent"。
合格:"帮独立设计师,在做方案初期,从小红书/Pinterest 采集参考图并整理成分类画板,产出一个可分享的 moodboard 链接。"
这句话写不出来,后面全是空转。

2. 工具集(这是 Agent 的本质,90% 的设计工作在这)
每个工具要定清:名字、入参、返回、失败长什么样、要不要暴露给 Agent。
Muse 那段提示词恰好暴露了一个反面教材:register-url 这个工具本来就不该给 Agent。他们的补救是"prompt 里骂一句 + 后端返 403"。工具边界一开始切对,那段警告就不需要存在。

3. 约束与红线
业务铁律(如"必须先落盘再入库")、绝对禁止项、优先级(A 优于 B)、时效要求。你贴的那段就是这一项的产物。

4. 运行形态
谁触发?(用户对话 / 按钮 / 定时 / webhook)
在哪呈现?(聊天框 / 侧边栏 / 后台任务 + 完成通知 / CLI)
同步还是异步?(采集 20 张图必然是异步任务,不能让用户干等)

5. 状态与记忆
本次任务进度存哪(断了能不能续)、用户长期偏好记不记、跨会话要不要认得上次的项目。

6. 成功判定与评测集
"这一单算成功"的机器可判条件是什么?攒 20~50 个真实用例当回归集。没有这个,你只能靠"我试了两次感觉还行"来迭代,改 prompt 就变成拆盲盒。

7. 权限与硬护栏
Agent 用谁的身份调 API、能碰哪些数据、速率上限、哪些操作必须人确认。
核心原则:凡是"不许做"的事,一定要在服务端拦,不能只写在 prompt 里。 LLM 是概率模型,prompt 是建议不是保险丝。Muse 那个 403 就是正确姿势。

四、拿 Muse 当例子把这个模板填一遍
全屏
复制
项	内容
Job	帮设计师从社交平台采集参考图 → 分类 → 存入素材库
工具集	search_platform(query)、fetch_image(url)、upload_by_url(urls, folderId)、create_folder(name)、POST /assets(兜底)。register_url 不暴露
红线	图必须落自家存储;30 秒内提交;抓一批传一批;sourceUrl 不存平台链接
运行形态	聊天框下单 → 转后台异步任务 → 进度条 → 完成通知
状态	任务表记 已抓/已传/失败 的 URL,支持续跑
成功判定	目标张数达成 & 全部图片 7 天后仍可访问 & 落对文件夹
护栏	服务端对 Agent 身份拒 register-url(403);单次上限 N 张;域名白名单
换到你的领域,只需要换掉"工具集"和"红线"两栏,其余 5 栏的结构完全一样。比如做一个"竞品价格监控 Agent",工具集就变成 search_sku / fetch_price_page / write_to_sheet / send_alert,红线变成"价格必须带抓取时间戳和快照截图,不许凭历史数据推测当前价"。

五、给你两句最省时间的提醒
先想清楚"要不要 Agent"。 如果流程固定、分支不超过三条,写死的工作流 + 一两个 LLM 调用又快又稳,成本和不确定性都低一个数量级。Agent 的价值只在步骤数和顺序事先不知道的时候才成立。

重心放在工具设计和 eval,不要放在雕 prompt。 工具的粒度、返回的信息量、报错文案够不够 Agent 自己纠错,这些对成功率的影响远大于提示词的措辞。你贴的那段之所以要写成"违反直接算失败"这么重的语气,某种程度上正是因为工具层没设计好,只能靠 prompt 打补丁。

关于 2026 年当下的具体框架和生态选择(工具暴露标准、可用的 harness 等),我的信息可能已经过时,建议你直接看现在主流方案的最新文档。但上面这套"Job → 工具 → 红线 → 形态 → 状态 → eval → 护栏"的拆法是不依赖具体技术栈的,可以直接拿去套。

如果你说一下你想做的具体是哪个场景,我可以帮你把这七栏当场填出来,顺便判断一下它到底该做成 Agent 还是工作流。
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
