# TikTok 美区达人建联：整体方案与工具调研

> 版本：2026-11 ｜ 用途：内部决策参考
> 说明：工具价格大多来自第三方评测或厂商公开页面，各来源之间经常对不上，表中写的是“参考价”。**采购前请以厂商官网或销售报价为准**。工具的数据库规模、回复率这类数字多为厂商自述，没有经过独立验证。

---

## 0. 结论先行

| 问题 | 结论 |
|---|---|
| 有没有全流程一体化工具？ | **有，但没有一个能把每一环都做到最好。** 美区 TikTok Shop 场景最贴合的是 **Reacher / Euka / Cruva / Hubfluence** 这类“TikTok Shop 联盟建联平台”，覆盖选达人、站内邀约和私信、邮件、管理达人、看 GMV。如果想要更强的“邮件建联 + 合作管理”，可以考虑 Influencer Hero、GRIN 这类网红营销平台 |
| 我们的推荐打法 | **两条渠道同时做，先买工具再补开发**：① 用一个 TikTok Shop 联盟建联工具，负责站内定向邀约和私信；② 用一套冷邮件工具（Smartlead 或 Instantly）负责邮件建联。我们自己开发两个轻量模块：**“选达人和数据处理流程”**、**“回复实时提醒中心”** |
| 每月联系上万达人，邮箱怎么配？ | **不要用公司主域名直接群发。** 每月 1 万个达人，加上追发，大约是每月 2.5～3 万封邮件。按每个邮箱每天发 30～40 封算，需要 **30～40 个发信邮箱，分布在 10～15 个专门用来发信的副域名上**（详见第 3 节） |
| 回复怎么第一时间知道？ | 邮件工具的 **Webhook（有回复时自动通知我们的接口）→ 自建中转服务 → AI 判断回复意图并写好回复草稿 → 推送到飞书或企业微信群并 @ 专人**。专人在手机上就能处理。目标：**达人回复后 15～60 分钟内回应** |
| 每月大致预算 | 买工具和发信基础设施大约 **$800～2,000/月**，另外需要 1 名开发投入约 1～2 个月（详见第 7 节） |

---

## 1. 背景：这门生意怎么赚钱，为什么要建联

- **TikTok 是“刷到才买”的电商**：商品要靠达人的视频才能获得流量。品牌自己的账号粉丝少、信任度低，所以要借达人的流量和信任来卖货。
- **TikTok Shop 联盟模式**：达人带货，按成交额拿佣金。
  - **公开计划**：所有达人都能看到商品并申请带货。
  - **定向计划**：品牌单独邀请特定达人，可以给更高的佣金。
  - 寄样有三种方式：免费寄样、先付费后返还、不寄样。
  - 佣金 = (成交额 − 退款) × 佣金比例。行业参考：公开计划 10～15%，定向计划 18～25%。
  - 头部达人通常还要**固定费用（坑位费）**，这部分一般通过邮件谈。
- **为什么要大规模建联**：这是一个漏斗。冷邮件回复率一般在 1～5%，写得好的个性化邮件能更高；回复的人里又只有一部分会要样品、真正发视频，最后出爆款的更少。所以**前端要靠数量，后端要靠回复速度和跟进质量**。
- **合规提醒**：达人推广要标注“付费合作”，这是美国 FTC（联邦贸易委员会）的要求，TikTok 上要打开“商业内容”开关。合作条款里要写明这一点。

---

## 2. 全流程拆解：每一环用什么工具和 API

```
① 根据商品锁定达人 → ② 拿到并验证联系方式 → ③ 个性化内容 → ④ 发信（邮件 + 站内）
→ ⑤ 回复实时提醒 → ⑥ 谈判、寄样、跟踪视频和 GMV → ⑦ 复盘（留住能出单的达人）
```

### ① 根据商品锁定达人

**思路**：最好的达人，是**已经在带同类或竞品商品、而且近期有成交的达人**。其次才是“粉丝画像和我们的商品匹配的达人”。

