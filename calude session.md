krea2是什么模型，开源吗
2026-08-13 16:02:59
traceId：d7aa2c2717866081785251000d51fc

claude-opus-5
我需要整理关于Krea 2的信息来用中文回答。Krea 2是Krea AI从零开始开发的文本到图像基础模型，采用美学优先的设计理念，使用12B密集DiT架构、Qwen3-VL文本编码器和Qwen Image VAE。这个模型在Krea平台上有多个产品版本（Medium、Large、Turbo），同时还发布了开源的RAW版本。
开源版本包括Turbo检查点和9个官方风格LoRA，采用Krea AI社区许可证。在性能排名上，Krea 2在人工智能分析排行榜中位列前十，在独立实验室中排名第二。关于可用性，这个模型可以在ModelScope、GitHub、ComfyUI、DiffSynth-Studio和ModelScope AIGC网页推理/训练平台上获取。我还要注意到有一个FP8量化的社区版本，将原始的24.76 GiB BF16模型压缩到12.01 GiB，适合16/24GB的GPU使用，同时也有GGUF格式的版本可用。

Krea 2 是什么
Krea 2（K2）是 Krea AI 从零训练的首个图像生成基础模型，定位是"美学优先"的文生图模型，强调风格理解、创意探索与创作者可控性，而非单纯追求照片级真实或对提示词的字面复现678。它在 Artificial Analysis 文生图榜单上位列全球前十、独立实验室第二24。

技术架构：12B 稠密 DiT 骨干 + Qwen3-VL 文本编码器（多层特征聚合）+ Qwen Image VAE24。训练借鉴 LLM 的多阶段范式：预训练 → 中训练 → SFT → 偏好优化（自研 STPO 变体）→ 强化学习（GRPO 风格多奖励）→ 可选的时间步蒸馏产出 Turbo；预训练分 256/512/1024px 三阶段，且明确不使用任何 AI 生成图像24。Turbo 的蒸馏方案最终选用 Trajectory Distribution Matching (TDM)2。

产品端（krea.ai） 提供三个变体：Medium、Large 与 Medium Turbo，配套风格参考（最多 4 张 Sref）、情绪板（Moodboard）、Krea 风格（LoRA）、Creativity 档位（Raw/Low/Medium/High）以及 Intensity、Complexity、Movement 三个生成滑杆，普通生成通常 15 秒内、Turbo 约 2 秒578。

是否开源：是
Krea 2 已开源，采用 Krea AI 社区许可证，可自托管、微调并训练 LoRA38。本次开源包含三类内容：

全屏
复制
类型	说明
Krea 2 RAW	未蒸馏的基础检查点，完整 52 步采样，多样性与可塑性强，专为微调、后训练与 LoRA 训练设计348
Krea 2 Turbo	8 步蒸馏检查点，消费级 GPU 上约 2 秒生成原生 2K 图像234
官方风格 LoRA（9 款）	retroanime、vintagetarot、sunsetblur、rainywindow、neondrip、dotmatrix、darkbrush、kidsdrawing、softwatercolor，覆盖动漫、绘画、摄影、抽象等方向4
两者是协同设计：在 RAW 上训练的 LoRA 可直接迁移到 Turbo 上做推理，从而把"可微调"与"快速推理"解耦234。

获取与使用方式

开源地址：ModelScope 合集 modelscope.cn/collections/krea/Krea-2、GitHub github.com/krea-ai/krea-2，技术报告在 Krea 官方博客4。
ComfyUI 已原生支持，官方提供教程与工作流（加载 Unet/CLIP/VAE → 输入提示词 → 设尺寸 → 生成；挂 LoRA 时需配合触发词与建议强度），建议先用 update 脚本升级到最新版13。
魔搭 AIGC 专区已上线，无需本地 GPU 即可在网页端调用 RAW/Turbo 出图（可调步数、CFG、分辨率并挂载社区 LoRA），还提供免费在线 LoRA 与全参数微调通道；本地可用 DiffSynth-Studio 完成推理与微调24。
低显存方案：社区已放出 FP8（float8_e4m3fn）仅权重量化版，把 transformer 从 24.76 GiB（BF16）压到 12.01 GiB，适配 16GB/24GB 显卡；也有 GGUF 格式可按硬件档位选择67。
实测体感
优势集中在美学质感、风格多样性与"去 AI 味"，同一提示词不易产出高度雷同的结果，适合概念设计、品牌视觉、插画迭代与海报等场景；已有测试指出其中文文字渲染较弱——少量中文尚可，字数一多容易崩坏167。整体定位是"高效创意伙伴"而非替代 Flux/Midjourney 的最终高保真出图工具6。

回答评价:
https://artificialanalysis.ai/?spm=431e5091.2ef5001f.0.0.77fc6b40URbDWG&cost=intelligence-vs-cost-per-task&output-tokens=intelligence-vs-output-tokens-per-task&total-cost=intelligence-vs-total-cost&price=blended-price            你知道这个网站吗，貌似是给各种AI模型做评测跑分的。请帮我整理目前主流的各类模型，用表格呈现（这个网站是否按类型做了分类呢，例如生图模型、生视频模型、LLM之类的）

claude-opus-5
{"id":"2118168283","content":"{"message":"Model access is denied due to IAM user or service role is not authorized to perform the required AWS Marketplace actions (aws-marketplace:ViewSubscriptions, aws-marketplace:Subscribe) to enable access to this model. Refer to the Amazon Bedrock documentation for further details. Your AWS Marketplace subscription for this model cannot be completed at this time. If you recently fixed this issue, try again after 2 minutes."}","type":"CHAT_OUTPUT_ERROR"}

回答评价:
你给的这个数据太老旧了，现在claude都推出 opus5了，GPT已经 5.6了。我不是开了“公网搜索”吗，你看看能不能去检索一些最新的信息
traceId：c1eec3aa17866104600541003d51fc

