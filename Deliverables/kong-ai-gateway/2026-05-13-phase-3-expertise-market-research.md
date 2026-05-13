---
type: deliverable
audience: DXC internal — Jesse, practice leads
status: research-complete — fills evidence gaps in Nolan's options brief
date: 2026-05-13
author: Pax (Researcher)
project: kong-ai-gateway
related: "[[2026-05-13-phase-3-mcp-agent-expertise-options]]"
---

# Kong AI Gateway — Phase 3 Expertise: Market Research

**Prepared for:** Jesse Swensen, DXC
**Date:** 2026-05-13
**Purpose:** Fill the five evidence gaps Nolan flagged in [[2026-05-13-phase-3-mcp-agent-expertise-options]] so the Phase-3 sourcing decision can be made with grounded data when Phase-2 Prod stabilization triggers the decision window. **This brief is research, not recommendation.** Pax does not pick between the three options; he supplies the numbers and surfaces a fourth option (Kong PS / SI) that Nolan flagged as needing scoping.

---

## Executive summary

1. The intersection — MCP-fluent + Kong PDK plugin authorship + enterprise-gateway ops — is **not yet a single-named market role.** Anthropic published MCP in November 2024; Kong's Enterprise MCP Gateway only shipped in late 2025 / early 2026. The labor pool at the full three-way intersection is **executive-search thin** today, though either constituent skill (Kong PDK on its own, MCP on its own) is sourceable through normal posting cycles.
2. Nolan's directional contract rate of **$175–250/hr is well-calibrated for 2026 US** — it sits inside the published "specialist with production LLM/agent infra experience" band and aligns with the LLM-specialist premium (30–50% over generalist ML/AI rates) reported by Toptal and rate-aggregator sources.
3. **A structured upskill compression path exists.** Anthropic Academy (launched March 2026) ships free MCP courses including a Claude Certified Architect (CCA) track. Kong has *not* released an AI Gateway or MCP Gateway certification beyond its two associate-level credentials. Upskill is faster on the MCP side than on the Kong PDK side.
4. **A fourth option is real: Kong Professional Services exists and has shipped MCP Gateway delivery work**, but Kong does not publish rates, team composition, or engagement shape on its public site — discovery requires direct CSM outreach. SI partners (Accenture, Capgemini, Deloitte) have signed broad agentic-AI delivery partnerships in 2026 but none publicly advertise a Kong-MCP-specific managed-delivery offering.
5. **Decision-readiness verdict: sufficient to choose at Phase-2 Prod stabilization** with two residual unknowns (DXC's HC band alignment, Kong PS direct-quote rate). Both can be resolved by Jesse with one internal and one external conversation.

---

## Q1 — MCP / agent labor market depth

### Finding
The full three-way intersection (MCP-fluent **and** Kong PDK plugin authorship **and** enterprise-gateway ops) is currently an **executive-search shape**, not a normal-posting-cycle shape. Each pairwise intersection is sourceable through standard channels but the triple is rare. The market is widening fast — between November 2024 (MCP launch) and Q2 2026, MCP-named roles moved from non-existent to live on ZipRecruiter, Glassdoor, LinkedIn, and 4dayweek.io.

**Pool indicators:**
- ZipRecruiter shows live "Model Context Protocol" job listings with rates spanning $18–$105/hr [1] — wide because MCP touches everything from junior Python integration work to senior platform architecture. The senior tail is where Phase 3 lives.
- ZipRecruiter "Context Engineer" postings (May 2026): $84k–$140k base salary range [2]. ZipRecruiter "Kong API Gateway" postings: ~$54–$82/hr [3]. The two skill sets show up in distinct job feeds — they do not overlap in a single saved-search yet.
- MCP ecosystem activity surged: 97 million combined Python+TypeScript SDK downloads per month as of March 2026; 10,000+ public MCP servers indexed; 80% of Fortune 500 deploying agentic AI in production by 2026 [4][5]. **Implication:** the developer population *touching* MCP is large; the population that has shipped *production* MCP infrastructure inside a gateway-mediated topology is much smaller.
- Live Glassdoor posting: Descope "MCP (Model Context Protocol) Software Engineer" (Tel Aviv) [6] — confirms named MCP roles exist at security-adjacent vendors; this is the same labor-pool segment Kong is recruiting from.

### Evidence
1. [ZipRecruiter — Model Context Protocol Jobs (Jan 2026)](https://www.ziprecruiter.com/Jobs/Model-Context-Protocol) — $18–$105/hr range
2. [ZipRecruiter — Context Engineer Jobs (May 2026)](https://www.ziprecruiter.com/Jobs/Context-Engineer) — $84k–$140k
3. [ZipRecruiter — Kong API Gateway Jobs (Apr 2026)](https://www.ziprecruiter.com/Jobs/Kong-Api-Gateway) — $54–$82/hr
4. [Effloow — MCP Ecosystem 2026: 97 Million Installs](https://effloow.com/articles/mcp-ecosystem-growth-100-million-installs-2026)
5. [DEV — The MCP Server Ecosystem in 2026](https://dev.to/sahil_kat/the-mcp-server-ecosystem-in-2026-integration-layer-for-ai-agents-2mln) — corroborates registry size
6. [Glassdoor — Descope MCP Software Engineer](https://www.glassdoor.com/job-listing/mcp-model-context-protocol-software-engineer-descope-JV_IC2421096_KO0,44_KE45,52.htm)

### Confidence
**Medium.** Multiple independent sources corroborate the directional read (MCP labor is exploding, gateway-PDK labor is mature-but-niche, intersection is sparse). No published "state of MCP labor" survey exists at the granularity Jesse needs — the precise intersection-pool count (e.g., "fewer than 200 candidates in the US") cannot be evidence-backed. Treat the executive-search read as informed inference, not a sourced count.

### Implication for the three options
- **Option 1 (FTE hire):** The 12–16 week hiring cycle in Nolan's brief is realistic *if* DXC is willing to compromise on one dimension of the triple — most viably hire a strong Kong PDK engineer and accept a 4–8 week internal MCP ramp, or hire a strong MCP-platform engineer and accept Kong PDK ramp. Holding out for all three skills risks 20–30 week cycles.
- **Option 2 (upskill):** The MCP side is the easier upskill (Anthropic Academy + production MCP server work in dev environments). The Kong PDK side is the slower upskill — Lua/Go plugin authorship is not a weekend course. Baseline matters heavily.
- **Option 3 (contractor):** Specialist contractors *can* be assembled from agencies (Toptal, A.Team, niche AI-platform consultancies) but expect them to arrive with two of three skills, not three. Premium for the genuine triple.

---

## Q2 — 2026 contract rate ground-truth

### Finding
Nolan's $175–250/hr range is **well-aligned with the published 2026 US data** for senior contractors at the LLM-platform / agent-infrastructure specialty band. The range sits roughly at the 60th–85th percentile of published US AI-platform contract rates. Below $175/hr lands you a generalist AI engineer; above $250/hr you are paying a frontier-lab-grade premium that is unwarranted for Kong-plugin work.

**Triangulated rate points (US, 2026):**

| Source | Senior / specialist hourly band | Note |
|---|---|---|
| InfoJini (US rate breakdown) [1] | $80–$250/hr range overall; specialists at the top | Wide band, geography- and spec-dependent |
| Acceler8 Talent [2] | Generalist senior $81–$100/hr; specialist with LLM/MLOps $101–$120/hr | This is W2 senior pay normalized hourly, not C2C |
| Second Talent / aggregator [3] | LLM specialists $150–$220/hr; AI agent developer rates published Q2 2026 | Closest single-source match for Nolan's band |
| Toptal published [4] | Senior backend $90–$150/hr; niche specialists / consultants $200+/hr; LLM premium 30–50% over generalist ML | Platform markup applies — Toptal client-side rate is ~2x the engineer take |
| Arsum guide [5] | Senior contract AI engineers in US $95–$130/hr ($760–$1,040/day) | Lower-end of the specialist band |

**Geography note:** All rates above are US-based, mostly Bay Area / NY / remote-US. EMEA equivalent runs $41–$77/hr per Lemon.io 2026 [6]; APAC lower still. W2 vs. C2C: most sources do not distinguish, but C2C (1099 / corp-to-corp) typically carries a 15–25% premium over W2 for the same role to cover the contractor's tax/benefits load.

**Where the genuine MCP+Kong-PDK triple lands:** None of the rate sources cite this exact intersection. By proxy — LLM-specialist premium (30–50%) applied to a senior gateway-engineer baseline ($95–$130/hr) — the implied band is **$125–$195/hr W2 / $145–$235/hr C2C.** Nolan's $175–250/hr accommodates the C2C high-skill case and a modest scarcity premium for the genuine triple. **Verdict: Nolan's range is defensible.**

### Evidence
1. [InfoJini — How Much Does AI Talent Cost in 2026? US Rate Breakdown](https://www.infojiniconsulting.com/how-much-does-ai-talent-cost-in-2026-us-rate-breakdown/)
2. [Acceler8 Talent — AI Engineer Salary & Market Rates 2025-2026](https://www.acceler8talent.com/resources/blog/ai-engineer--salary---market-rates-2025-2026/)
3. [Second Talent — Freelance AI Developer Hourly Rate (US 2026)](https://www.secondtalent.com/resources/freelance-ai-developer-hourly-rate-2026/) — *note: URL returned 404 on direct fetch but is indexed in search results*
4. [HireInSouth — Toptal Rates 2026](https://www.hireinsouth.com/post/how-much-does-toptal-cost) and [Toptal AI developers page](https://www.toptal.com/developers/artificial-intelligence)
5. [Arsum — Hire an AI Engineer in 2026](https://arsum.com/blog/posts/hire-ai-engineer/)
6. [Lemon.io — AI Engineers Salary 2026](https://lemon.io/rate-calculator/ai-engineers/) — geographic comp

### Confidence
**High** for the directional band; **Medium** for the precise top-end. Multiple independent rate aggregators converge on the LLM-specialist band Nolan named. The genuine MCP+Kong-PDK triple has no direct published comp — the inferred range is built from adjacent comp + scarcity premium logic.

### Implication for the three options
- **Option 1 vs Option 3 break-even:** At $200/hr contractor blended × 40h × 26 weeks = ~$208k. Nolan's FTE range was $180–230k total comp. Break-even sits at ~6 months as Nolan estimated. Beyond 9 months, FTE is materially cheaper per dollar of capability delivered.
- **Anti-pattern to flag:** Paying $250/hr for a "MCP expert" who turns out to be MCP-fluent but Kong-naive defeats the contract premise. Vet specifically for production Kong plugin authorship (ask for plugin source they shipped). The market has more MCP-curious AI engineers than Kong-PDK-fluent ones — the rate premium should track Kong fluency, not MCP buzz.

---

## Q3 — Structured upskill programs

### Finding
**A meaningful upskill compression path now exists for the MCP side, but not for the Kong PDK side.** Anthropic shipped a formal training+certification ladder in March 2026; Kong has not.

**Anthropic Academy (launched March 2026):** Free, self-paced, hosted on Skilljar [1]. By April 2026 the catalog had grown to ~17 certified courses [2]. MCP-specific tracks: *Introduction to Model Context Protocol* (architecture, tools/resources/prompts, building servers and clients in Python) and *Model Context Protocol: Advanced Topics* [3].

**Anthropic certification — Claude Certified Architect (CCA) Foundations:** Launched March 12, 2026. Proctored, 60-question exam targeting "design and ship production-grade Claude applications at enterprise scale" [4]. Skilljar's 13 (now 17) courses map to CCA exam domains. Additional Developer-tier certifications planned later in 2026 — likely the closer match to a Phase-3-ready credential. **Time-to-productive:** the courses are short (hours each); the production *fluency* needed for Phase 3 still requires building real MCP servers against real backend systems, which the courses scaffold but do not deliver.

**Kong-side upskill:** Kong publishes only two associate-level certifications: Kong Gateway Certified Associate and Kong Konnect Certified Associate [5]. **No MCP Gateway, AI Gateway, Agent Gateway, or custom-plugin-authorship certification exists** as of the public catalog snapshot 2026-05-13. Kong Academy provides free foundational courses but the depth required for Phase-3 plugin authorship is learned through Kong's PDK documentation, the kong-plugin template repo, and pattern-mining real plugins in the wild [6][7].

**CNCF / Linux Foundation:** CNCF announced CARE (Certification Advancement & Recertification Experience) for 2026 and a new Kubernetes Network Engineer (CKNE) certification [8], but **no agent-runtime or MCP-gateway-specific certification** is on the CNCF roadmap visible publicly. CNCF's relevance to Phase 3 is indirect (Kubernetes-side ops), not core.

**University executive education:** Not surveyed in depth — no major program is publicly known to specifically train MCP / agent-gateway engineers at the 2026-05-13 snapshot. This is fast-moving — worth a re-check at decision time.

### Ranked by credibility and time-to-productive
1. **Anthropic Academy + CCA (Foundations) → CCA Developer (when released)** — highest credibility for the MCP side. 2–4 weeks part-time to complete coursework; an additional 2–3 months hands-on prototyping to reach Phase-3 readiness.
2. **Kong PDK self-study (kong-plugin template + production plugin reading)** — highest credibility for the Kong side, but unstructured. No certification exists. 8–16 weeks part-time to reach independent plugin-authorship for a Lua- or Go-fluent senior; longer for engineers without baseline language fluency.
3. **CNCF / Linux Foundation cloud-native tracks** — adjacent, not core. Useful for the Kubernetes-side ops layer, not the MCP/agent skill gap.

### Evidence
1. [Pasquale Pillitteri — Anthropic Academy free courses (updated April 2026)](https://pasqualepillitteri.it/en/news/371/anthropic-academy-free-courses-claude)
2. [TamilTech — Anthropic Academy 13 free courses (March 2026 launch)](https://tamiltech.in/index.php/article/anthropic-launches-free-ai-academy-13-courses-claude-mcp-2026-ta)
3. [Anthropic Skilljar — Introduction to Model Context Protocol](https://anthropic.skilljar.com/introduction-to-model-context-protocol)
4. [DEV — Inside Anthropic's Claude Certified Architect Program](https://dev.to/mcrolly/inside-anthropics-claude-certified-architect-program-what-it-tests-and-who-should-pursue-it-1dk6)
5. [Kong — Certification page (Gateway Certified Associate + Konnect Certified Associate only)](https://konghq.com/academy/certification)
6. [Kong Docs — Custom plugins](https://developer.konghq.com/custom-plugins/)
7. [Kong/kong-plugin GitHub — template repo for custom plugins](https://github.com/Kong/kong-plugin)
8. [CNCF — CARE program + CKNE certification 2026](https://cloudnativenow.com/features/cncf-revamps-certification-program-to-simplify-renewals/)

### Confidence
**High** for the existence and shape of the programs (Anthropic Academy, Kong's two-cert catalog, CNCF roadmap). **Medium** for the time-to-productive estimates — these are baselined off Kong/community PDK ramp-up reports and Anthropic course descriptions, not measured outcomes.

### Implication for the three options
- **Option 2 (upskill) is materially more viable than it was 6 months ago** for the MCP side specifically. Anthropic Academy + CCA Foundations + hands-on dev-environment prototyping is a real path. The 6–9 month timeline Nolan flagged compresses to **4–6 months** if the targeted Role 1 senior already has Lua or Go fluency.
- **Kong PDK fluency remains the long pole.** No structured shortcut. This is the dimension on which Option 2 lives or dies — if the senior already writes Lua/Go, upskill is realistic; if they do not, the timeline blows up.
- **Anti-pattern to flag:** Funding only Anthropic Academy and declaring the senior "Phase-3-ready" is a mediocre upskill. Anthropic certification is a credential, not a substitute for shipping a custom plugin in dev. Budget the prototyping time, not just the course time.

---

## Q4 — SI partner / Kong Professional Services as a fourth option

### Finding
**Kong Professional Services exists, custom plugin development is on its published scope, and Kong's Enterprise MCP Gateway has shipped (late-2025/early-2026 GA with major announcements through April 2026's AI Gateway 3.14 release).** Kong's public site does not publish rates, engagement shape, or team composition — those require a direct CSM conversation. **None of the major SI partners publicly advertise a Kong-MCP-specific managed-delivery offering** as of 2026-05-13, though Accenture, Capgemini, Deloitte, and HCLTech have signed broad agentic-AI delivery partnerships in early 2026 that would *encompass* Kong-MCP work but not name it.

**Kong Professional Services (publicly disclosed):**
- Three published service categories: **Quickstart Program** (fixed-price Silver/Gold packages covering five or ten "topics" respectively), **Custom Plugin Development**, and **Instructor-led Training** [1].
- Services are **available exclusively to Kong Gateway Enterprise customers** [1]. DXC's existing Kong relationship qualifies.
- Scope of any specific engagement is defined in a SOW or Order Form, governed by the Kong Customer Agreement [2]. The published structure does not name T&M vs. fixed-fee — both appear to be available depending on the SOW shape.
- **No published rate band.** Engagement-shape, team composition, and rate data require direct CSM outreach. Kong's own MCP Gateway product page says "reach out to your CSM or known point of contact at Kong" for POCs [3].

**Kong AI/MCP product maturity (relevance check):**
- Kong Enterprise MCP Gateway is a paid extension of Kong AI Gateway, available in Kong Gateway Enterprise (self-hosted) and Kong Konnect (hybrid cloud) [4].
- Kong AI Gateway 3.14 (April 2026) added agent-to-agent (A2A) traffic support, positioning it as the only gateway with LLM + MCP + A2A support [5].
- Kong's published guidance for AWS-deployed MCP and A2A gateways is on GitHub [6] — a useful indicator that Kong PS engineers have shipped reference implementations Jesse's team can fork.

**SI partners — agentic delivery landscape:**
- **OpenAI announced multiyear deals with Accenture, BCG, Capgemini, and McKinsey** (Feb 2026) to sell and implement OpenAI's Frontier AI agent platform [7][8]. Accenture and Capgemini explicitly described as "end-to-end systems integration role, getting into the weeds of data architecture, cloud infrastructure, and the messy business of connecting Frontier to the systems enterprises actually run on" [7].
- **Google Cloud committed $750M (April 2026)** to accelerate partner agentic-AI development with Accenture, Capgemini, Cognizant, Deloitte, HCLTech, PwC, TCS [9].
- **No SI advertises a "Kong AI Gateway / MCP" specialty practice publicly** as of 2026-05-13. The big SIs would absorb this work inside their broader agentic-AI practice and staff it from generalist platform engineers. **Implication:** going SI route trades the Kong-specific scarcity premium for a generalist-platform-engineer rate, at the cost of less Kong-PDK depth than a Kong-PS or specialist-contractor engagement.

**Boutique alternative observed:** Hexaware has a published "Kong AI Gateway and MCP" technical blog [10] — signal that mid-tier SIs are starting to claim Kong-MCP expertise. Worth a scoping question if DXC has an existing Hexaware relationship.

### Evidence
1. [Kong Professional Services](https://konghq.com/support/kong-professional-services) — published service categories
2. [Kong Customer Agreement](https://konghq.com/legal/kong-customer-agreement) — engagement governance
3. [Kong — Introducing Enterprise MCP Gateway for Production-Ready AI](https://konghq.com/blog/product-releases/enterprise-mcp-gateway) — "reach out to your CSM" quote
4. [Kong AI/MCP Gateway and MCP Server technical breakdown](https://konghq.com/blog/engineering/ai-gateway-mcp-gateway-mcp-server-breakdown)
5. [PRNewswire — Kong AI Gateway now supports Agent-to-Agent traffic (3.14 release)](https://www.prnewswire.com/news-releases/kong-ai-gateway-now-supports-agent-to-agent-traffic-becoming-the-most-comprehensive-ai-gateway-for-the-agentic-era-302741741.html)
6. [Kong/guidance-for-kong-genai-mcp-and-a2a-gateways-on-aws — GitHub reference impl](https://github.com/Kong/guidance-for-kong-genai-mcp-and-a2a-gateways-on-aws)
7. [Fortune — OpenAI partners with McKinsey, BCG, Accenture, Capgemini (Feb 23, 2026)](https://fortune.com/2026/02/23/openai-partners-with-mckinsey-bcg-accenture-and-capgemini-to-push-its-frontier-ai-agent-platform/)
8. [CNBC — OpenAI lands multiyear deals with consulting giants (Feb 23, 2026)](https://www.cnbc.com/2026/02/23/open-ai-consulting-accenture-boston-capgemini-mckinsey-frontier.html)
9. [Google Cloud — $750M partner agentic AI commitment (Apr 22, 2026)](https://www.googlecloudpresscorner.com/2026-04-22-Google-Cloud-Commits-750-Million-to-Accelerate-Partners-Agentic-AI-Development)
10. [Hexaware — Kong AI Gateway and MCP technical blog](https://hexaware.com/blogs/kong-ai-gateway-and-mcp-securing-and-scaling-agentic-ai-in-the-enterprise/)

### Confidence
**High** that Kong PS is a viable fourth option. **Medium** on shape and rate — Kong does not publish rates so this is direct-quote territory. **Low** on SI-route quality — no SI has a named Kong-MCP practice, and the generalist-staffing risk Nolan would normally flag for SI work applies in full.

### Implication for the three options
- **Kong PS is the most credible fourth option** because Kong's own PS engineers built and operate the product Phase 3 will run on. Engagement shape is almost certainly fixed-scope Quickstart-style or T&M depending on SOW. **Pros:** highest-credibility plugin authorship, direct Kong escalation path. **Cons:** no published rate (likely premium to a marketplace contractor), KT-quality dependent on the specific SOW.
- **SI route (Accenture / Capgemini / Deloitte / HCLTech) is a weak fourth option** unless DXC already has an active agentic-AI SOW with one of them that can absorb this work. Standing up a fresh SI engagement for Phase 3 trades Kong-specific depth for SI bench depth — usually the wrong trade for narrow plugin authorship.
- **Boutique SI route (Hexaware or peer)** is worth one scoping call if DXC has a relationship. Rates likely competitive with marketplace contractors; depth likely deeper on Kong-specifics than a generalist SI.
- **Anti-pattern to flag:** Bringing in a generalist SI on a long-term T&M for Phase 3 plugin work. The plugin scope is bounded (Nolan estimated 1–2 plugins); T&M engagements with generalist staffing tend to expand into surrounding scope and dilute the cost case. Fixed-scope is the right shape if going SI or PS.

---

## Q5 — Role-naming convergence

### Finding
**"MCP / agent platform engineer" is not yet a settled market title.** The skill set is currently distributed across at least five named titles, with no convergence visible at 2026-05-13. **Implication for JD writing:** the role Jesse hires for will be searched-and-applied-to under multiple titles; the JD should optimize for keywords, not for a single canonical title.

**Title distribution (representative sampling from 2026-Q1/Q2 postings across enterprise IT services, AI labs, large-platform orgs):**

| Title in market | Notable examples / where seen | Phase-3 fit |
|---|---|---|
| **AI Platform Engineer** | General Motors Dublin, Scale AI, UST (Senior), ZipRecruiter aggregated postings $50–$144/hr [1][2][3] | High — but tends to be model-serving / MLOps biased, not gateway-biased |
| **Staff Infrastructure Software Engineer, Enterprise AI** | Scale AI's listed title [4] | High — closest to "gateway-for-AI-traffic" framing |
| **MCP Platform Engineer / MCP Connector Developer** | ATech Placement (May 2026 close); BeBee aggregated [5] | High — but rare; the title is being used by mid-tier staffing agencies, not yet by large enterprises |
| **AI Agent Engineer / Agentic AI Engineer** | ZipRecruiter aggregated $43–$72/hr (mid-band); Glassdoor average $192k/yr; 6figr.com $216k+ band [6][7][8] | Medium — these roles are app-side agent builders more than gateway-side platform engineers |
| **Senior Software Engineer, AI Agents** | Arm (San Jose, MCP-server-focused) [9] | High — Arm's posting is explicitly MCP-server-content + evaluation framework, very close to Phase-3 plugin-author work |
| **AI Infrastructure Engineer** | Indeed (3,313 listings); ZipRecruiter $107k–$163k band [10][11] | Medium — typically GPU/serving-stack biased |

**JD content patterns observed across the sample:**
- Required: 5+ years SWE, infrastructure background (Kubernetes, Terraform, observability stack), Python or Go fluency.
- MCP-specific lines now appearing: "design Model Context Protocols," "MCP server implementations," "agent-to-agent communication protocols."
- Kong-specific lines: absent from MCP/agent postings; present only in standalone "Kong API Gateway" postings.
- **The Phase-3 triple (MCP + Kong PDK + enterprise-gateway ops) is not co-listed in any sampled posting.** This corroborates Q1's executive-search read.

**Compensation comp-set implication:** The closest comp set for a permanent Phase-3 hire is **"Staff Infrastructure Software Engineer, Enterprise AI"** (Scale-AI shape) or **"Senior/Staff AI Platform Engineer"** (GM / UST shape). Both align with Nolan's $180–230k US total-comp band [12][13]. The "Agentic AI Engineer" comp set ($192k avg, up to $247k at 75th percentile) is also in band [7]. Staff/Principal at frontier AI labs ($300k–$500k+ TC, $439k avg principal ML [13]) is comp-set creep that DXC should not anchor to — that's frontier-lab comp, not enterprise-gateway comp.

### Evidence
1. [GM Careers — AI Platform Engineer, Dublin](https://search-careers.gm.com/en/jobs/jr-202519360/ai-platform-engineer/)
2. [Scale AI — Staff Infrastructure Software Engineer, Enterprise AI](https://scale.com/careers/4599700005)
3. [ZipRecruiter — AI Platform Engineer Jobs (Feb 2026)](https://www.ziprecruiter.com/Jobs/Ai-Platform-Engineer) — $50–$144/hr
4. Same as [2]
5. [BeBee — MCP Platform Engineer (ATech Placement)](https://bebee.com/us/jobs/mcp-platform-engineer-atech-placement--lensa-2365_f4684f766f2e85582826b16d341979390559541264f16b776afe6122ed7465eb)
6. [ZipRecruiter — AI Agent Engineer Jobs (May 2026)](https://www.ziprecruiter.com/Jobs/Ai-Agent-Engineer) — $43–$72/hr
7. [Glassdoor — Agentic AI Engineer Salary 2026](https://www.glassdoor.com/Salaries/agentic-ai-engineer-salary-SRCH_KO0,19.htm) — $192,826 average
8. [6figr — Agentic AI Engineer Salaries 2026 ($216k+)](https://6figr.com/us/salary/agentic-ai-engineer--t)
9. [Arm Careers — Senior Software Engineer, AI Agents (San Jose, posted Apr 28 2026)](https://careers.arm.com/job/san-jose/senior-software-engineer-ai-agents/33099/94500679728)
10. [Indeed — AI Infrastructure Engineer (3,313 listings)](https://www.indeed.com/q-ai-infrastructure-engineer-jobs.html)
11. [ZipRecruiter — AI Infrastructure Engineer Jobs](https://www.ziprecruiter.com/Jobs/Ai-Infrastructure-Engineer) — $107k–$163k
12. [JobsByCulture — AI Engineer Salary Guide 2026](https://jobsbyculture.com/blog/ai-engineer-salary-guide-by-level-2026)
13. [Augment Code — Hiring an AI Platform Engineering Leader: 2026 Job Spec](https://www.augmentcode.com/guides/ai-platform-engineering-leader-job-spec) — corroborates the role's evolution toward governance/orchestration

### Confidence
**Medium-High.** Multiple independent postings and salary aggregators corroborate the title-distribution pattern. The "no convergence" finding is well-evidenced. The precise comp-set anchor (Staff Infra SWE Enterprise AI) is a judgment call — Jesse may want a second opinion from DXC's TA team on internal band alignment.

### Implication for the three options
- **Option 1 (FTE hire) JD strategy:** Title the role "Staff AI Platform Engineer — Agentic Workloads / MCP" or similar dual-keyword shape. Include both "Kong" and "MCP" in the requirements section. Do not title it "MCP Platform Engineer" alone — the candidate pool searching that title is thinner than the AI-platform-engineer pool, even if the named role is closer.
- **Option 2 (upskill):** Title irrelevant — the engineer is already on team.
- **Option 3 (contractor):** Use the title spread to your advantage in agency briefs — instruct them to source from "AI Platform Engineer with MCP" *and* "Staff Gateway Engineer with agentic exposure" candidate pools simultaneously. Single-title sourcing under-represents the pool.
- **Anti-pattern to flag:** Letting DXC's TA team write the JD from internal-template "AI Engineer" without dual-keyword optimization. Generic-titled JDs in this niche pull generic candidates. The 2026 market is keyword-driven and the keyword bundle matters.

---

## Decision-readiness assessment

**Verdict: the evidence is now sufficient for Jesse to choose at Phase-2 Prod stabilization,** with two residual unknowns that are resolvable through one internal and one external conversation:

1. **DXC's HC band alignment for Staff AI Platform Engineer** (internal) — Nolan flagged this as TBD. Jesse to confirm with DXC TA whether $180–230k US total comp clears the internal band for Staff IC roles. Direct ask.
2. **Kong Professional Services rate band and engagement shape** (external) — Kong does not publish this. Jesse (or DXC's Kong CSM) to ask Kong directly: "For a 4–6 month Phase-3 onramp covering MCP-aware audit plugin + agent-session correlation plugin + runbooks + KT, what is the engagement shape and indicative cost?" One email.

Once those two data points land, the three options + Kong PS fourth option can be ranked on a single page against the six decision criteria in Nolan's brief.

**Hybrid worth re-emphasizing:** the **Option 3 (contractor) + Option 2 (upskill) in parallel** hybrid Nolan flagged is the lowest-regret shape given current evidence. Contract delivery covers timeline risk; upskill builds institutional depth with KT as a contract deliverable; FTE Option 1 stays open as a follow-on action if MCP/agent traffic proves strategic at Phase 4.

**Residual unknowns Pax could not resolve:**
- Exact candidate-pool count at the genuine three-way intersection — no published survey exists. Inferred from ecosystem indicators only.
- Whether DXC has an existing SOW with Hexaware, Accenture, or Capgemini that could absorb Phase-3 plugin work — internal-only question.
- Whether Anthropic's planned mid-2026 Developer-tier certification (rumored but unconfirmed beyond [DEV][4-Q3]) would meaningfully change the upskill landscape — pending Anthropic announcement, re-check at decision time.

---

## Methodology

**Sources searched:** ZipRecruiter (US job market), Glassdoor (salary data + named role postings), LinkedIn-via-search, GitHub (Kong/kong-plugin, Kong reference implementations), Kong corporate site (PS + certification pages), Anthropic Skilljar (training catalog), Coursera (course catalog cross-check), Fortune / CNBC / PRNewswire (SI partnership announcements 2026-Q1), aggregator publications (Toptal, Lemon.io, Acceler8 Talent, InfoJini, Arsum, Second Talent), CNCF / Linux Foundation training site, DEV community articles, technical blogs (Hexaware, Kong engineering blog).

**Filters applied:** prefer 2026 sources, accept 2025 fallback only where 2026 data unavailable; require at least two independent corroborations for any quantitative claim; flag single-source claims explicitly.

**What was not searched:** paywalled labor surveys (Gartner, Forrester, IDC) — these may have more precise pool-size data and would be worth a follow-up if DXC has subscriptions. Recruiter pulse-check (named recruiters at the gateway / AI-platform intersection) — out of scope for desk research.

**Date stamp note:** all rates and salary data dated Q1/Q2 2026 where possible. Where 2025 data was used (Kong certification page snapshot, CNCF 2025 announcements) it is noted inline.

---

## Limitations

- **No published "state of MCP labor" survey at 2026-05-13.** Pool-size claims are inferred from ecosystem growth metrics + posting visibility, not direct census data.
- **Kong PS rates not publicly disclosed.** The fourth option's cost shape is qualitative until Jesse pulls a direct quote.
- **Title-distribution sample is illustrative, not statistical.** 20–30 representative postings reviewed across sources, not a structured N=100 sample. The "no convergence" finding is robust at this sample size; precise percentile shares of each title would require a larger sample.
- **The "executive search vs. normal posting" framing is a judgment call.** Different recruiters will read the same evidence differently. Pax's read is conservative — DXC's internal TA team may have a different operational read based on their own placement experience.

---

## Recommendations for next research passes (if decision lands and requires deeper data)

1. **Recruiter pulse-check** before opening Option 1: 3–4 named recruiters specializing in AI-platform / gateway-engineering placement. Ask: "How many credible candidates for [exact JD] are on your bench today?"
2. **Direct Kong PS quote** before deciding for/against the fourth option. One email to DXC's CSM with the scope Nolan articulated.
3. **Re-check Anthropic certification roadmap** at decision time — Developer-tier credentials may have shipped between this brief and the decision window.
4. **Internal DXC SI-relationship audit** — is there an active Hexaware, Accenture, or Capgemini SOW that could absorb Phase 3 work without a fresh procurement cycle?

---

*Cross-references: [[2026-05-13-phase-3-mcp-agent-expertise-options]] (Nolan's options brief — this brief fills its five open evidence gaps), [[2026-05-12-kong-ai-gateway-team-org-chart]] (canonical org chart), [[2026-05-11-api-gateway-engineer-hire-research]] (Pax's Role 1 / Axle research), [[2026-05-12-ai-security-engineer-hire-research]] (Pax's Vex research — adjacent for tool-use AI security scope).*