| 工具 / API | 网址 | 作用 | 参考价 | 备注 |
|---|---|---|---|---|
| **TikTok Shop 联盟中心（官方）** | https://seller-us.tiktok.com （卖家后台 → 联盟） | 官方达人广场：搜达人、发定向邀约、管寄样 | 免费，只付佣金 | 每次邀约最多 50 个达人 |
| **TikTok Shop Affiliate Seller API（官方）** | https://partner.tiktokshop.com/docv2/page/affiliate-seller-api-overview | 可以通过程序**在达人广场搜索达人**、**创建定向计划和公开计划**、查联盟订单 | 免费，需要申请开发者资质 | **自研时最该接入的官方接口**，合规而且是一手数据 |
| **Kalodata** | https://www.kalodata.com | TikTok Shop 商品、达人、视频的销量数据，找“竞品是谁在带” | 价格要登录后才能看；企业版有 API，可导出达人联系方式 | 美区数据口碑好 |
| **FastMoss** | https://www.fastmoss.com | 同上，覆盖 19 个以上国家，有直播数据 | 约 $49/月起；API 属于企业版服务 | |
| **EchoTik** | https://www.echotik.live | 同上，有永久免费版 | $13.9～57/月；达人邮箱导出只在企业版 | 性价比高 |
| **嘀嗒狗 Tabcut** | https://www.tabcut.com | 国内团队常用的 TikTok 数据工具 | 需要咨询 | |
| **AIsa · 达人 AI 搜索 / 相似达人** | https://aisa.one （工具名 `waveinflu_ai_search`、`waveinflu_similar_creators`） | 用一句话描述，或者给一个对标达人，找出一批达人；**结果里大多直接带邮箱**；可按地区、语言、粉丝量、播放量筛选 | 约 $0.006 / $0.005 每个达人 | 按次付费，适合自研流程调用 |
| **AIsa · TikTok 数据（TikHub）** | 同上（`tikhub tiktok …` 系列） | 关键词搜视频和用户、按品类拉商品、商品详情里的相关视频（可以反查带货达人）、达人资料 | 约 $0.001/次 | **查达人带货 GMV 的接口要达人自己的登录凭证，查不了别人** |
| **Modash** | https://www.modash.io | 达人搜索、受众分析、批量查邮箱 | 约 $120～299/月起，有 14 天试用 | 偏“找人 + 查邮箱” |
| **HypeAuditor** | https://hypeauditor.com | 达人搜索、识别假粉 | 需要询价 | 适合判断粉丝真实性 |
| **NoxInfluencer（聚星）** | https://www.noxinfluencer.com | 达人搜索和分析，号称收录 6300 万达人 | 约 $239 起 | 国内团队常用 |
| **influencers.club** | https://influencers.club | 达人数据和邮箱补全接口，按积分计费 | 约 $208～299/月起 | 适合自研时补全邮箱 |
| **Apify 爬虫（第三方）** | https://apify.com （例如 `tiktok-contact-scraper`、`tiktok-shop-creator-search`） | 抓达人主页、简介里的邮箱，或按 GMV 搜达人 | 约 $3.5 / 1000 条起 | 非官方数据，有违反平台服务条款的风险 |

### ② 拿到并验证联系方式

| 工具 | 网址 | 作用 | 参考价 |
|---|---|---|---|
| AIsa · 邮箱查询 | https://aisa.one （`waveinflu_email_lookup`） | 从主页链接查邮箱 | 约 $0.002/次 |
| Modash / influencers.club | 见上表 | 批量查邮箱 | 见上表 |
| Reacher 自带邮箱库 | https://reacherapp.com | 号称有 10 万以上可导出的达人邮箱 | 包含在订阅里 |
| **邮箱有效性验证（必做）** | MillionVerifier https://www.millionverifier.com 、ZeroBounce https://www.zerobounce.net 、NeverBounce https://neverbounce.com | **发信前把无效邮箱清掉**，避免退信率过高损害域名信誉 | MillionVerifier 约 $40 / 1 万个；ZeroBounce 约 $99 / 1 万个 |

> 现实情况：TikTok 上大部分达人**不公开邮箱**，能查到的比例通常在 30～60%。查不到的达人，可以通过站内定向邀约或私信联系，**这就是要“两条渠道同时做”的原因**。

### ③ 个性化内容

- 用大模型（Claude、GPT 等，AIsa 上也能调用）读达人的简介和最近几条视频，生成**针对这个达人的开头两句**，再接上标准合作条款：佣金比例、免费寄样、爆款脚本参考、出单奖励。
- 主流冷邮件工具都支持在邮件里插入变量（比如达人名字、个性化开头），由我们的流程把内容提前写好再导入。

### ④ 发信

**渠道 A：TikTok Shop 站内（定向邀约 + 私信）**，这是给“没有邮箱的达人”和“TikTok Shop 原生达人”用的。