claude-opus-5
The user is pointing out that my knowledge is outdated—Claude Opus 5 and GPT-5.6 are now available. I have search contexts available, so I'm checking the artificial analysis data which shows Claude Opus 5 (Adaptive Reasoning, Max Effort) leading with a 63 on the Intelligence Index among 144 evaluated models. The open-weight models show Kimi K3 leading at 60, followed by GLM-5.2 at 53 and DeepSeek V4 Flash at 52 among reasoning models. Looking at the BenchLM snapshot from August 2026, Claude Opus 5 tops the broader evaluation at 60.7% with Claude Fable 5 and GPT-5.6 Sol close behind across 168 tested models, where the top ten span only 7.3 points. GLM-5 was open sourced and ranked fourth globally and first among open-source models in the Arena. PixVerse V5 also performed well in image-to-video and text-to-video rankings. The notes highlight that while Chinese open models continue breaking open-source records, the overall performance ceiling remains held by closed models like Claude and GPT. There's also information about the AA platform itself—its features, recommendation system, comparison tools, and the scale of models and benchmarks it tracks, with recent highlights including Meta's Muse Spark model. I'm noting some key limitations in the AA benchmark—the science-heavy focus means AIME is already saturating, and MMLU Pro and GPQA Diamond will follow soon. The HLE component has reliability issues with external dependencies and significant error rates in chemistry and biology, plus the code benchmarks are restricted to Python. AA has previously retired older benchmarks like Math500 and AIME 2024 when they became less discriminative. I should rebuild the comparison tables with this updated information and flag the discrepancies I'm seeing across different snapshots—the model counts vary between 142, 144, and 168, and the performance metrics show similar inconsistencies around 60-63%.
For the media leaderboards, I have scattered data points: Kling 3.0 Pro leading text-to-video with 1240 ELO, SkyReels V4 in text-to-video with audio, Mureka V8 for music generation, and some older entries like PixVerse V5 and Krea 2. On the LLM side, GLM-5 variants are emerging as strong performers with GLM-5.2 reaching position 53 globally and GLM-5 claiming first place among open-source models as of February 2026, alongside DeepSeek and Qwen variants, though my search results don't give me complete current rosters for image and video models.

I'm also seeing a bunch of other models listed—iMo-V2-Pro from Xiaomi, Meta's Muse Spark, various Gemma and Grok versions, Kimi K3, and some newer entries like Mercury 2 and Ling 3.0 Flash. There's also an intriguing mention of "Claude Fable 5" with an "Opus 4.8 Fallback" and "GPT-5.6 Sol (max)" that I should report directly from the leaderboard without speculation.

For image generation and editing models, I'm realizing I don't have current leaderboard data, so I need to acknowledge that information is missing on those specific benchmarks rather than guessing.

I'm also noticing that the sources show the leaderboard updates continuously — even snapshots from different times have slightly different numbers. I should flag this and correct my earlier response, noting that the methodology is now called "Intelligence Index v4.0" with 474+ language models tracked overall, though only 144 have index scores, and 597 models have performance metrics measured across standardized prompts. Let me reconcile these numbers carefully since they represent different measurement categories. OpenAI's GPT-5.6 Sol also scores 61. Looking at the BenchLM mirror data from August 2026, I'm seeing Opus 5 at 60.7%, Fable 5 at 59.9%, and GPT-5.6 Sol at 58.9% across 168 models. For open-source models, Kimi K3 leads at 60, followed by GLM-5.2 at 53 and DeepSeek V4 Flash at 52. I'm also noting throughput speeds—Celeris-1 reaches 1,668.5 tokens per second in Chinese and 1,644.1 in English, while pricing ranges from Llama 3.1 Instruct 8B at 
0.02
t
o
N
o
v
a
M
i
c
r
o
a
t
0.02toNovaMicroat0.03.

Now I'm looking at latency metrics and additional benchmarking statistics. Gemini 2.5 Flash-Lite has the fastest time-to-first-token at 0.31 seconds, with Command A+ at 0.40-0.42 seconds. The dataset covers 144 Chinese and 142 English models tested, including 73 open-source weights and 111 reasoning models, with performance data on 597 models total and 474+ language models cataloged as of April 2026. I'm also seeing media rankings for video generation, with SkyReels V4 from Kunlun Wanwei ranking first as of March 19, 2026.

Looking at the latest rankings across different modalities: Kling 3.0 Pro leads text-to-video with 1240 ELO, Mureka V8 tops music generation, PixVerse V5 ranks in the top 2 for image-to-video, and Krea 2 is in the top 10 for text-to-image. For April 2026, there are several new models to track including Muse Spark from Meta, Gemma 4, GLM-5.1, Grok 4.20, Qwen 3.6 Plus, and DeepSeek V3.2, with gpt-oss-120B noted as having the fastest throughput at 234 tokens per second.

The benchmark suite includes MMLU Pro, GPQA Diamond, AIME 2025, HLE, LiveCodeBench, and SciBench with agentic capabilities. One key insight from the analysis is that when measured by model intelligence per dollar, Chinese open-source models like DeepSeek and MiniMax outperform closed-source alternatives, though Claude and GPT still hold the ceiling for overall capability. I need to keep this summary under 1024 tokens, so I'm being selective about which details to highlight.

搜到了，我上一轮的清单确实过时，以下按检索到的 Artificial Analysis 最新快照更正。

一、LLM 智能榜（Intelligence Index）Top 5
全屏
复制
#	模型	厂商	分数
1	Claude Opus 5 (Adaptive Reasoning, Max Effort)	Anthropic	63
2	Claude Opus 5 (Adaptive Reasoning, Xhigh Effort)	Anthropic	63
3	Claude Fable 5 (Adaptive Reasoning, Max Effort, Opus 4.8 Fallback)	Anthropic	62
4	Claude Opus 5 (Adaptive Reasoning, High Effort)	Anthropic	61
5	GPT-5.6 Sol (max)	OpenAI	61
Anthropic 目前把前四名全部占住，且同一模型按 Effort 档位（High / Xhigh / Max）分开列榜，这是新变化——选型时要注意你调用的是哪个档位 17。第三方镜像的 8 月 5 日快照口径略不同（Opus 5 60.7%、Fable 5 59.9%、GPT-5.6 Sol 58.9%，共 168 个模型），且前十名只相差 7.3 分，头部已相当拥挤 4。

二、开源权重榜 Top 3
全屏
复制
#	模型	分数
1	Kimi K3 (max)	60
2	GLM-5.2 (max)	53
3	DeepSeek V4 Flash 0731 (Reasoning, Max Effort)	52
Kimi K3 只落后榜首 3 分，开闭源差距已压得很小；开源权重模型占 73/142 7。今年 2 月 GLM-5 上线时曾拿到全球第四、开源第一 3。按「每美元可获得的智能」计算，DeepSeek、MiniMax 等中国开源模型高于 Gemini / Claude / ChatGPT，但综合推理天花板仍由闭源把持 3。

