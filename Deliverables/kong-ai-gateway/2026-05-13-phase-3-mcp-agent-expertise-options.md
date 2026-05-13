---
type: deliverable
audience: DXC internal — Jesse, practice leads
status: planning-only — no hire decision made
date: 2026-05-13
author: Nolan (HR)
project: kong-ai-gateway
related: "[[2026-05-12-kong-ai-gateway-team-org-chart]]"
---

# Kong AI Gateway — Phase 3 MCP/Agent Expertise: Sourcing Options Brief

**Prepared for:** Jesse Swensen, DXC  
**Date:** 2026-05-13  
**Purpose:** Structured options analysis for the Phase 3 skill gap. Planning artifact only. No hire decision is being made. Return to this document when Phase 2 reaches steady state in Prod.

---

## Problem statement

Phase 3 (Agentic workloads) introduces a capability surface — MCP protocol, agent-session lifecycle management, multi-step orchestration observability, and likely one or two custom Kong plugins — that none of the existing 13-role org chart explicitly carries. This is not an oversight in the org chart design; it is a deliberate deferral because MCP tooling and agent-gateway patterns were still emerging when the org chart was drafted (Kong 3.x does not ship first-class MCP-aware audit or agent-session rate-limiting catalog plugins as of 2026-05-13). The gap becomes load-bearing at Phase 3 entry, which falls inside Tier 2 of the team maturity arc. Axle has flagged that the sourcing decision must land 3–4 months before Phase 3 entry — not at entry — because hiring, onboarding, or upskilling a specialist in this space carries material lead time regardless of path chosen. Jesse's primary DXC engagement runs through mid-August 2026; Phase 3 entry is post-engagement. This brief exists so the decision process can be picked up cleanly when it re-enters the planning horizon.

---

## Skill gap inventory (what Phase 3 actually requires)

Before evaluating options, a grounded inventory of what the role must cover:

| Capability domain | Load-bearing for Phase 3 |
|---|---|
| MCP protocol mechanics | Tool-call routing, per-tool auth/scope models, MCP server hosting patterns in a Kong-proxied topology |
| Agent-session lifecycle | Long-running session correlation (session IDs across multi-step traces), connection state at the gateway layer, session budget and rate-limiting patterns |
| Multi-step orchestration observability | OTel span stitching across agent turns, trace attribution to initiating user identity, agent-step anomaly detection |
| Custom plugin authorship (Lua or Go) | MCP-aware audit plugin, agent-session correlation plugin — Kong's catalog does not cover these natively |
| AI security for tool-use | Per-tool authorization scopes, exfiltration risk on tool-call payloads, prompt-injection via tool response surfaces |

The last two items — custom plugin authorship and tool-use AI security — are the ones most likely to require net-new skill. Lua/Go PDK for Kong plugin authorship is a skill the Role 1 senior can grow into, but it requires dedicated time. AI security for tool-use traffic is adjacent to Role 5 (AI-Aware Security Engineer), though the Vex brief did not explicitly scope MCP/agent attack surfaces.

---

## Option 1 — Net-new dedicated hire: MCP/Agent Platform Engineer

**Profile:** Staff-level engineer with demonstrated production experience in agentic workload infrastructure — MCP server operations, agent orchestration (LangGraph, CrewAI, or similar), gateway-layer integration for tool-use traffic, and Kong Lua or Go PDK plugin authorship. This is a rare skill combination in 2026; expect a long sourcing cycle and senior-IC compensation.

| Dimension | Assessment |
|---|---|
| **Pros** | Cleanest capability acquisition; full-time focus; builds institutional knowledge from day one; no knowledge-transfer cliff at end of contract; long-term enterprise-strategic if MCP/agent patterns become the dominant AI-traffic shape (likely) |
| **Cons** | Slowest to source (12–16 week realistic hiring cycle); highest base compensation; candidate pool is thin — MCP-fluent + Kong-plugin-authorship + enterprise-gateway background rarely coexist in one profile |
| **Cost shape** | Senior IC band: $180–230k USD total comp (US market); principal-level in APAC or EMEA will vary by geography. DXC's internal band alignment unknown — Jesse to confirm. |
| **Time to productive** | 3–5 months from offer to fully independent on Phase 3 work (assumes 4-week notice, 2-week onboarding, 6–8 weeks ramp on Kong specifics and DXC topology) |
| **Retention/KT risk** | Low retention risk if work remains challenging (Phase 3 into Phase 4 and beyond is a multi-year mandate). KT risk is minimal because the role is permanent — knowledge stays on the team. |

**When this option wins:** Phase 3 is not the last time DXC will encounter MCP/agent traffic. If the enterprise-strategic read is that agentic workloads represent 30–50% of future AI traffic, this skill set is permanently load-bearing and a permanent hire is the right shape.