| 工具 | 网址 | 主要能力 | 参考价 |
|---|---|---|---|
| **Reacher** | https://reacherapp.com | 对接联盟中心，自动批量做定向邀约、私信、邮件序列，管理达人、看数据；YC 投资；号称是 TikTok Shop 应用市场金牌应用 | 约 $199～599/月，有 7 天试用（价格来自竞品评测，待确认） |
| **Euka** | https://www.euka.ai | AI 选达人、站内私信 + 邮件自动建联、管理达人、看 GMV | Starter $199/月（每月 1 万条消息和邀约）；Growth $479～599/月 |
| **Cruva（原 UpTk）** | https://cruva.com | 号称 TikTok Shop 官方合作伙伴；建联、管理达人、自动化 | 官网 Starter $179/月；更高档到 $599/月，有 14 天试用 |
| **Hubfluence** | https://www.hubfluence.io | 站内私信 + 邮件序列，达人不回复会自动追发，并能识别回复 | $149/月起（每月 1 万条邀约和消息，每天 100 封邮件） |
| 国内 Chrome 插件：达秘、ProBoost、极目等 | 见 Chrome 应用商店（例如达秘 https://extscope.org/extension/nhpgibkafngkpkjlkhnfipkalhkkbebo ） | 在卖家后台里批量邀约和私信 | 便宜 |

> ⚠️ 站内自动化有被 TikTok 限流甚至封店的风险。**优先选走官方联盟 API、并且是 TikTok Shop 官方合作伙伴的工具**；国内插件要求的浏览器权限很高，账号安全风险要自己评估。

**渠道 B：邮件**

| 工具 | 网址 | 主要能力 | 参考价 |
|---|---|---|---|
| **Smartlead** | https://www.smartlead.ai | 不限邮箱数量、自动预热、多邮箱轮换发信、统一收件箱、**接口和 Webhook 完善**（有“收到回复”和“回复分类变化”两类通知），AI 自动给回复分类 | 约 $39～94/月起，按线索量升档 |
| **Instantly** | https://instantly.ai | 同上，更好上手；有收到回复、有意向、无意向等通知事件；有 AI 自动回复 | 约 $37～97/月起；Webhook 和统一收件箱需要较高档套餐（约 $97/月），待确认 |
| Lemlist | https://www.lemlist.com | 多渠道建联（邮件 + 其他渠道） | 约 $69～99/用户/月 |
| **Influencer Hero** | https://www.influencer-hero.com | 网红营销一体化：找达人、邮件序列、AI 起草、管理达人、寄样 | 约 $384～2,000/月（各来源差异大） |
| **GRIN** | https://grin.co | 一体化，统一收件箱，可对接 Slack | $399～1,799/月（按管理的达人数量分档） |
| Upfluence | https://www.upfluence.com | 一体化，按模块收费 | 约 $478/月起，通常按年签约 |
| Aspire | https://www.aspire.io | 一体化，偏中大型品牌 | 需要询价，通常按年签约 |
| **AIsa · AgentMail** | https://aisa.one （mail 模块）／官方 https://agentmail.to | 通过 API 建收件箱、发信、回复、看邮件往来，适合让 AI 自动处理 | 按次付费；**大规模发冷邮件前要先确认它是否允许** |

**发信基础设施（买邮箱和域名）**

| 方案 | 网址 | 参考价 |
|---|---|---|
| Google Workspace | https://workspace.google.com | 约 $7～14/邮箱/月 |
| Microsoft 365 | https://www.microsoft.com/microsoft-365/business | 约 $6～12/邮箱/月 |
| Mailforge（冷邮件专用邮箱） | https://www.mailforge.ai | 约 $2～3/邮箱/月，域名另算 |
| Maildoso | https://maildoso.com | 约 $1.9/邮箱/月起 |
| Infraforge（独立 IP） | https://www.infraforge.ai | 约 $3～4/邮箱/月，独立 IP 另加 $99/月 |

### ⑤ 回复实时提醒（我们重点自研的部分）