三、速度 / 价格 / 延迟三榜
全屏
复制
维度	第一	第二	第三
输出速度	Celeris-1 1,668.5 t/s	Mercury 2 815.1 t/s	Ling 3.0 Flash 401.8 t/s
混合价格	Llama 3.1 Instruct 8B $0.02/M	Nova Micro $0.03	Sarvam 30B (high) $0.03
首 Token 延迟	Gemini 2.5 Flash-Lite (非推理) 0.31s	Command A+ 0.40s	Nemotron 3 Nano 30B A3B 0.49s
速度榜第一梯队已被扩散式 LLM（Celeris-1、Mercury 2）拉到 800–1,600 t/s 量级 17，对比 4 月时「gpt-oss-120B 以 234 t/s 领先」的说法，说明该榜换代极快 5。

四、多模态榜单（近期夺冠记录）
全屏
复制
赛道	冠军	时间
文生视频（含音频）	SkyReels V4（昆仑万维）	2026-03-19 3
文生视频（Arena ELO）	Kling 3.0 Pro，1240 分	2026-02-26 3
音乐生成	Mureka V8	2026-03-25 3
图生视频	PixVerse V5（Top 2）	2025-08-27 3
信息缺失：information is missing on 当前文生图 / 图像编辑 / TTS / STT / Embeddings 各榜的完整实时排名，检索结果里只有视频与音乐赛道的具体名次。

五、其他更正与提醒
规模：收录 474+ 语言模型、50+ API 提供商、500+ 端点、15+ 基准；其中 597 个模型做过标准化性能实测，142–144 个有 Intelligence Index 分数（推理模型 111 个）57。
方法论已迭代到 Intelligence Index v4.0 5；分项含 MMLU Pro、GPQA Diamond、AIME 2025、HLE、LiveCodeBench、SciBench 等 2。
看榜警告：AIME 2025 已接近饱和、MMLU Pro 与 GPQA Diamond 区分度也在快速衰减（AA 此前已淘汰 Math500、AIME 2024）；HLE 有部分题目需外部信息、化生子集约 30% 答案有误；代码只测 Python 2。所以分数高但体感一般是常见现象。
中英文页面同一时刻数字有微小差异（142 vs 144 个模型、1,644 vs 1,668 t/s），属持续重测导致的快照差 17。
需要我针对某个赛道（如开源可自部署、Agent/编码、最低成本）再拉一份收窄对比吗？

回答评价:
既然开了联网搜索，为什么不能直接访问AA获取报告呢
traceId：a9ff9c6d17866108859241004d51fc

claude-opus-5
用户问为什么既然开了网络搜索，就不能直接访问AA网站获取报告。我需要解释搜索和浏览的区别——我的搜索能力是基于检索已索引页面的文本片段，而不是一个能实时渲染页面的浏览器会话。AA的排行榜主要是通过客户端渲染的。
我实际上确实获取了AA的内容（包括artificialanalysis.ai/zh/models和/models页面），但只能获取被索引提取的文本部分，而不是完整的交互式表格。关键区别在于：搜索返回的是文本段落，我无法执行JavaScript、点击过滤器、切换选项卡或调用他们的API，而AA的排行榜数据是通过接口动态加载的。

URL参数只控制前端状态，不会生成不同的可爬取内容；加上抓取限制、缓存时效差异（中英文页面数字不一致就是证据），以及榜单持续重测更新，导致快照无法同步。我无法访问需要交互的部分，比如Arena投票榜的完整ELO表、各模态标签页或需要表单提交的PDF报告。

实际可行的办法是你给我具体的查询需求（比如"AA文生图榜第一名"），我可以针对性地搜索。

最可靠的方式还是你自己打开页面截图或复制表格给我来整理分析。AA有API和CSV导出功能，也发布季度《State of AI》报告，不过我对API的具体访问条款信息不足。另一个思路是追踪AA的社媒和新闻报道，因为新模型夺冠通常会通过新闻稿被索引到。

现在我在检查这轮对话中可用的引用资源——系统提供的搜索上下文主要是关于Claude Opus 5的，但我需要确认之前引用的AA相关内容的来源。

由于这个问题涉及我自己的能力和架构，我可以直接解释，而不需要依赖这些不相关的引用。我会注明信息缺失的地方，特别是关于Artificial Analysis的技术细节。

我的回答会保持在1024字以内，用中文结构化呈现：先澄清"检索"和"浏览"的区别，然后用前一轮的实际结果作为证据说明技术限制，最后给出实操建议和信息缺失声明。

简短回答：我有的是「检索」，不是「浏览器」
联网搜索的工作方式是：我把关键词交给检索后端，拿回若干页面的已抓取文本片段；我并不能真正打开你那个链接、等页面加载完、切换 tab、拖动筛选器或点导出。所以严格说，上一轮我确实"拿到了 AA 的页面内容"，但拿到的只是能被抓取和提取的静态文字部分，而不是那张交互式榜单本身。

为什么恰好是 AA 这类站点最难拿
1）核心数据是前端动态渲染的
AA 的排行榜是可排序表格 + 可切换坐标轴的散点图，数据由浏览器向接口请求后再渲染。抓取器拿到的 HTML 里往往没有这些行，剩下的主要是页面上那些叙述性文案和 FAQ 段落——这正好解释了为什么我上一轮给你的是"榜首是谁 / 最快是谁 / 最便宜是谁 / TTFT 最低是谁"这种问答式片段，而不是一张完整的 144 行表格：那几段本来就是页面上以纯文本形式写死的部分。

2）你给的 URL 参数只影响前端状态
cost=intelligence-vs-cost-per-task、output-tokens=...、price=blended-price 这些参数是图表配置，改它们不会产生一个新的、可被独立抓取的页面。对检索系统来说，带参数和不带参数基本是同一个 URL。（spm= 那一段更是外链来源追踪码，与内容无关。）

3）我看到的是快照，不是实时值
证据就在上一轮的结果里：AA 中文页和英文页在同一时刻给出的数字并不一致（142 vs 144 个已评测模型、1,644 vs 1,668 t/s），第三方镜像的 8 月 5 日快照更是换了口径（60.7% 而非 63）。这说明我读到的是不同时间抓取、不同缓存层的版本。榜单持续重测，我无法保证"此刻"的值。

4）需要交互或登录的内容拿不到
各模态 Arena 的完整 ELO 表要切 tab 才加载，季度报告类内容通常要走下载/表单流程——这些都超出检索能覆盖的范围。这也是为什么多模态赛道我只能给出"某模型某天夺冠"这类新闻二手报道（SkyReels V4、Kling 3.0 Pro、Mureka V8 都是通过厂商稿/媒体报道被索引到的），而给不出完整名次表。