---

## Option 2 — Upskill Role 1 senior: invest in MCP/agent expertise

**Profile:** Identify the most capable senior on the gateway engineering team (Role 1) and fund a structured upskill investment: dedicated learning budget, external mentorship with an MCP/agent practitioner, hands-on prototyping time inside the DXC Phase 3 dev environment, and Kong PDK plugin authorship mentorship.

| Dimension | Assessment |
|---|---|
| **Pros** | Zero sourcing lead time (the person is already on the team); preserves DXC's investment in an engineer who already knows the topology, the IaC patterns, and the org; cheapest direct cost; builds deep institutional knowledge with no onboarding tax |
| **Cons** | Highest timeline risk — if the individual lacks adjacent foundational knowledge (distributed tracing, plugin authorship, agentic architecture patterns), upskill to Phase-3-ready may take 6–9 months of part-time investment; creates single-point-of-failure risk for Phase 3 if the upskilled engineer departs; takes the senior partially off their current Role 1 load during the upskill period, straining an already-lean Tier 1 team |
| **Cost shape** | Training/mentorship budget: $10–25k; opportunity cost of partial role-1 backfill or reduced throughput during upskill period. If a formal MCP/agent certification or structured program exists (check in mid-2026), add that cost. |
| **Time to productive** | 4–7 months from decision to Phase-3-ready, depending on the individual's starting baseline. Compressed if the engineer already writes Lua/Go and has exposure to agentic patterns. |
| **Retention/KT risk** | Highest KT risk of the three options: if the upskilled engineer leaves, the institutional knowledge walks. One engineer = one bus. Mitigate by pairing them with a second engineer in Option 3 (contractor) who documents patterns as they go. |

**When this option wins:** The targeted Role 1 senior already has Lua/Go fluency and has independently explored agentic tooling; the upskill delta is domain knowledge rather than foundational skill; and DXC's Phase 3 timeline is not aggressive (no entry date pressure forcing a 12-week decision).

---

## Option 3 — Contract specialist: short-term engagement through Phase 3 entry + stabilization

**Profile:** Engage a specialist contractor with specific MCP/agent + Kong expertise for a defined engagement (roughly 4–6 months covering Phase 3 dev/test + Prod entry + 4–6 weeks stabilization). Contract scope: deliver the custom plugins (MCP-aware audit, agent-session correlation), author the Phase 3 runbooks and architecture decision records, and run knowledge transfer to the permanent team in the final weeks.