| 组件 | 网址 | 说明 |
|---|---|---|
| Smartlead Webhook | https://api.smartlead.ai/api-reference/webhooks/events | 有“收到回复”事件，附带统一收件箱的跳转链接；也可以只在达人被标成“有意向”时才通知 |
| Instantly Webhook | https://developer.instantly.ai/guides/webhook-events.md | 有“收到回复”和“有意向”等事件，接口地址为 `https://api.instantly.ai/api/v2/webhooks`；失败会重试，所以要防止重复提醒 |
| AgentMail 官方 Webhook / WebSocket | https://docs.agentmail.to/webhooks-overview.md | 有新邮件事件；**通过 AIsa 接入的版本没有开放创建 Webhook，只能定时去查** |
| n8n（可以自己部署的流程自动化工具） | https://n8n.io | 不写代码就能把各个 Webhook 和飞书、企业微信、表格串起来，适合先跑通 |
| 飞书自定义机器人 | https://open.feishu.cn （开放平台文档 → 自定义机器人） | 把消息卡片推到群里并 @ 人，支持签名校验 |
| 企业微信群机器人 / 钉钉机器人 | https://developer.work.weixin.qq.com ／ https://open.dingtalk.com | 同上 |

### ⑥⑦ 谈判、寄样、跟踪、复盘

- 寄样、视频发布、GMV：TikTok Shop 后台，或者 Reacher、Euka、Cruva 自带的达人管理和数据功能。
- 自研时：用 TikTok Shop Affiliate API 拉联盟订单和 GMV，结果写进飞书多维表格或我们自己的管理系统。

### 开源参考（只能参考，不能直接用于生产）

| 项目 | 网址 | 说明 |
|---|---|---|
| InfluenceX | https://github.com/oratis/influencex | MIT 协议，支持找达人、建联流程、ROI 追踪，可用 Docker 部署；项目很新，几乎没人用 |
| n8n 模板：TikTok 达人发现 + AI 分析 + 邮件建联 | https://n8n.io/workflows/4774-discover-and-analyze-tiktok-influencers-with-bright-data-claude-ai-and-email-outreach/ | 最接近我们的流程；但依赖 Bright Data 爬虫，有违反平台服务条款的风险 |
| outreach-os | https://github.com/hitb1099/outreach-os | 基于 n8n 的冷邮件 + AI 个性化，原本面向 B2B 获客，可以改造 |

---

## 3. 每月上万达人：邮箱和送达率怎么规划（重要）

> 关于“我们用企业邮箱，是正常建立的”：企业邮箱本身没问题，**风险在于“用公司主域名大批量发冷邮件”**。
> - Gmail、Yahoo、Outlook 会按**域名**累计信誉。一旦投诉率或退信率过高，**整个主域名都会被降级**，连公司日常和客户、供应商的往来邮件都可能进垃圾箱。
> - Google Workspace、Microsoft 365 的使用条款都限制发送“未经请求的批量邮件”，账号有被停用的风险。
> - 所以，**行业通行做法是：主域名只用来收发正常业务邮件；冷邮件用专门注册的副域名**（比如 `brandname-collab.com`、`trybrandname.com`），这些副域名也建在企业邮箱体系里，属于正常合规的做法。

**容量估算（美区，每月 1 万个达人）**

| 项目 | 数值 |
|---|---|
| 每个达人发几封 | 首封 + 2 封追发 ≈ 平均 2.5～3 封 |
| 每月发信量 | 约 25,000～30,000 封 |
| 每个工作日发信量（按 22 天） | 约 1,100～1,400 封 |
| 每个邮箱每天安全发信量 | 30～40 封（新邮箱从 10～20 封开始） |
| **需要的发信邮箱数** | **约 30～40 个** |
| **需要的副域名数** | **约 10～15 个**（每个域名 2～3 个邮箱） |
| 预热期 | 新邮箱要先预热 2～3 周，期间持续预热 |

**必须做到的送达要求**（Google、Yahoo 自 2024 年起，Microsoft 自 2025 年起执行）：
- 每个域名都要配好发件人认证（SPF、DKIM、DMARC），并且发件域名要对齐。
- 投诉率**低于 0.3%**，实际要控制在 0.1% 以下。
- 提供退订入口。批量营销邮件要求“一键退订”；冷邮件至少在正文里放退订方式，并且**48 小时内处理退订**。
- 遵守美国 CAN-SPAM 法规：标题不能误导，要写公司实际地址，要提供退订方式。
- 发信前一定要验证邮箱有效性，**退信率控制在 2% 以下**。
- 同一个达人不要被不同同事重复联系：在达人管理系统里统一去重，并维护一个“不再联系”名单。

---

## 4. 回复实时提醒：专人值守怎么配合

既然有专人负责回复时差，系统要做的是**让这个人不漏掉、不延迟、不用一直盯邮箱**：