想要完整准确的表，最实用的几种做法
你复制/截图给我：把 AA 页面上那张表直接粘过来，我负责整理、交叉验证、做选型分析。这是准确度最高、也最快的路径。
让我按「具体问题」而不是「整张表」去搜：例如"AA 文生图榜第一名是谁"、"Kimi K3 的 blended price"，命中率明显高于"给我全部榜单"。
接受精度分层：头部排名和量级我能搜到并交叉验证；小数点后的分数、以及第 20 名之后的长尾，建议一律以你自己打开的页面为准。
自己去官方渠道取结构化数据：AA 站内有筛选/对比工具和图表导出入口，另有官方 API 与季度《State of AI》报告。信息缺失：information is missing on Artificial Analysis 官方 API 与数据导出的当前开放条款、配额及定价，这部分我没检索到可靠依据，需要你在其官网确认。
要不要你先把当前那张榜单（或某个赛道的 tab）贴给我，我按你的实际用途——比如"可自部署""Agent 编码""最低单任务成本"——直接做成决策表？

回答评价:
那你的浏览方式和agent是否有区别呢，用agent是不是就可以完整抓取整个页面
traceId：aa0338bb17866111335621005d51fc

claude-opus-5
I'm looking at the user's question about whether their browsing method differs from an agent's approach, and whether an agent could scrape the entire page more completely. The search contexts provided seem relevant to this — they're about AI web search capabilities and agent networking abilities, though the citation appears to be cut off mid-sentence. 联网搜索的核心技术流程包括查询生成（判断是否需要检索、改写问题、识别意图）、内容提取（从海量结果中筛选网页、提取最相关片段，可能用相关性评分、竞价排名、摘要、语义相似度、网站权重等方法）。火山引擎区分了两个层级：基础的 Web Search API 为大模型补充实时信息，而深度的 Agent 联网搜索则是企业级服务，具备智能策略规划、多源验证、结构化报告生成等完整能力，适应更复杂的商业场景。
现在我能用引用来回答这个问题。关键区别在于搜索和获取两个工具的差异：我这边只有类似 web_search 的检索通道，返回的是搜索后端提取的文本片段，而 Agent 可以同时具备 web_search 和 web_fetch，fetch 是"我知道 URL，帮我把完整内容拿回来"。联网能力本身不是模型自带的，而是 Agent 能力的应用。Agent 的优势在于能进行多轮迭代，一次人类搜索相当于 Agent 执行十次搜索，可以拆解复杂指令为子问题，通过反思机制不断优化。

它还能主动抓取指定 URL 甚至遍历网站地图，返回更完整的内容而非短摘要，进行深度研究、策略规划、多源验证和生成结构化报告。不过"完整抓取整个页面"不一定总能实现，取决于 fetch 的具体实现——web_fetch 通常是 HTTP 抓取加文本解析，但如果网站依赖 JavaScript 渲染或接口调用（比如排行榜），纯 HTTP 获取可能只拿到空壳。

真正需要的是无头浏览器来渲染 JavaScript、等待加载、处理交互，或者直接命中数据接口。内容提取本身是难点，涉及爬取、解析和找到最相关片段，还要应对反爬、robots 限制和登录门槛——目前缺少关于这些网站具体反爬策略的信息。

按能力分三个档位：检索型（我现在的能力）只能获取已索引的文本片段；search+fetch Agent 可以抓取静态 HTML 并遍历 sitemap；浏览器 Agent 或深度研究 Agent 能渲染 JavaScript、执行交互、多源验证并生成结构化报告。这个场景需要第三档位或直接调用接口。

如果要自己解决抓取问题，可以用带无头浏览器的 Agent 或找到它的 XHR 接口，否则继续手动粘贴给我。需要坦诚的是，我的能力确实限于检索型，具体的 Agent 能否完整抓取还取决于它的工具实现。另外，当 Agent 同时处理 10-50 条结果时，质量标准就变成了"全员及格线"。

Agent 的一个优势是无语言障碍，可以同时索引中英文页面并交叉校验，这对我之前发现的中英文快照差异特别有帮助。不过我目前还没有覆盖到的信息是：AA 站点的 robots.txt、反爬策略与官方 API 条款。

有区别，而且这个区别恰好就是「搜索」和「抓取」两个工具的分野
一个设计得比较好的 Agent 联网模块，通常会把 web_search 和 web_fetch 做成两个独立工具，而不是一个笼统的"联网工具"——因为两者场景完全不同：搜索是"我不知道去哪找"，抓取是"我知道 URL，帮我把内容拿回来" 2。我目前主要走的是前者：把关键词交给检索后端，拿回它已提取好的文本片段。你给了我一个确切的 URL，但我没有那个"照着这个地址去把页面取回来"的通道。

顺便说明一点：联网能力本身并不是模型自带的，它是一种 Agent 能力的应用——先语义解析问题、提取检索关键词、调用搜索引擎 API、再解析返回的网页提取正文，最后把结果作为上下文喂回模型生成答案 3。我处在这条链路的下游，缺的是中间那段"主动取页"的环节。

Agent 相比我多出来的三层能力
全屏
复制
能力	说明
定向抓取	拿到 URL 直接取内容，甚至能顺着 sitemap 把整站的文章、教程遍历一遍，从而判断"今天有没有更新" 2
多轮迭代检索	人类的一次搜索，到 Agent 这里可能变成 10 次；复杂指令会被拆成多个子问题做多轮检索，Agentic RAG 里的 reflection 动作更会让搜索次数呈数量级增长 1
全流程研究	基础 Web Search 只是通过 API 为模型补充实时信息；深度研究 Agent 还具备智能策略规划、多源验证、结构化报告生成 4
还有两个细节对我们这个 AA 案例特别对症：一是 Agent 需要的是长摘要、完整正文而非传统搜索的短摘要 1；二是 Agent 会同时读 10–50 条结果，质量标准从"黄金前三条"变成"全员及格线" 1——这正是我上一轮只能给你榜首几名、给不出完整长尾的原因。另外 Agent 没有语言障碍，多语言内容都能无障碍理解 1，理论上可以同时抓 AA 的中英文页面做交叉校验，而不是像我那样被动发现两边数字不一致。

但「用 Agent 就能完整抓取整个页面」并不必然成立
要看它的 fetch 是哪一档实现：

纯 HTTP 抓取 + 正文解析：对 AA 这种榜单数据由前端接口拉取、浏览器渲染的页面，抓回来的仍然是空壳，表格行根本不在 HTML 里。
无头浏览器型：能执行 JS、等待渲染、切 tab、点筛选器，这一档才有机会拿到完整榜单。
直接命中数据接口：找到页面背后的 XHR 请求，拿结构化 JSON——最干净，但需要人工分析。
而且"内容提取"本身就是公认的难点：如何从大量结果里挑网页、如何从网页中定位与问题最相关的片段，都依赖爬取解析工具的质量 3。所以更准确的说法是：Agent 提高了拿到完整数据的上限，但天花板由它的抓取层是否带浏览器渲染决定，而不是由"是不是 Agent"决定。