| Dimension | Assessment |
|---|---|
| **Pros** | Fastest time to capability (engagement can start within 4–8 weeks of decision); no permanent headcount approval required (useful if DXC's HC process is slow); contractor is accountable for a defined deliverable (plugins + runbooks + KT), not an open-ended role; DXC can evaluate whether the capability should stay in-house after seeing Phase 3 land |
| **Cons** | Highest per-hour cost of the three options; knowledge-transfer quality is highly variable — if KT is rushed or the contractor disengages early, the permanent team is left with undocumented plugins; no long-term retention; the specialist's incentive is to close the engagement, not build durable patterns; risk of over-dependence if Phase 4 needs return to MCP/agent work |
| **Cost shape** | Senior contractor rates for MCP/agent + Kong plugin authorship: $175–250/hr US market (2026 range; thin pool commands premium). 6-month engagement at 40h/week = $175k–$260k. Materially cheaper than a FTE total comp only if the FTE hire would be empty for 12+ months while sourcing. Directionally break-even at 6 months; worse than FTE beyond that. |
| **Time to productive** | 2–4 weeks from contract start (contractor should arrive fluent — no ramp on fundamentals). Fastest of the three options. |
| **Retention/KT risk** | Highest KT risk in the steady state: all knowledge leaves with the contractor unless the permanent team has the capacity and discipline to absorb it in real-time. Requires a dedicated KT phase (2–4 weeks) as a hard contract deliverable, not an afterthought. Pairs best with Option 2 (upskill the Role 1 senior to be the KT recipient). |

**When this option wins:** Phase 3 entry date is hard-locked and the hiring cycle for Option 1 cannot close in time; DXC HC constraints make permanent headcount difficult to approve at the current planning horizon; or the team wants to validate the scope of the permanent role (Option 1) before committing to a full-time profile.

---

## Hybrid worth considering

**Option 3 + Option 2 in parallel:** Engage the contractor for Phase 3 delivery while the Role 1 senior runs alongside as the KT recipient and co-author. This closes the timeline risk (contractor delivers Phase 3 on schedule) while building internal depth (senior emerges from Phase 3 with plugin authorship and MCP/agent operational experience). If DXC then evaluates that the capability is enterprise-strategic long-term, Option 1 (net-new hire) can be opened as a separate action with the existing senior as a hiring signal that DXC takes this space seriously.

---

## Decision criteria for Jesse

Weight these against DXC's specific constraints when the decision window opens:

1. **Is MCP/agent expertise enterprise-strategic beyond this gateway phase?** If agentic workloads represent a growing fraction of DXC's AI traffic long-term, a permanent hire (Option 1) compounds in value. If Phase 3 is a contained workload with low recurrence, a contract engagement (Option 3) is the correct shape.

2. **What is the Phase 3 entry date firmness?** A hard-locked entry date (driven by DXC stakeholder commitment or regulatory timeline) heavily favors Option 3 or the hybrid. A soft entry with a 12+ month runway opens Option 1.

3. **Is the Role 1 senior baseline strong enough for upskill?** Option 2 is only viable if the targeted individual already writes Lua/Go and has adjacent exposure to distributed tracing and agentic architectures. If the baseline is thin, upskill time balloons past the Phase 3 window.

4. **What is DXC's HC approval velocity?** If permanent headcount approvals take 4–6 months, Option 1's effective lead time extends to 7–10 months from decision. In that case, Option 3 becomes the bridge while Option 1 is approved.

5. **What is the plugin authorship load after Phase 3?** If custom Kong plugin work is expected to be ongoing (Phase 4 onboarding, new regulatory requirements, new AI traffic patterns), a permanent hire with PDK fluency has compounding ROI. If Phase 3 plugins are a one-time build, contract authorship is sufficient.

6. **Is there a DXC managed-services or SI-partner option?** DXC operates SI-partner relationships (Kyndryl, others) that may carry Kong professional services with MCP/agent exposure. This is a fourth option not fully evaluated here — worth a Pax scoping question.

---

## Recommended trigger for revisiting

**At Phase 2 entry + stabilization in Prod** — defined as: Phase 2 enforcement plugins have been running on the high-risk 20% of traffic in production for at least 4 weeks with no P1/P2 incidents. At that point, Phase 3 is the active next horizon, and the 3–4 month lead time Axle flagged becomes the operational constraint. The trigger is milestone-based, not calendar-based: if Phase 2 Prod stabilization slips, this decision window shifts with it.

Secondary trigger: **if Jesse's post-engagement role at DXC gives him direct influence over Phase 3 staffing**, this document is the brief to bring to that conversation. File it in the Phase 3 project intake, not just in myPKA.

---

## Open questions for Pax before a decision is made

> **Market research now available:** Pax has answered all five questions below in [[2026-05-13-phase-3-expertise-market-research]] (2026-05-13). Headline findings: (1) full three-way intersection is executive-search thin, (2) $175–250/hr contract band is defensible, (3) Anthropic Academy compresses MCP-side upskill but Kong PDK remains the long pole, (4) Kong Professional Services is a credible fourth option but rates are direct-quote only, (5) no settled market title — JD must be dual-keyword. See that brief for evidence, confidence levels, and decision-readiness assessment.

These require market research that Nolan cannot answer from internal knowledge:

1. **MCP/agent labor market depth.** How many credible candidates exist at the intersection of: MCP-fluent, Kong plugin authorship (Lua or Go PDK), and enterprise-gateway operations experience? Is this a "post in LinkedIn and wait" market or an executive-search market?

2. **Contract rate benchmarks.** What is the current market rate for a senior contractor with MCP/agent + gateway-layer experience? The $175–250/hr range above is directional — Pax should ground-truth this against 2026 contract market data.

3. **Upskill programs.** Are there structured MCP/agent training programs (certification-style, bootcamp-style, or vendor-backed) that could compress Option 2's timeline? Kong has a partner-training program — does it cover MCP integration patterns?

4. **SI partner option.** Do DXC's existing Kong SI partners (or Kong Professional Services directly) offer Phase 3 onramp engagements that would cover Option 3-equivalent scope without a direct contractor engagement? What is the rate and typical engagement shape?

5. **Role convergence check.** Is "MCP/agent platform engineer" emerging as a named role in the market, or is this currently being absorbed by staff-level gateway engineers, AI platform engineers, or ML platform engineers? The answer shapes the JD if Option 1 proceeds.

---

*Cross-references: [[2026-05-13-phase-3-expertise-market-research]] (Pax's market research filling the five open questions above), [[2026-05-12-kong-ai-gateway-team-org-chart]] (canonical org chart — skill gaps section), [[2026-05-11-api-gateway-engineer-hire-research]] (Pax's Role 1 research), [[2026-05-12-ai-security-engineer-hire-research]] (Pax's Vex research — adjacent for tool-use AI security scope).*