```
达人回复
  │ Smartlead / Instantly 的 Webhook（有回复就立刻通知）
  ▼
回复中转服务（自研；先用 n8n 跑通，之后改成自己写的服务）
  ├─ 去掉重复通知、过滤自动回复（比如“我在休假”）
  ├─ AI 判断回复意图：有兴趣 / 问价格 / 要样品 / 要坑位费 / 拒绝 / 退订
  ├─ AI 起草回复：带上达人信息、我们的佣金政策、寄样表单链接
  └─ 写入达人管理系统，更新跟进状态
  ▼
飞书 / 企业微信群卡片 @ 值班人员
  内容：达人主页、粉丝量、回复原文、意图分类、AI 草稿、[一键在统一收件箱打开] 按钮
  ▼
值班人员在手机上确认或修改后发出
  └─ 超过 30 分钟没处理 → 再提醒一次，并 @ 备岗人员
```

- **提醒分级**：“有兴趣”“要样品”立即强提醒；“问价格”普通提醒；“拒绝”“退订”只记录、不提醒，同时自动加进“不再联系”名单。
- **对外承诺的时效**：值班时段内 **15～60 分钟回应**。可以考虑让 AI 自动发一封“已收到，30 分钟内安排”的确认信，但正式回复和报价一定要由人确认后再发。
- **工作量估算**：每月 1 万个达人，按 2～5% 回复率，每月约 200～500 封回复，**每天大约 10～25 封**，1～2 个人就能处理。

---

## 5. 买工具还是自己开发：先买，同时自研

| 模块 | 买 | 自研 | 建议 |
|---|---|---|---|
| 站内邀约和私信 | Reacher / Euka / Cruva / Hubfluence | 用官方 Affiliate API 自己做 | **买**：站内自动化门槛高，规则也常变，交给官方合作伙伴更稳 |
| 找达人和数据 | Kalodata / FastMoss / EchoTik + Modash | 接 AIsa 和 TikTok Shop Affiliate API，自己做打分 | **两者结合**：先买一个数据工具给运营用；开发同时写自动化选达人流程 |
| 邮件发信 | Smartlead（推荐，接口完善）/ Instantly | 不建议自研发信引擎 | **买**：预热、多邮箱轮换、送达率优化这些都是现成能力 |
| 发信邮箱和域名 | Google Workspace / Microsoft 365 / Mailforge | — | **买** |
| 回复提醒 + AI 分类 + 草稿 | 工具自带的较弱 | 用 Webhook 接到我们的服务，再推到飞书或企业微信 | **自研**：成本低、提升大，而且贴合我们的流程 |
| 达人管理和效果复盘 | 联盟工具自带 | 飞书多维表格起步 → 自建系统 | 先用工具自带的，后期再自建并打通 |

---

## 6. 落地计划

| 阶段 | 时间 | 要做的事 | 怎么算成功 |
|---|---|---|---|
| **P0 准备** | 第 1～2 周 | 注册 10～15 个副域名，开 30～40 个邮箱，配好发件人认证并开始预热；申请 TikTok Shop Affiliate API 开发者资质；同时试用 2～3 个联盟建联工具（Reacher、Euka、Cruva、Hubfluence 选 2～3 个）和 Smartlead 或 Instantly | 预热正常，工具账号开通 |
| **P1 试点** | 第 3～6 周 | 每周联系 500～1,000 个达人，站内和邮件同时做；用 n8n 把“回复 → 飞书提醒”跑通；准备 3～5 套邮件模板做对比测试 | 回复率达到 2% 以上，寄样转化率、响应时长都能被统计 |
| **P2 放量** | 第 2～3 个月 | 提到每月 1 万个达人；开发做好自动化选达人流程（AIsa + 官方 API + 大模型打分 → 自动导入 Smartlead）和回复中转服务（AI 分类 + 草稿） | 投诉率低于 0.1%，退信率低于 2%，回复后 60 分钟内回应的比例超过 90% |
| **P3 系统化** | 第 3 个月以后 | 建达人管理系统，把寄样、视频、GMV 都串起来，形成复盘闭环；根据数据判断哪些买来的工具可以换成自研 | 每个合作达人的 GMV 和 ROI 可以算清楚 |

---

## 7. 月度预算估算（每月 1 万个达人，美区）