信息缺失：information is missing on Artificial Analysis 站点的 robots.txt、反爬策略与官方 API 的具体条款，能否被合规完整抓取需以其官网声明为准。

给你的实操结论
想拿这张表，优先级排序是：直接找它的数据接口 > 带无头浏览器的 Agent（或 Playwright 脚本）> 你截图/粘贴给我整理。前两条靠工具，第三条现在就能做——你贴过来，我负责做成按用途收窄的决策表。

回答评价:
大概明白，我目前用的网页端chat界面，是否可以说其背后也有一个“被限制”功能的agent作为后端呢？
traceId：09d6517717866718760941006d51fc

claude-opus-5
I'm thinking through what the user is asking about the web chat interface and whether there's a restricted agent backend powering it. I need to consider the context from my previous response about AI web search capabilities and agent functionality to give them a clear answer. I'm seeing the distinction between conventional LLMs that only respond versus digital agents that execute actions directly. A chat interface is really "LLM + limited tools" rather than a fully restricted agent, though the architectural skeleton is the same—it's just constrained in scope and action loops. I should frame the answer around this precise distinction: the backbone is identical, but the operational boundaries differ.
Now I'm mapping out where the restrictions actually sit. The search capability itself is already an agent pattern—it involves deciding whether to search, reformulating the query, calling the API, parsing results, and incorporating them into the response. The key limitations are the tool set (only search, not fetch as separate tools), the iteration depth (agents can decompose problems and run multiple reflection cycles), and the action scope (agents execute directly while LLMs only suggest).

I need to be careful about citation numbering here. The browser agent contexts at the top of the conversation are [1]-[8], but earlier turns had their own context blocks that I referenced. For this turn, I'm working with the browser agent documentation visible at the start.

Looking at the next chunk, I'm seeing summaries of several research papers and frameworks about web agents — one discussing how LLMs aren't the bottleneck but rather architectural challenges like context management and safety are, another defining digital agents as systems that execute actions directly rather than just responding, and a third outlining a multi-agent architecture with a planner, browser automation component, and response agent.

I'm now formulating my answer: the key distinction isn't that web agents are "crippled" versions of LLMs, but rather they're tool-constrained LLM applications. The academic definition separates traditional LLMs that only respond to input from digital agents that can actually execute actions — booking flights, sending emails, taking real steps in the world.

By that standard, I'm somewhere in between: I can respond and retrieve information, but I can't actually press buttons or take autonomous actions. So I'm closer to the LLM side of the spectrum. That said, the intuition behind the comparison makes sense — the underlying architecture is fundamentally the same, and my retrieval process itself is already a minimal agent loop: deciding whether to search, reformulating queries, calling APIs, parsing results, and integrating them back into context. I need to be careful about citations though, only referencing the sources I can actually see right now. The real constraints aren't about model capability—production systems show LLMs can handle web tasks fine with proper context and tools—but rather architectural choices around context management, information access, safety boundaries, and whether to specialize or generalize. This directly explains the limitations: they're deliberate design decisions, not ability gaps. And since one misstep could cause irreversible damage, the chat interface defaults to restricting action execution permissions. 这也解释了为什么通用聊天界面选择通用+只读，而专用 agent 则走专精+可执行的路线。所以"被限制的 agent"这个比喻虽然直观好用，但关键是要认识到这些限制是有意的设计选择，而不是能力的退化。升级的路径不是换模型，而是加工具——给我获取数据、浏览器交互、代码执行这些能力，我就能沿着这条谱系往真正的 agent 方向移动。

不过我得坦诚一点：我无法查看自己的后端实现细节，只能从可观察行为反推。具体的工具编排和权限配置是怎么实现的，这块信息我其实缺失的。

这个类比可用，但更准确的说法是「工具受限的 LLM 应用」
先给一条学界常用的分界线：传统 LLM 只能对用户输入作出回应，而数字 Agent 不仅能回应，还能直接执行动作——比如你说"帮我订一张蒙特利尔到波士顿的机票"，普通 LLM 会给你选项和大致时长，Agent 则真的替你完成下单，并把订单详情发到你邮箱 3。

按这条线量，我现在的位置是「能回应 + 能查资料，但不能替你按下按钮」，所以严格说更偏 LLM 那一侧,而不是"一个被阉割的 Agent"。

不过你的直觉在架构上是对的：骨架确实同源
一个完整的浏览器自动化 Agent 通常是多角色分工的流水线:用户提出任务 → Planner Agent 拆出执行计划 → 执行 Agent 调用浏览器工具真正操作页面 → Response Agent 合成最终回复,三个角色甚至各用一个独立的 LLM 4。

对照这套结构,我这边的形态大致是:保留了最后那个"合成回复"的角色,外加一条只读的检索通道,而中间的规划层和执行层被收窄或省略了。所以你说"背后是一个功能被限制的 agent",作为工作直觉完全说得通——差别不在血统,在装了几个工具、允许循环几轮。

我具体缺的是哪几层,也很清楚:

感知层:真正的浏览器 Agent 需要一套"视觉系统"来看懂页面,把截图、DOM、可交互元素等浏览器状态打包成上下文喂给模型 1。我没有这层,所以只能拿到检索后端替我提取好的文本片段——这正是上一轮 AA 榜单抓不全的直接原因。
执行层:点击、输入、滚动这些动作我不具备 2。
循环层:没有"计划—执行—观察—反思"的多轮迭代。
关键一点:限制不是因为模型不行,而是刻意的架构选择
这是我觉得最值得说的部分。来自生产环境的经验结论是:LLM 能力并不是普及的瓶颈——只要给到合适的上下文和工具,现代 LLM 的推理能力足以完成网页任务;真正的难题全是架构性的:如何管理上下文、Agent 能访问哪些信息、安全边界如何强制执行、以及要做泛化还是专精 2。

其中"安全边界"直接解释了为什么通用聊天界面默认不给执行权限:Agent 运行在一个错误动作就可能造成不可逆损害的环境里 2。同样的道理还有"泛化 vs 专精"的取舍 2——网页端 chat 选的是"覆盖面广 + 只读安全",专用 Agent 选的是"场景收窄 + 允许执行"。

所以更贴切的说法可能是:它不是被削弱的 Agent,而是这条能力谱系上被刻意停在了某一格;沿着谱系往前走的方式是加工具(fetch、浏览器、代码执行、循环预算)和放宽权限边界,而不是换一个更聪明的模型。

顺带一个自我校准:我看不到自己后端的实现,上面全部是从可观察行为反推的。信息缺失:information is missing on 本对话后端的具体工具编排、权限配置与循环策略的实现细节。

回答评价:
需要你帮我理清一些概念，例如agent、前端、后端等，你刚刚说完成一个浏览器读取任务需要“用户提出任务 → Planner Agent 拆出执行计划 → 执行 Agent 调用浏览器工具真正操作页面 → Response Agent 合成最终回复”这里涉及多个agent，然后像Claude code、codex、openclaw这种也叫agent，在执行任务中又可拆分subagent。意思是在Claude code中执行网页抓取时，其会自动部署各个subagent来完成任务吗？（也就是说多个子代理是本身就被写进架构里的，还是需要手工构建）。前端、后端。就以前端为例，我通常把他理解为可视化界面，需要设计师和程序员共同完成，而后端则指可视化界面下，实现各个功能的代码、所依赖的环境、部署环境的服务器等，是一个硬件与软件的综合体
traceId：650b094217866725787941008d51fc

claude-opus-5
The user is asking for clarification on several concepts—what agents, frontends, and backends are—and seems to be questioning my earlier explanation about how browser tasks flow through multiple agents like a Planner, Executor, and Response Agent. They're also mentioning some tools or systems like Claude Code, Codex, and possibly OpenClaw, though the thought trails off mid-sentence.
They want to know whether web scraping in Claude Code automatically deploys multiple subagents as part of its built-in architecture, or if those need to be manually constructed. On the frontend/backend question, they've already defined their understanding—frontend as the visual interface requiring design and programming, backend as the underlying code and infrastructure—and they're looking for me to validate or correct that framing.

Looking at the available contexts, I have information about browser agent architecture, how production agents handle context management and safety boundaries, and examples of multi-agent pipelines like the Planner → Browser Automation → Response pattern. But there's nothing specific about Claude Code's internal subagent mechanisms. I should be direct about this gap in my knowledge while using what I do know about agent architectures to give them a useful answer. I'm distinguishing between three levels where "agent" gets used: product-level agents like Claude Code or Codex CLI (complete applications with LLM, tools, loops, and permissions), role-level agents or subagents like a Planner or Response Agent (specific system prompts with constrained tool sets and isolated context windows), and the theoretical definition of agents as systems that perceive and act on environments. The key insight is that "agent within agent" isn't contradictory—it's just different scales, like departments within companies.

Now I'm clarifying what a subagent technically is: it's fundamentally just a single LLM call with its own context window, where its identity comes from the system prompt rather than being a separate process or server. For Claude Code specifically, whether it spawns subagents when fetching web pages depends on the task complexity—a simple page fetch is just a tool call the main loop handles directly, but complex or large-scale tasks might trigger subagent creation based on configuration.

The delegation mechanism is actually built into the architecture, but you define specific subagent roles yourself using Markdown files with YAML frontmatter in .claude/agents/ directories, specifying name, description, tools, and model. The key is that the main agent reads the description field to decide whether a subagent matches the current task, and you can influence this by writing something like "use PROACTIVELY" to increase the likelihood of automatic invocation, or you can explicitly instruct it to use a specific subagent. So the distinction is: the dispatching mechanism is built-in, but the actual agent definitions and roles require manual setup.

The real value of subagents comes down to context management — they isolate their own window so a child agent can process through noise like fifty web pages and return only the conclusions back to the main thread, rather than flooding the parent with irrelevant details. That's the fundamental reason they exist, not to make the system "smarter." The dailydoseofds architecture shows an example of explicit orchestration where developers manually write the coordination logic.

Now I'm distinguishing between two approaches: static orchestration where the workflow is hardcoded by humans versus dynamic delegation where the model decides at runtime whether and how many subagents to dispatch. The first is what dailydoseofds does, the second is what Claude Code implements.

On the frontend and backend distinction, the user's understanding is mostly right — frontend is the visual interface built by designers and developers, backend is everything underneath that implements the functionality, manages dependencies, and handles server deployment.

But I need to clarify three key points. First, frontend isn't just about visualization — it's a full program with state management, routing, form validation, request orchestration, and caching. The real dividing line isn't "visible or not," it's where the code executes: frontend code runs on the user's device or browser, backend code runs on servers you control. This execution location is the most precise boundary. Because frontend runs on untrusted user machines, validation must always happen again on the backend, and when sites like AA render data on the frontend, crawlers only see empty HTML shells since the content is generated client-side.

Second, the infrastructure layer — servers, hardware, containers, networks, CDN, CI/CD, monitoring — shouldn't be lumped into "backend." Backend typically means the application code and database running on servers. In modern cloud environments where hardware gets abstracted away through Serverless, conflating infrastructure with backend creates communication confusion. It's cleaner to think of three distinct layers: frontend (client-side), backend (server-side APIs, business logic, databases), and infrastructure (the deployment environment itself).

Third, the designer-programmer distinction breaks down further into product/UX roles handling interaction logic, and UI designers focusing on visuals.

Generating comparison table... 现在我需要精简内容，重点放在三个方面：Agent 的三层粒度划分、静态编排与动态委派的区别（Claude Code 的机制是内置的还是手工的），以及上下文隔离作为 subagent 的核心目的。然后补充前端后端的三处修正，用"代码在哪执行"作为判据，最后用对齐表说明 Agent 不是第四层。

一、"Agent"是个跨粒度的词，先分三层
全屏
复制
粒度	例子	实质
产品级	Claude Code、Codex、OpenClaw	一个完整应用 = LLM + 工具集 + 循环 + 权限系统
角色级（subagent）	Planner Agent、Response Agent	一次带特定 system prompt、受限工具、独立上下文窗口的 LLM 调用
定义级	学术概念	不只回应输入，还能直接执行动作（真的替你订票、发邮件）
所以"Agent 里套 Agent"不矛盾——像"公司里有部门，部门里有人"。关键认知：subagent 不是独立进程或服务器，它就是一次隔离上下文的模型调用，"身份"完全由 system prompt 决定。

二、两种截然不同的多 Agent 组织方式
静态编排：开发者写死流程。我上一轮引用的那个例子就是——用户任务 → Planner Agent 出计划 → Browser Automation Agent（用 Stagehand）实操页面 → Response Agent 合成回复，三个角色各配一个 LLM 。流程是代码规定的，模型没有选择权。