| 项目 | 参考月费 |
|---|---|
| TikTok Shop 联盟建联工具（选 1 个） | $179～599 |
| 冷邮件工具（Smartlead 或 Instantly，按线索量选档） | $94～300 |
| 发信邮箱 30～40 个 | $100～400（Mailforge 便宜，Google Workspace 贵一些） |
| 副域名 10～15 个 | 约 $15～25（按年付，折合每月） |
| 邮箱验证（约 1 万个） | $40～100 |
| 数据工具（Kalodata / FastMoss / EchoTik 选 1 个） | $15～200 |
| AIsa 按次调用（AI 搜达人 + 补邮箱，约 1 万个达人） | 约 $60～150 |
| 大模型（个性化开头 + 回复分类和草稿） | 约 $20～100 |
| **合计** | **约 $800～2,000/月** |
| 人力 | 1 名开发 1～2 个月（之后少量维护），1～2 名运营（包括时差值守） |

> 还不包括达人佣金、样品和物流成本、头部达人的坑位费。

---

## 8. 风险清单

1. **主域名信誉受损**：冷邮件一律用副域名发，主域名只收发正常业务邮件。
2. **TikTok 站内自动化被限流或封店**：优先选官方合作伙伴、走官方 API 的工具，控制每天的邀约和私信量。
3. **数据合规**：第三方爬虫数据有违反平台服务条款的风险；尽量使用官方 API 和正规数据服务商；联系欧洲达人前要先评估 GDPR。
4. **工具宣传数据不可靠**：数据库规模、回复率都要在试用期里自己验证。
5. **达人推广披露**：合同里要写明遵守 FTC 规定、打开 TikTok 商业内容开关。

---

## 附：主要参考来源

- TikTok Shop Affiliate Seller API：https://partner.tiktokshop.com/docv2/page/affiliate-seller-api-overview
- TikTok 官方开发者博客（联盟 API 发布）：https://developers.tiktok.com/blog/2024-tiktok-shop-affiliate-apis-launch-developer-opportunity
- 定向计划说明：https://ads.tiktok.com/help/article/about-target-collaborations-in-seller-center?lang=en
- Euka 价格：https://www.euka.ai/pricing
- Cruva 价格：https://cruva.com/pricing
- Hubfluence 价格：https://intercom.help/hubfluence/en/articles/15699688-how-much-does-hubfluence-cost-and-what-is-included-in-each-plan
- Reacher（YC）：https://ycombinator.com/companies/reacher
- Reacher 评测（竞品视角）：https://www.hubfluence.io/blog/reacher-review-alternative
- Kalodata、FastMoss、EchoTik 价格：https://delzonic.com/blogs/fastmoss-pricing-2026/ 、https://delzonic.com/blogs/echotik-pricing-2026/ 、https://creatify.ai/blog/kalodata-pricing-plans-and-what-you-ll-actually-pay-in-2026
- Smartlead 与 Instantly 对比：https://bouncezero.io/smartlead-vs-instantly-2026 、https://emelia.io/hub/smartlead-vs-instantly
- Smartlead Webhook 事件：https://api.smartlead.ai/api-reference/webhooks/events
- Instantly Webhook 事件：https://developer.instantly.ai/guides/webhook-events.md
- AgentMail 处理收件：https://docs.agentmail.to/knowledge-base/handling-inbound-emails.md
- Gmail、Yahoo、Microsoft 批量发件人规则：https://smtpedia.com/email-google-and-yahoo/ 、https://redsift.com/guides/bulk-email-sender-requirements
- 冷邮件基础设施：https://prospeo.io/s/cold-email-infrastructure 、https://woodpecker.co/blog/mailforge-pricing/
- 邮箱验证工具对比：https://nerdbot.com/2026/08/25/7-best-bulk-email-verification-tools-in-2026-price-at-10000-volume-compared/
- GRIN、Influencer Hero 等网红营销平台价格：https://ecommercefastlane.com/8-best-influencer-crms-for-shopify-brands-in-2026/ 、https://www.g2.com/compare/grin-vs-upfluence-vs-aspireiq-aspire
- influencers.club：https://influencers.club/llms.txt
- 开源与 n8n：https://github.com/topics/affiliate-outreachbot 、https://n8n.io/workflows/4774-discover-and-analyze-tiktok-influencers-with-bright-data-claude-ai-and-email-outreach/
- AIsa 的能力（通过 MCP 免费查询，未发生付费调用）：https://aisa.one/docs/agent-quickstart.md