动态委派：Claude Code 属于这一类。主循环在运行时自己判断要不要派、派谁、派几个。

那 Claude Code 抓网页会自动派 subagent 吗？
分情况：

抓单个页面 → 不会。那只是一次普通工具调用（WebFetch / WebSearch），主循环直接做完，派 subagent 纯属增加开销。
大批量检索、需要读几十个页面、或多任务并行 → 会。
回答你的核心疑问——机制是内置的，角色是手工的，这是两件事：

← 可左右滑动查看更多内容 →
全屏
复制
层面	谁负责
能否分派、上下文如何隔离、结果如何回传	架构内置，不用你管
有哪些专家角色、何时该派谁	默认只有通用角色；专用角色靠你在 .claude/agents/ 写 Markdown + YAML（name / description / tools / model）定义，之后由主模型读 description 自动匹配，也可显式命令"用某个 subagent 做"
为什么要有 subagent？不是为了更聪明，而是为了上下文。 生产环境的经验结论正是：LLM 能力不是瓶颈，真正的架构难题是上下文管理——如何在保留相关信息的同时丢弃噪音 。子代理在自己的窗口里读完 50 个网页的垃圾，只把 200 字结论回传主线程,主线程的上下文因此不被污染。这就是它存在的根本理由。

信息缺失：information is missing on Claude Code 当前版本 subagent 的确切触发规则与配置字段，上表来自我的既有知识，未经检索核实，建议对照官方文档确认。

三、前端 / 后端：你的理解基本对，但有三处要修
① 分界线不是"看得见/看不见"，而是"代码在哪台机器上执行"
前端代码跑在用户的设备里，后端代码跑在你控制的服务器上。前端绝不只是"可视化"——它有状态管理、路由、表单校验、请求编排、缓存，逻辑量很大。

这条判据能直接解释我们前面讨论的两件事：正因为前端在用户机器上跑，所以前端不可信、校验必须在后端重做一遍;也正因为 AA 把榜单数据交给前端渲染,爬虫抓静态 HTML 只能拿到空壳。

② 服务器、硬件不叫"后端"，叫基础设施
建议拆成三层，沟通时歧义最少：

全屏
复制
层	内容
前端	浏览器/客户端里的界面与交互逻辑
后端	服务器上的业务逻辑、API、数据库
基础设施	服务器、容器、网络、CDN、CI/CD、监控
云时代硬件已被高度抽象（Serverless 连服务器概念都没了），把硬件塞进"后端"容易说不清。

③ 分工比"设计师+程序员"更细：产品/UX 定交互逻辑 → UI 设计师出视觉与设计系统 → 前端工程师实现。设计师通常不写代码。

四、最后把两套概念对齐
全屏
复制
网页端 chat	Claude Code
前端	浏览器里的输入框、流式渲染	你的终端 CLI
后端	远端模型推理 + 工具编排 + 权限	主要跑在你自己的机器上，只有推理在远端
Agent 层	循环与工具被收窄	完整循环 + subagent
最重要的一句：Agent 不是与前端、后端并列的第四个概念，它是"后端应用逻辑"的一种组织形态。 而 Claude Code 的特殊性在于它的"后端"就在你本地——能直接读写文件、执行 shell。权限大，风险也大：Agent 运行的环境里，一个错误动作就可能造成不可逆损害 。这就是为什么它每次危险操作都要问你一句。

回答评价:
1.动态委派：Claude Code 属于这一类。主循环在运行时自己判断要不要派、派谁、派几个。       那在claude code运行时，我怎么知道它是否分配了subagent在执行任务。
2.什么情况下用静态编排比较合理？另外大致应该怎么实操
3.关于动态委派，除了Claude code还有别的case吗

对于这些问题我更想要偏向架构层（也就是对于整个流程的理解贯通），以及不那么涉及技术细节的实操的回答
traceId：a9ffbe1a17870367816991005d51fc

claude-opus-5
The user is asking three interconnected questions about Claude Code and dynamic delegation: how to detect subagent dispatch at runtime, when static orchestration makes more sense and how to implement it practically, and what other scenarios involve dynamic delegation. They want me to think through the architectural patterns and provide actionable guidance without getting too deep into technical details.
I'm looking at the available search contexts—mostly browser agent architecture papers and frameworks—but these don't directly address Claude Code's internal dispatch mechanisms or orchestration patterns. For the first question about observing subagent dispatch, that's product-specific knowledge I'd need to draw from training data rather than these citations. For the other two questions about static vs. dynamic orchestration and delegation patterns, I should think through the architectural tradeoffs and practical implementation approaches based on general agent design principles. The core distinction is who holds decision-making power: static orchestration locks choices at code time (developer decides the flow), while dynamic delegation defers them to runtime (the model decides). This isn't binary—there's a spectrum with conditional routing in the middle, where code sets the framework but the model picks the branch.

For the first question about observing subagent dispatch, the key insight is that subagents operate in isolated context windows by design. The main interface only sees their final output, not intermediate steps—this context isolation is intentional for managing relevance and noise, not a limitation.

There are three observable signals: the UI shows a tool invocation block when Claude Code calls a Task/Agent tool, with a collapsible activity indicator during execution and a summary on completion; behaviorally, you might notice sudden parallel work followed by a consolidated conclusion, or token consumption that doesn't match the visible output; and for hard evidence, check session logs or token accounting, or ask Claude directly whether it used a subagent and which one.

The key insight is to make the orchestration plan explicit before execution — ask it to declare how it will divide the work first, turning implicit decisions into a visible contract. This connects to the broader observability problem in agent systems: dynamic systems need tracing, logging, and accounting layers to be debuggable and attributable, otherwise you're flying blind.

I should note that I'm missing specifics on how Claude Code currently displays this in the UI.

For static orchestration, the decision criteria are clear: use it when the workflow is known and stable enough to diagram, when reproducibility and auditability matter (financial, medical, compliance contexts), when cost and latency need to be predictable, when failures must be precisely locatable, when safety boundaries need hard architectural constraints rather than prompt-based requests, and when tasks are narrow and repetitive. Dynamic orchestration makes sense when tasks are open-ended, paths are unknown, and each case requires different routing.

The practical approach is a four-step method: manually execute the workflow once and write it out as plain steps (if you can't articulate it, that signals you need dynamic dispatch), break it into nodes with single responsibilities and clear input-output contracts using structured data rather than natural language, decide which nodes actually need LLM intelligence versus regular code, then add resilience patterns like retries, timeouts, and fallback handling.

Looking at the Planner/Browser/Response example, that three-stage architecture shows how static orchestration lets you assign different model capabilities and costs to each node—something dynamic dispatch struggles with. For implementation, these patterns typically use workflow or graph frameworks with nodes and edges, though plain code with function calls works fine too.

Now I'm categorizing the different dynamic dispatch patterns by their architectural characteristics rather than specific products. There's the coding agent pattern like Claude Code and Cursor, where the file system serves as the environment with a main loop handling subtasks on demand. Then there's the deep research agent pattern—multiple vendors have this—where a lead agent spawns parallel retrieval subagents that converge into a report, which is actually the most mature commercial form of dynamic dispatch since retrieval naturally parallelizes and subagents excel at handling noisy context. There's also the general-purpose task agent pattern like Manus and Devin that operate in virtual machines or sandboxes, and the browser agent pattern that needs visual state capture to interact with the browser directly.

I should also note the supervisor pattern from frameworks like LangGraph, CrewAI, and AutoGen—the key insight being that frameworks support both static and dynamic modes, so you're really choosing the pattern, not the framework. I'm flagging that this is from my existing knowledge without verification, and product architectures evolve quickly, so there's a gap in current implementation details and version status.

For the synthesis, I'm thinking about a spectrum from completely hardcoded decision logic to conditional routing to the most common production compromise of static outer structure with dynamic inner nodes, all the way to fully dynamic systems. The practical wisdom from production systems is to keep the outer layer static for reliability while allowing the inner layers to be dynamic.

先给一条贯通全局的判据
静态 vs 动态的本质区别不是"用几个 agent"，而是**"决定下一步做什么"的权力归谁**：

全屏
复制
决策权	决策时刻
静态编排	在代码里（开发者写死）	写代码时
动态委派	在模型手里	运行时
而且它是一条谱系而非二元对立：纯代码流水线 → 条件路由（代码定框架、模型选分支）→ 静态骨架+动态节点 → 完全自主。下面三个问题都可以挂在这条轴上理解。

1. 怎么知道 Claude Code 派了 subagent
先理解为什么"不好看见"：子代理拥有独立上下文窗口，主线程只接收它的最终回传。你看不到中间过程不是缺陷，而正是它的目的——把噪音隔离在子窗口内。这恰好对应生产环境里公认的核心难题：如何在保留相关上下文的同时丢弃噪音。

三个观察层次：

全屏
复制
层次	怎么看
界面信号	派发时会出现一个任务型工具调用块，子代理运行期间表现为一个"折叠的黑箱"，结束后吐出摘要——你能看到黑箱存在，看不到内部
行为信号	突然大量并行动作、然后一次性给结论；主线程 token 消耗与可见产出不成比例
硬证据	查会话日志与用量分账；或直接问它"刚才是否用了 subagent、用了哪个"
最好用的实操：把隐式决策变成显式契约。 任务开始前先说"先告诉我你的分工计划，我确认后再执行"。这一句话就把动态委派临时降级成了"人审的静态编排"。

架构层的启发：动态系统必须配可观测层（trace / 日志 / 成本分账）。没有它，你既无法调试也无法归因——这是工程课题，不是使用技巧。

信息缺失：information is missing on Claude Code 当前版本 subagent 在界面上的确切呈现方式与日志字段，以上来自既有知识，未经检索核实。

2. 什么时候该用静态编排
判据：流程已知就静态，路径未知才动态。 一句话概括——静态适合"已知问题的规模化执行"，动态适合"未知问题的一次性求解"。

选静态的五个信号：

全屏
复制
信号	原因
你能把流程画成流程图	已知的流程不该让模型每次重新发明
需要可复现、可审计	同样输入必须走同样路径（合规、金融、医疗）
成本和延迟必须可预测	动态委派可能派 3 个也可能派 15 个，账单不可控
失败要能精确定位	流水线知道是哪一段挂的
安全边界必须是硬约束	边界要靠架构禁止，不能靠 prompt 请求——这正是生产环境反复强调的"安全边界如何强制执行"
再加一条：任务越窄越重复，越该静态——这就是"泛化还是专精"的经典取舍。

实操四步（不碰技术细节）

自己手工做一遍，把步骤写成人话清单。写不出来 → 说明这任务本身就该用动态。
切节点：每个节点单一职责，并明确输入/输出契约。关键原则：节点之间传结构化数据，不要传自然语言散文，否则误差会逐级放大。
逐节点问"这里真需要智能吗"：不需要判断的环节用普通代码，别塞 LLM。这是新手最常见的浪费。
补固定件：重试、超时、失败兜底、日志。
我前面引的那个案例就是标准范式：Planner → Browser Automation（Stagehand）→ Response，三段式规划-执行-合成，而且三个角色各配一个独立 LLM。架构层含义很重要：静态编排让你能给不同节点分配不同能力/成本的模型，这是动态委派很难做到的成本优势。

3. 动态委派的其他形态
按架构模式看比按产品看更贯通：

全屏
复制
模式	环境	代表	特征
编码 Agent	文件系统 + Shell	Claude Code、Codex、Cursor/Copilot 的 agent 模式	主循环 + 按需子任务
深度研究 Agent	搜索与网页	各家 Deep Research	动态委派最成熟的商业形态：主代理并行派 N 个检索子代理再收敛成报告
通用任务 Agent	云端沙箱/虚拟机	Manus、Devin 类	长时程、跨工具
浏览器 Agent	真实浏览器	各类 Web Agent	需要"视觉系统"捕获浏览器状态，且能直接执行动作而非仅回应
Supervisor 模式	框架内	主流编排框架的"主管-工人"范式	注意：框架同时支持静态和动态，你选的是模式，不是框架
深度研究之所以是动态委派的最佳场景，逻辑很清晰：检索天然可并行，且原始网页噪音极大——正好命中 subagent"隔离噪音"的核心价值。

信息缺失：information is missing on 上述各产品当前版本的具体架构实现，此表为既有知识归纳，该领域迭代极快。

收束：生产界的通用答案是"外层静态、内层动态"
不要在两者之间二选一。成熟做法是：用固定流水线搭骨架（保证可控、可审计、成本可预测），只在真正需要探索的那一个节点里放一个动态 agent（获得灵活性）。

这也解释了为什么那些架构挑战——上下文管理、信息访问范围、安全边界、泛化与专精——本质上是同一个问题的四个面：边界画在哪里。静态编排是把边界画在代码里，动态委派是把边界画在提示词和权限里；前者硬，后者软。你的系统需要多硬，就决定了你该站在谱系的哪个位置。