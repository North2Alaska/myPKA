---
type: deliverable
audience: DXC internal — Jesse, hiring managers, practice leads
status: draft
date: 2026-05-12
updated_at: 2026-05-13
author: Nolan (HR)
project: kong-ai-gateway
---

# Kong AI Gateway — Phased Team Org-Chart Proposal

**Prepared for:** Jesse Swensen, DXC  
**Date:** 2026-05-12 (revised 2026-05-13)  
**Purpose:** Staffing model for a production Kong AI Gateway practice at DXC enterprise scale — routing ALL AI traffic from ALL DXC employees and DXC-developed applications through a single, centrally governed gateway. Use alongside [[2026-05-12-kong-api-gateway-engineer-jd]] (deep-cut JD for Role 1).

> **Scope revision note (2026-05-12):** The original draft was framed around a first-production-rollout for a single business unit. Jesse confirmed the actual scope: DXC is a 130,000-employee multinational, and the goal is to route every AI request from every employee and every DXC-developed application through this gateway. The original "minimum-viable team" is now Tier 1 (the on-ramp). The original "steady-state" is reclassified as a mid-scale shape that does not reach the real destination. This document has been rebuilt from the ground up to match enterprise-platform scope. Leadership should budget to reach Tier 2 within 12 months of Tier 1 go-live — not eventually.

> **Revision 2026-05-13 — two foundational decisions added:** Jesse locked in (a) a **three-environment tier** (Dev → Test → Prod) for every phase of work, and (b) a **four-phase implementation plan** (Discover → Pareto risk reduction → Agentic workloads → Everything else) that sits orthogonally to the team-maturity tiers. Two dimensions to keep straight: **maturity tiers** (Tier 1 / 2 / 3) describe the team and the practice — how many FTE, what coverage, what governance posture. **Phases** (1–4) describe the gateway's workload coverage — what traffic is in scope and what posture (observe-only vs. enforcing). Phases progress through Dev → Test → Prod independently of which tier the team is at. The team grows along the tier axis; the workload grows along the phase axis. Both axes carry their own hiring and skill-gap implications. The two are connected, not coincident: Phase 1 (Discover) typically lands during Tier 1 of the team; Phase 3 (Agentic workloads) is when Tier 2's missing skill — MCP / agent / tool-use expertise — becomes load-bearing and likely requires a net-new hire or aggressive upskill. See the new "Phase plan" and "Environment tiers" sections below; the per-tier FTE tables now carry phase and env-tier annotations where they shape the work.

---

## Environment tiers — Dev / Test / Prod (decision locked 2026-05-13)

Every workload the gateway carries — at any maturity tier, in any phase — passes through three environment instances on its way to production:

| Env | Purpose | Traffic shape | Owners |
|---|---|---|---|
| **Dev** | Engineering iteration, plugin-chain experimentation, integration testing against mocked / sandbox model endpoints. Drift here is acceptable as long as it never promotes. | Synthetic + sandbox provider endpoints. No real DXC employee traffic. | Gateway engineering (Role 1), DevOps (Role 2). Self-service for engineering. |
| **Test** | Stakeholder UAT, security scanning (Garak, promptfoo, plugin-chain red-team), performance / load testing, pre-prod validation. Last stop before real users. | Mirrored / shadow traffic patterns; controlled stakeholder cohorts; security and load harnesses. | Gateway engineering + AI-security (Role 5) + Compliance (Role 9) for UAT sign-off. Observability (Role 3) for load profile. |
| **Prod** | Live enterprise traffic for 130k DXC employees. The only environment that touches a real provider key with real cost attribution. | All real DXC AI traffic governed by the gateway. | Gateway engineering + on-call rotation (Role 12). Tier-1 NOC (Role 11) on first-line incidents from Tier 2+ onward. |

**Standard promotion gates: Dev → Test → Prod.** Every gateway-entity change (decK YAML) and every tenancy change (Terraform) passes through PR-gated `deck diff` / `terraform plan` review against each environment in sequence. No change reaches Prod that hasn't been verified in Test against representative traffic. Emergency hot-fixes get a 24h back-fill PR.

### Konnect-native pattern for three environments (open — arch team confirms)

Kong's recommended pattern for multi-environment Konnect at DXC's scope is **one Konnect organization with multiple control planes**, one control plane per environment (Dev / Test / Prod). This is the modern shape that replaced the older "Runtime Groups" naming. It gives DXC single-pane billing, single audit trail, central RBAC, and a shared plugin catalog while keeping environments operationally isolated (a Dev mistake cannot touch a Prod data plane). It composes cleanly with the Tier 1 tenancy recommendation (Option A — single Konnect org, namespaced control planes per BU): the BU dimension and the env-tier dimension are both control-plane-shaped, so the IaC repo carries a control plane per (env, BU) pair.

**Alternatives we considered:**

- **Separate Konnect organizations per environment.** Highest blast-radius isolation; highest operational cost (separate Terraform state, separate billing, separate RBAC per org). Not justified for env separation alone — the BU dimension already drives that conversation in Tier 1.
- **Tagged data plane groups within a single control plane.** Cheapest, lowest isolation. A config-level Dev mistake propagates to Prod data planes attached to the same control plane. Not acceptable at DXC's regulatory surface.

**Recommendation:** one Konnect org, one control plane per environment per BU (Tier 1), evolving to one control plane per (env, BU, region) at Tier 2 once multi-region data planes land. **Status: open — arch team to confirm** as a Row 3 sub-decision in the architecture ask. The ask doc has been updated to include this.

### Operational implications

- **On-call ownership.** Dev is best-effort, business-hours, owned by the engineer who last touched the relevant route. Test is owned by the gateway practice during UAT windows. Prod is the only environment in the formal on-call rotation. At Tier 1 the sole gateway engineer owns all three — see on-call math for why this is unsustainable past 6 months. At Tier 2+ the rotation is Prod-only; Dev and Test escalations are working-hours requests.
- **Observability split.** OTel traces and File Log audit events emit from all three environments, but Prod is the only environment with FinOps-grade dashboards and the only environment whose data feeds chargeback. Dev / Test telemetry is short-retention and used for engineering debugging only.
- **Secrets backend separation.** Each environment references vault paths scoped to that environment (`vault://prod/<provider>-key`, `vault://test/<provider>-key`, etc.) — no cross-environment key sharing. Dev typically uses sandbox provider credentials; Test uses staging credentials where the provider supports them; Prod uses production keys with strict rotation cadence.

---

## Phase plan — four implementation phases (decision locked 2026-05-13)

The four phases are the workload axis: they describe what AI traffic the gateway carries and what governance posture applies. Phases are sequential by entry-gate but **overlapping in execution is the norm** — Phase 2 begins before Phase 1 is "done" (Phase 1 doesn't end, it becomes background discovery against new applications). Phases do **not** map 1:1 to environment tiers: each phase progresses through Dev → Test → Prod under its own promotion gates.

| Phase | Posture | What lands | Entry gate |
|---|---|---|---|
| **Phase 1 — Discover** | Observe-only. The gateway is a passive sensor. | Route all AI traffic through the gateway to inventory it. File Log + OTel are the only "controls" — no enforcement plugins. Goal: build the risk inventory. Shadow AI usage surfaces here. | Arch sign-off + first Dev/Test/Prod control planes provisioned + first data plane deployed. |
| **Phase 2 — Pareto risk reduction** | Enforcing on the highest-risk 20% of traffic that represents 80% of the risk. | `ai-prompt-guard`, `ai-sanitizer`, `ai-rate-limiting-advanced` land on real routes. Plugin-chain reviews per route are now mandatory. AI-security team becomes load-bearing. | Phase 1 risk inventory has identified the high-risk 20%; AI-security policy authored; UAT in Test passes against shadow traffic. |
| **Phase 3 — Agentic workloads** | Enforcing on tool-use / MCP / multi-step agent traffic. Distinct from chat/completion. | Routes serving MCP servers, agent orchestrators, and tool-using LLM workflows. Long-running session handling, multi-step trace correlation, per-tool authorization. New plugin compositions and likely **net-new tooling** (MCP-aware audit, agent-session rate-limiting patterns the catalog may not cover natively as of Kong 3.x). | Phase 2 policy framework stable in Prod; agent workloads identified in the risk inventory; MCP / agent expertise on the team (see skill-gap note below). |
| **Phase 4 — Everything else** | Full governance catch-all. | Remaining workloads brought under the same plugin / observability / compliance regime. BU-liaison model (Tier 3) becomes the scaling mechanism. | Phase 3 stable; federation model designed; embedded BU champions onboarded. |

### Phase × tier interaction

Phases and team-maturity tiers are correlated but not coincident:

- **Phase 1 typically lands inside Tier 1** of the team — sole gateway engineer + consumed compliance partner, observe-only posture matches the staffing reality. Observability lift is the dominant workload — Role 3 capacity matters more than Role 5 at this phase.
- **Phase 2 begins to overlap Tier 1 → Tier 2 transition.** Enforcement on real traffic is the trigger for dedicated AI-security headcount (Role 5 moves from "consumed" to "dedicated team"). Compliance partner becomes embedded, not engaged.
- **Phase 3 demands skills the current team plan does not explicitly carry.** MCP-aware operations, agent-session observability, tool-use authorization patterns — this is a **skill gap to flag for Nolan**. Options: dedicated MCP / agent platform engineer hire, upskill an existing Role 1 senior, or contract a specialist for the Phase 3 onramp. Phase 3 entry should not depend on hoping one of the existing engineers happens to have MCP experience.
- **Phase 4 is when the BU-liaison model earns its keep.** Onboarding the long tail of workloads is exactly the volume problem federation solves.

### Phase × env-tier interaction

Every phase walks through Dev → Test → Prod:

- **Phase 1 in Dev** = synthetic / shadow traffic. **Phase 1 in Test** = stakeholder UAT on observe-only routes (validate File Log + OTel pipelines). **Phase 1 in Prod** = real DXC employee traffic, gateway-routed but unenforced. This is where the risk inventory is built.
- **Phase 2 in Dev** = plugin-chain experimentation against shadow traffic. **Phase 2 in Test** = security and load harnesses against the candidate plugin set. **Phase 2 in Prod** = enforcement on the high-risk 20%.
- Same for Phases 3 and 4. The promotion gate is identical at every phase; what changes is the policy surface being promoted.

### How to read the rest of this document

The tier sections (Tier 1 / 2 / 3) below describe team shape, FTE, and governance. The phase axis annotates each tier where the staffing implication is non-trivial. Most clearly: Tier 1 carries Phase 1 and the early-Phase-2 entry; Tier 2 is when Phase 2 hits steady state and Phase 3 begins; Tier 3 is the platform on which Phase 4 finally closes.

---

## Role taxonomy

This document uses a canonical role taxonomy spanning four categories. Roles may be carried by dedicated FTE, shared/consumed from adjacent DXC platform teams, or sourced via a governance overlay:

| #   | Role                                     | Category                                       |
| --- | ---------------------------------------- | ---------------------------------------------- |
| 1   | Kong Gateway Engineer / SRE              | Dedicated                                      |
| 2   | DevOps / GitOps Engineer                 | Dedicated                                      |
| 3   | Observability Engineer                   | Dedicated                                      |
| 4   | Platform / Kubernetes SRE                | Consumed (earmarked)                           |
| 5   | AI-Aware Security Engineer               | Dedicated (Tier 2+); Consumed (Tier 1)         |
| 6   | LLM Platform / Model Engineer            | Dedicated (Tier 2+); Consumed (Tier 1)         |
| 7   | Application / API Developers (consumers) | Consumed                                       |
| 8   | Architect / Tech Lead                    | Governance                                     |
| 9   | Compliance / Data-Privacy Officer        | Dedicated (Tier 2+); Consumed/Engaged (Tier 1) |
| 10  | Product / Program Manager                | Dedicated (Tier 2+); Fractional (Tier 1)       |
| 11  | Tier-1 NOC                               | Operations (shared service)                    |
| 12  | Tier-2 On-call Engineer                  | Operations (rotation)                          |
| 13  | BU Gateway Champion / Liaison            | Dedicated (Tier 3, embedded per BU)            |

"Consumed" means the role exists on an adjacent DXC team and the gateway practice consumes it as a service. "Consumed (earmarked)" means the platform org provides the FTE but those individuals are effectively dedicated to gateway data-plane support — they must be named and SLA-backed, not queue-shared, at Tier 2+.

---

## Konnect tenancy strategy — Tier 1 architecture decision

At DXC's scale, the single most consequential early architectural decision is how to partition Konnect tenancy. This must be resolved in Tier 1 because it constrains every naming, RBAC, and IaC pattern that follows. Two dominant options:

**Option A — Single Konnect organization with namespaced control planes per BU**

One Konnect org. Each major business unit gets its own control plane (or control-plane group) within that org, with strict RBAC: BU teams can manage routes within their control plane; only the central platform team can modify shared config (plugins, CA certs, Vault integrations, org-level rate-limit quotas). Terraform manages the org structure; decK scoped per control plane manages route-level config.

- Pros: Single billing entity, single audit trail, central policy enforcement, easier cross-BU traffic governance. Plugin catalog and PII sanitizer sidecar deployed once, shared.
- Cons: A platform-level misconfiguration (wrong Terraform plan, bad RBAC policy) can touch all BUs simultaneously. Org-level Konnect quotas must be sized for aggregate traffic. Multi-region requires deliberate data-plane placement per control plane.

**Option B — Multiple Konnect organizations, federated**

Each major BU or geography gets its own Konnect org, fully isolated. A central governance team sets standards, audits compliance, and manages a shared plugin catalog, but does not have admin access to each org.

- Pros: Blast radius is contained per org. BUs can move at their own pace. Regulatory isolation (e.g., EU-org with data-residency controls separate from US-org) is structurally enforced.
- Cons: Significantly higher operational cost — every org needs its own Terraform state, its own Konnect team/membership management, its own CI pipelines. Shared plugin upgrades must be coordinated across orgs. Vendor cost scales per org. Cross-BU traffic observability is fragmented.

**Recommendation for DXC:** Option A with strong namespacing and RBAC, with the explicit design principle that each control-plane group is operationally independent (no BU can affect another's runtime). Option B is appropriate only if a regulatory mandate hard-requires per-org isolation (e.g., a government-sector BU that cannot share a Konnect org with commercial BUs). Jesse should flag this to the architecture team as the primary Row 3 decision — it must be made before the IaC repo is scaffolded.

---

## Tier 1 — Pilot (0–6 months, first production routes for 1–2 early-adopter BUs)

**This is the on-ramp, not the destination.** Leadership should plan for Tier 2 within 12 months of Tier 1 go-live. Do not size Tier 1 as the steady state.

**Phase coverage at this tier:** Phase 1 (Discover) is the primary workload. Phase 2 (Pareto risk reduction) entry is the trigger for Tier 2 hiring — see transition criteria. Phases 3 and 4 are not in scope at Tier 1.

**Env-tier coverage at this tier:** All three environments (Dev / Test / Prod) stand up at Tier 1 go-live. Dev exists from the first IaC commit; Test exists by the first stakeholder UAT; Prod exists at first real traffic. The sole gateway engineer owns operations across all three — see on-call math for why this is unsustainable past 6 months.

**Scope:** Single region, ~10–25 AI routes, one or two early-adopter business units, architecture decisions still landing (Konnect tenancy, secrets backend, compliance posture). No 24/7 SLA — business-hours coverage plus documented best-effort after-hours.

**Compliance and regulatory note:** Even at Tier 1, AI-security and data-privacy roles are NOT optional for DXC at 130k-employee scale. If the first production routes touch EU employees, GDPR applies from day one. If those routes touch healthcare or financial-services customers, sector-specific overlays apply from day one. Engaging the AI-security function and a compliance partner as consumed-but-active roles in Tier 1 is mandatory, not a Tier 2 upgrade. The cost of retroactive compliance remediation on an enterprise-scale gateway is orders of magnitude higher than the cost of engaging these roles early.

### FTE table

| Role | FTE | Notes |
|---|---|---|
| Kong Gateway Engineer / SRE (Role 1) | 1.0 | Sole gateway owner. Owns decK YAML, Terraform Konnect tenancy, AI plugin composition, and on-call (best-effort). **Must be staff/principal level** — this person sets IaC patterns the entire future team will follow. See JD section on first-hire calibration. |
| DevOps / GitOps Engineer (Role 2) | 0.5–1.0 | May be shared from a DXC platform engineering pod in early days. Owns CI pipelines, Helm chart management, and environment promotion. `deck diff` + `terraform plan` in every PR from day one — no exceptions. |
| Architect / Tech Lead (Role 8) | 0.2 (fractional) | Part-time oversight. Sets IaC patterns, owns the Konnect tenancy decision (Option A vs B above), acts as escalation. Typically a principal IC wearing this hat. |
| AI-Aware Security Engineer (Role 5) | Consumed, active | Not a quarterly reviewer — an active participant in route onboarding, plugin-chain reviews, and threat modeling from day one. See compliance note above. Pax's Vex research brief applies here; the org chart anticipates a small dedicated team at Tier 2, not a single stretch AppSec engineer at any tier. |
| Compliance / Data-Privacy Officer (Role 9) | Consumed, active | Active from day one on any route touching regulated data. GDPR, regional data-residency mandates, and sector-specific overlays cannot be retrofitted. Engage this partner before the first production route ships. |
| Platform / Kubernetes SRE (Role 4) | Consumed (earmarked) | Provides K8s namespace, cluster access, network policies, cert-manager. Not gateway-dedicated — but must be a named contact with a defined SLA for infrastructure requests. If response time is measured in days, document this as a Tier 1 risk explicitly. |
| LLM Platform / Model Engineer (Role 6) | Consumed | Owns provider API key lifecycle and model selection. Joint runbook on key rotation with the gateway engineer. |
| Product / Program Manager (Role 10) | 0.2 (fractional) | Defines which AI routes are needed, which BUs are first adopters, and what the internal SLA commitment is. |

**Dedicated headcount: 1.5–2.0 FTE**  
**Consumed/active partners: AI security, compliance, platform SRE, LLM platform**

### What this tier can do

- Stand up **three Konnect control planes** (one per env: Dev / Test / Prod) under a single Konnect org with strong namespacing per the tenancy decision above. Self-managed data planes attach per env. Per-(env, BU) control planes are the right end state — at Tier 1 a single BU keeps that to three control planes total.
- Operate a route set of 10–25 AI routes in Phase 1 (Discover) posture with File Log + OTel from day one.
- Begin Phase 2 onboarding for the highest-risk subset of those routes (active plugins land in Dev → Test → Prod under PR gating).
- Maintain a PR-gated IaC repo with `deck diff` / `deck lint` / `terraform plan` on every PR, per env.
- Handle business-hours incidents with documented best-effort after-hours coverage.
- Satisfy GDPR and active regulatory overlays via consumed-but-active compliance and AI-security partners.

### What this tier cannot do

- Sustain 24/7 on-call without heroics (see on-call math below).
- Scale to multi-BU simultaneously without becoming a severe bottleneck.
- Absorb a major plugin upgrade concurrent with an incident.
- Support autonomous BU onboarding — every new route requires gateway-engineer involvement.

### Tier 1 → Tier 2 triggers

Move when **any two** of the following are true:

- Route count crosses 25 production AI routes.
- Consuming BU count reaches 3 or more.
- Any P1 incident is caused by single-point-of-failure (sole gateway engineer unavailable).
- On-call burnout is documented in a retrospective.
- A compliance requirement mandates 24/7 SLA.
- Traffic volume makes token-cost attribution a real budget conversation rather than a rounding error.

---

## Tier 2 — Production enterprise platform (6–18 months, multi-region, multi-BU)

**This is the real shape of the team for "all DXC AI traffic."** Tier 2 is the destination that justifies the Tier 1 investment. Leadership should expect to be here within 12 months of Tier 1 go-live, and should resource for the Tier 2 build starting at month 4 of Tier 1 (sourcing, onboarding, and knowledge transfer take time).

**Phase coverage at this tier:** Phase 2 (Pareto risk reduction) is at steady state across multi-BU traffic. **Phase 3 (Agentic workloads) entry happens during Tier 2** — this is the most consequential phase transition for hiring because MCP / agent / tool-use expertise is not in the existing role definitions. Flag for Nolan early in Tier 2 build-out: either upskill a Role 1 senior, hire a dedicated agent-platform engineer, or contract a Phase 3 onramp specialist. Phase 1 continues as background discovery against new applications and shadow AI.

**Env-tier coverage at this tier:** Dev / Test / Prod each scale to multi-region. Each control plane in the Konnect org now serves multi-region data planes; Prod control planes carry the full FinOps and chargeback observability stack. Test environments expand to carry security and load harnesses against the candidate plugin set for each new phase entry. Dev gets self-service for the broader engineering team.

**Scope:** Multi-region data planes (2–3 regions minimum for a 130k-employee global workforce), multi-BU, 24/7 on-call SLA, hundreds of AI routes, dedicated AI-security team, internal-product posture beginning, FinOps dashboards, compliance partners embedded.

### FTE table

| Role | FTE | Notes |
|---|---|---|
| Kong Gateway Engineer / SRE (Role 1) | 6–10 | Distributed across 2–3 regions for follow-the-sun on-call. See on-call math below. This is the non-negotiable floor for a credible 24/7 SLA at DXC scale. |
| DevOps / GitOps Engineer (Role 2) | 2–3 | One focused on platform automation (Helm, K8s, CI runners); one focused on config pipelines (`deck diff`, Terraform, environment promotion); one for release management and plugin test infrastructure as route count grows. |
| Observability Engineer (Role 3) | 2–3 | OTel pipelines at billions-of-spans scale, FinOps dashboards for token-usage attribution per BU and per model, anomaly detection, capacity forecasting. Dedicated — shared SRE resources cannot carry this load at Tier 2 scale. |
| AI-Aware Security Engineer (Role 5) | 3–5 | Small dedicated team, not a single engineer. Coordinate with Pax's revised Vex brief. Covers: Kong AI plugin-chain threat modeling, red-team cadence (Garak, promptfoo in CI), prompt-guard and sanitizer policy authorship, vault-reference posture, per-route compliance review, and the guardrails-bypass dossier. At DXC's scale and regulatory surface, a single AI-security engineer is under-staffed by definition. |
| LLM Platform / Model Engineer (Role 6) | 1–2 | Enterprise-scale provider procurement (volume agreements with OpenAI, Azure OpenAI, Anthropic, Bedrock, Vertex), fallback orchestration strategy, eval infrastructure for model-version upgrades. Dedicated at Tier 2 — provider key lifecycle at hundreds-of-routes scale requires owning this full-time. |
| Platform / Kubernetes SRE (Role 4) | 3–5 (earmarked) | Likely sourced from the DXC platform org but earmarked to the gateway practice. Owns data-plane cluster health across 2–3 regions, cert-manager lifecycle, network policy for CP-DP mTLS, cluster-level incident response on a defined SLA. |
| Compliance / Data-Privacy Officer (Role 9) | 1–2 | Dedicated partners embedded with the practice. GDPR (EU employees), regional data-residency mandates, sector-specific overlays for DXC healthcare/finance/government clients, EU AI Act tracking. At 130k employees across all major regulatory regimes, this is not a fractional engagement. |
| Product / Program Manager (Role 10) | 2–3 | This is an internal product with hundreds of consuming teams. Roles: route roadmap and SLA owner; consumer onboarding experience (self-service portal, route-request process, developer docs); stakeholder relationship with DXC leadership; chargeback/showback model. Not a luxury — without dedicated PM at Tier 2, the gateway team becomes a ticket queue. |
| Architect / Tech Lead (Role 8) | 1.0 | Full-time staff/principal. Drives the technical roadmap, plugin-ecosystem evaluation, Konnect version upgrade decisions, cross-practice standards, and embedded-BU liaison model design. |
| Tier-1 NOC (Role 11) | Shared service | SLA-backed 24/7 first-line triage. Multi-region NOC capacity, likely a shared DXC service. Gateway team maintains the runbook library NOC operates from. |
| Tier-2 On-call (Role 12) | Rotation from gateway engineers | Formalized PagerDuty rotation. See on-call math. |
| Application / API Developers (Role 7) | Consumers | Self-service onboarding process backed by the PM team. Route requests via portal or ticket queue. |

**Dedicated headcount: 20–32 FTE across gateway engineering, DevOps, observability, AI-security, LLM platform, compliance, and PM**  
**Consumed/earmarked: 3–5 FTE Platform/K8s SRE, Tier-1 NOC (shared service)**

### Internal-product posture (begins at Tier 2)

At multi-BU scale, the gateway team is an internal platform team. This workstream must start at Tier 2 entry, not be deferred to Tier 3:

- **Chargeback / showback model.** Token-usage attribution per BU, per application, per model. FinOps dashboards are not a luxury — at enterprise scale, AI cost is a real budget line and each BU needs visibility into their consumption. The observability team owns the dashboards; the PM team owns the chargeback methodology and BU conversations.
- **SLA tiers.** Define at minimum two internal SLA tiers: critical-path routes (24/7, <1min P1 response) and standard routes (business-hours, best-effort after-hours). BUs choose a tier; chargeback reflects it.
- **Consumer onboarding self-service.** A self-service route-request process with a defined SLA for the gateway team's review. Without this, every new AI route from every DXC application requires manual gateway-team intervention — unsustainable at hundreds of routes. The PM owns the portal; the gateway engineers own the review checklist.
- **Capacity planning cadence.** Quarterly forecast: projected route additions, traffic growth per existing route, provider cost trajectory, data-plane scaling requirements. Drives headcount and Konnect quota planning.

### What this tier can do

- Operate 2–3 region data planes with follow-the-sun 24/7 on-call.
- Route all DXC AI traffic through a single governed gateway.
- Serve hundreds of consuming teams with a self-service onboarding process.
- Run quarterly plugin upgrade cycles without production disruption.
- Enforce GDPR, regional data-residency, and sector-specific compliance per route.
- Produce accurate chargeback attribution for every AI dollar DXC spends.

### Tier 2 → Tier 3 triggers

Move when **any two** of the following are true:

- BU count exceeds 15–20 distinct consuming business units. Central onboarding becomes the bottleneck.
- Gateway engineer on-call rotation requires >6 FTE to maintain health (sustained hiring); this signals embedded BU capacity would offload route-management work better than more central hires.
- Chargeback model has active BU negotiation on SLA customization — signal that BUs want ownership, not service.
- A DXC M&A event adds a new organizational entity with a distinct compliance regime — federated model absorbs this more gracefully.

---

## Tier 3 — Mature federated platform (18+ months, fully internal-product posture)

**Phase coverage at this tier:** Phase 4 (Everything else) is where the federation model earns its keep — the long tail of DXC workloads is exactly the volume problem central teams cannot absorb. Phases 1–3 are all in steady state, with new application discovery (Phase 1) running continuously and new agentic workloads (Phase 3) onboarded by either the central agent-platform function or trained BU champions.

**Env-tier coverage at this tier:** Same three-environment model, but BU champions hold merge rights to **their BU's namespace** in each env's control plane. The IaC repo carries one decK directory per (env, BU) control plane; champions PR against their BU's directory while the central team retains review rights on shared concerns (plugin catalog updates, cross-BU policy changes). Prod across all regions is still under central on-call; Dev / Test become predominantly champion-operated for BU-specific routes.

**Scope:** Federation model. Central platform team owns the IaC repo, plugin catalog, shared plugin upgrades, and SLAs. Embedded gateway champions (liaisons) per major BU own route onboarding for their BU's applications and act as the first line of support for BU-specific route issues.

### Federation model

The central-team-only model breaks at DXC's eventual scale because:

- Route onboarding volume exceeds what a central team can review without becoming a bottleneck.
- BU-specific compliance overlays (healthcare BU, government BU) require embedded expertise that a central team cannot carry across all verticals.
- Plugin customization requests from BUs grow into a backlog that blocks the central team from roadmap work.

**The hybrid that works:** Central platform team (small, senior) owns the IaC repo, the plugin catalog, the Konnect org structure, the shared Terraform modules, and the SLA contract. Embedded gateway champions (1 per major BU, 2–3 per large BU) own route onboarding, BU-specific plugin configuration, and first-line support for their BU's routes. Champions are employed by their BU, trained and certified by the central team, and have merge rights to the BU's control-plane namespace in the IaC repo.

### FTE table

| Role | FTE | Notes |
|---|---|---|
| Central platform team — Kong Gateway Engineers (Role 1) | 6–8 | Smaller central team than Tier 2; BU liaisons absorb route-management volume. Central team focuses on platform health, Konnect upgrades, plugin catalog, and SLA management. |
| Central platform team — DevOps / GitOps (Role 2) | 2–3 | IaC repo automation, plugin test infrastructure, release management. |
| Central platform team — Observability (Role 3) | 2–3 | FinOps dashboards, anomaly detection, capacity forecasting, SLO product. |
| Central platform team — AI-Security (Role 5) | 3–5 | Dedicated red-team, guardrails-bypass dossier, plugin-chain policy authorship, OWASP LLM Top 10 / MITRE ATLAS cadence. Owns the plugin-chain review checklist BU liaisons use. |
| Central platform team — LLM Platform / Model (Role 6) | 2–3 | Provider procurement, fallback orchestration, eval infrastructure. Plugin authorship as a routine activity when gaps in the catalog exist. |
| Central platform team — Compliance (Role 9) | 2–3 | Central compliance mapping (GDPR, EU AI Act, regional data-residency, sector-specific). Per-BU overlays owned by BU compliance with central team as governance escalation. |
| Central platform team — Product / Program (Role 10) | 2–3 | Internal-product roadmap, SLA tiers, chargeback model governance, BU champion certification program. |
| Central platform team — Architect / Tech Lead (Role 8) | 1–2 | Technical roadmap, cross-BU standards, major platform decisions (Konnect major version, new plugin tier evaluation). |
| BU Gateway Champions / Liaisons (Role 13) | 10–20+ | Embedded per major BU. Own route onboarding for BU applications, BU-specific plugin configuration, first-line support. Trained and certified by the central team. Headcount varies by BU size and AI-route density. |
| Platform / K8s SRE (Role 4) | 5–8 (earmarked) | Multi-region data-plane fleet. Dedicated named contacts per region. |
| Dedicated red-team (under Role 5) | 1–2 | Explicit red-team function separate from the day-to-day AI-security review. Runs adversarial exercises on a fixed cadence, reports to the AI-security lead. |
| Tier-1 NOC (Role 11) | Shared service | Multi-region NOC with gateway-specific runbooks. |

**Total practice: 30–50+ FTE counting central team plus embedded BU liaisons**

### What this tier can do

- Operate the gateway as a true enterprise product with published SLAs, chargeback, and a developer portal.
- Absorb DXC M&A or new-BU additions without central team growth — the liaison model scales with the org.
- Run plugin authorship as a routine activity (Lua/Go PDK) when catalog gaps surface, not a last resort.
- Enforce strict SLAs with error-budget accounting and a formal improvement cycle.
- Run a dedicated red-team on the AI surface on a quarterly cadence independent of day-to-day security review.

---

## On-call math — revised for enterprise scope

A sustainable PagerDuty rotation requires a minimum of **5–6 on-call slots** to cover 24/7 without any person carrying more than one week in five. At DXC's follow-the-sun requirement (consumers in North America, Europe, and likely APAC), the math has a geographic dimension.

### Tier 1 (1 gateway engineer)

On-call every week. Unsustainable within 3–6 months. Acceptable only as a temporary measure with explicit leadership acknowledgment of the risk and a documented plan to hire Tier 2 within 12 months. This is not a baseline — it is a declared risk.

### Tier 2 minimum — 6 engineers across 2–3 regions

| Region | FTE | Coverage window |
|---|---|---|
| Americas (US-East or US-Central) | 2–3 | 08:00–20:00 ET primary; on-call after-hours |
| EMEA (UK or Continental EU) | 2 | 08:00–18:00 GMT/CET primary; on-call after-hours |
| APAC (India or APAC) | 1–2 | 08:00–18:00 IST/AEST primary; on-call after-hours |

With 6 engineers in a follow-the-sun rotation and regional primary coverage, no single engineer is on-call more than one week in six during primary hours. After-hours on-call slots are shared across the global roster with an expected escalation rate significantly lower than primary-hours incidents.

**PagerDuty math:** 1-in-6 rotation = ~8–9 on-call weeks per year per engineer. Industry standard for platform engineering is 1-in-4 as acceptable, 1-in-3 as burnout territory. 1-in-6 is comfortable. Below 4 engineers globally, the rotation degrades to 1-in-3 or worse — document this as a staffing risk on any plan that targets fewer than 6 gateway engineers at Tier 2.

**Minimum to claim a 24/7 SLA honestly:** 6 dedicated gateway engineers distributed across 2–3 regions, backed by a well-runbooked Tier-1 NOC. Below that, document the gap explicitly and obtain leadership sign-off.

### Tier 3

With 6–8 central gateway engineers plus BU liaisons covering regional escalation, the rotation becomes comfortable at 1-in-8 or better for P1/P2. Routine incidents (route config issues, rate-limit tuning requests) are handled by BU liaisons before they escalate to the central on-call rotation.

---

## Regulatory overlay — 130,000-employee multinational

At DXC's scale, the following regulatory regimes apply to AI traffic and are not optional:

- **GDPR** — applies to any AI route that processes data about EU employees or EU-based customers. Prompt content, user identifiers, and any personal data transiting the gateway are in scope. The `ai-sanitizer` PII plugin must be active and configured per GDPR's lawful-basis and data-minimisation requirements. File Log destinations must comply with GDPR's Article 30 processing records requirement. Data-residency: EU-employee AI traffic must be processed in EU-region data planes — Konnect's SaaS control plane does not see traffic, but data plane placement is an explicit architecture decision.
- **Regional data-residency mandates** — beyond GDPR, several jurisdictions (India, China, Brazil under LGPD, and others) impose data-localisation requirements that affect where data planes must be placed. The compliance team must map DXC's employee and customer geographies to these mandates and translate them into Konnect data-plane placement constraints.
- **EU AI Act** — phased implementation through 2026. GPAI provider obligations apply to model providers, not the gateway operator. However, DXC customers deploying into EU jurisdictions may inherit obligations that require DXC to evidence logging, traceability, and human-oversight capabilities — all of which the gateway provides, but only if audit logging is correctly configured from day one.
- **Sector-specific overlays** — DXC's healthcare customers (HIPAA), financial-services customers (SOC 2, PCI, sector-specific data-handling), and government clients (clearance requirements, FedRAMP, ITAR) each add requirements on top of baseline GDPR and regional mandates. The compliance team must maintain a per-customer-segment overlay matrix that the gateway team uses to configure routes serving those segments.

**Bottom line for staffing:** The AI-security and compliance roles at Tier 2 are not discretionary. Engaging them from Tier 1 as active partners (consumed but present) and scaling them to dedicated FTE at Tier 2 is mandatory at DXC's regulatory surface area.

---

## One-paragraph JD stubs

### Role 1 — Kong Gateway Engineer / SRE

Owns the Kong AI Gateway as infrastructure-as-code — Konnect control-plane provisioning via Terraform, data-plane deployment via Helm/KIC, AI plugin composition (`ai-proxy-advanced`, `ai-prompt-guard`, `ai-rate-limiting-advanced`, `ai-sanitizer`), and the decK YAML repo that keeps all three drift-free. Responds to gateway-layer incidents on-call, authors runbooks for every recurring operational procedure, and acts as the technical interface between the LLM platform team and the consuming application teams. At DXC enterprise scale, operates as part of a 6–10-engineer distributed team with follow-the-sun coverage across 2–3 regions. The first 2–3 hires must be at staff/principal level — they set IaC patterns, on-call discipline, and runbook standards that the team will follow for years. Key skills: Kong Konnect, decK, Terraform (`kong/konnect` provider), Helm/KIC, AI plugin stack, GitOps (PR-gated promotion), OpenTelemetry, vault-reference patterns. **Deep-cut JD at [[2026-05-12-kong-api-gateway-engineer-jd]].**

### Role 2 — DevOps / GitOps Engineer

Owns the CI/CD infrastructure that gates every gateway configuration change — `deck diff`, `deck lint`, and `terraform plan` on every PR; automated environment promotion from dev to staging to prod; Helm chart upgrade lifecycle for the data-plane pods; and the runner/backend plumbing (GitLab CI / GitHub Actions / Azure Pipelines, depending on Row 2 of the architecture decision). At Tier 2 scale, this team also owns plugin test infrastructure (automated regression suites against a staging Kong instance on every plugin-version upgrade) and release management for multi-region promotion. Key skills: CI runner configuration, Helm, Kubernetes, Terraform (state backend management), GitOps tooling (ArgoCD / Flux as applicable), and familiarity with decK and Kong's declarative model.

### Role 3 — Observability Engineer

Owns the full telemetry stack for the Kong AI Gateway — OTel collector configuration, Prometheus alert rules, token-usage dashboards, audit-log pipeline health, and cost-attribution reports per consumer team and per LLM provider. At enterprise scale, "billions of spans" is not hyperbole: a 130k-employee workforce generating AI traffic produces metric cardinality and log volume that requires deliberate pipeline design to remain cost-effective and actionable. Owns FinOps dashboards for chargeback attribution and capacity-forecasting models for data-plane scaling. At Tier 2+, owns the internal SLO / error-budget product. Key skills: OpenTelemetry (collector, traces, metrics), Prometheus/Grafana or equivalent, log-pipeline tooling (Loki, Splunk, Datadog — whichever DXC mandates), and AI-specific metrics fluency (token counts, semantic-cache hit rate, sanitizer redaction events, per-model latency histograms).

### Role 4 — Platform / Kubernetes SRE (consumed/earmarked)

Provides the K8s cluster layer the gateway data planes run on: namespace provisioning, network policy, certificate management (cert-manager), ingress controller baseline, and cluster-level incident response. At Tier 2+, 3–5 FTE earmarked from the DXC platform org with a named contact per region and a defined SLA (response time measured in minutes for P1/P2, not days). Multi-region means this function must have regional presence matching the gateway's data-plane deployment footprint. Key interface to the gateway team: the Helm values overlay for the `kong/ingress` chart, the network policy allowing CP-DP mTLS, and the cert-manager issuer for TLS termination.

### Role 5 — AI-Aware Security Engineer (dedicated team at Tier 2)

A 3–5 FTE dedicated team (see Pax's Vex hire research for full role profile). Threat-models the AI Gateway plugin chain per route, authors prompt-guard / sanitizer / vault-reference rules, owns the red-team posture (Garak, promptfoo in CI, manual adversarial exercises), and maintains the living guardrails-bypass dossier. At 130k-employee scale with enterprise-wide AI traffic, a single stretch AppSec engineer is structurally under-staffed. The team shape: one AI-security lead (staff-level, drives policy and architecture), 1–2 mid-level AI-security engineers (day-to-day plugin-chain reviews, red-team operations), and 1 dedicated red-teamer at Tier 3. Coordinates closely with Axle (gateway engineering) and the compliance partners.

### Role 6 — LLM Platform / Model Engineer

Owns provider API key lifecycle (provisioning, rotation, emergency revocation), enterprise-scale provider procurement (volume agreements, fallback orchestration across providers), model-version selection and version-pinning decisions, and the internal catalog of approved LLM providers. At DXC enterprise scale, this role also owns the eval infrastructure that validates a new model version against the route set before it is promoted — model-version upgrades affecting hundreds of routes require systematic regression testing, not per-route manual validation. Key skills: LLM provider APIs, IAM / workload identity patterns for secrets injection, Kong `ai-proxy-advanced` provider configuration, eval frameworks (promptfoo, or equivalent).

### Role 7 — Application / API Developers (consumers)

The consuming teams who route their AI traffic through the gateway. Their interface to the gateway team evolves from a manual runbook-backed ticket (Tier 1) to a self-service developer portal with a defined review SLA (Tier 2+) to a BU-liaison-mediated onboarding with a published route catalog (Tier 3). Key expectations at every tier: submit route requests with upstream service endpoint, required plugin stack, and expected traffic profile; follow the vault-reference pattern for application-level secrets; and engage the gateway team when a plugin behavior change affects their application.

### Role 8 — Architect / Tech Lead

Sets technical direction for the gateway practice — IaC patterns, plugin-ecosystem evaluation, decK/Terraform split of responsibilities, upgrade policy, Konnect tenancy architecture, and cross-team integration standards. At DXC enterprise scale, the architect also owns the federation model design (central team vs BU liaisons) and the internal-product posture (SLA tiers, chargeback methodology, developer portal design). Full-time staff/principal IC at Tier 2+.

### Role 9 — Compliance / Data-Privacy Officer

Owns the regulatory posture of the gateway — GDPR, regional data-residency mandates, EU AI Act tracking, sector-specific overlays (healthcare, financial, government clients). Translates regime requirements into concrete plugin configuration constraints (which `ai-sanitizer` categories active per route class, which File Log destinations and retention windows, which data-plane regions are permissible). Not a gateway-engineering role; this is a governance overlay that must be active from Tier 1 onward. At Tier 2+, 1–2 FTE dedicated and embedded with the practice.

### Role 10 — Product / Program Manager

Owns the gateway practice's internal-product roadmap, SLA commitments, chargeback model, and consumer onboarding experience. At Tier 2+, this is a 2–3 FTE function: the gateway serves hundreds of consuming teams and requires the same product management discipline as an externally-facing product. Owns the route roadmap, the developer portal design, the BU relationship for onboarding prioritization, and the stakeholder narrative for DXC leadership on AI-gateway investment and cost attribution.

### Role 11 — Tier-1 NOC

First-line triage for gateway incidents — alert acknowledgment, runbook-driven restart/failover actions, and escalation to Tier-2. Operates from a runbook library maintained by the gateway team. At Tier 2+, multi-region NOC capacity is required — a single-region NOC handling follow-the-sun incidents for a global gateway introduces a coverage gap the gateway team must document. Likely a shared DXC NOC service; the gateway team's responsibility is a gateway-specific runbook library that enables NOC operators to resolve P3/P4 incidents without escalation.

### Role 12 — Tier-2 On-call Engineer

The gateway-qualified on-call engineer who handles incidents the Tier-1 NOC cannot resolve from runbooks. At Tier 2, this rotation is drawn from the 6–10 gateway engineers distributed across regions, with the rotation designed for 1-in-6 or better on-call frequency per engineer. Requires gateway-engineer-level familiarity with decK, Konnect, the active plugin stack, and the ability to execute a cold-start runbook under pressure at 2am in any region.

### Role 13 — BU Gateway Champion / Liaison (Tier 3)

Embedded per major business unit. Owns route onboarding for the BU's applications, BU-specific plugin configuration (within the central team's policy guardrails), and first-line support for BU route issues before escalation to the central team. Trained and certified by the central platform team; holds merge rights to the BU's control-plane namespace in the IaC repo. This role is the mechanism by which the federation model scales — it is not a nice-to-have at Tier 3, it is the primary scaling mechanism.

---

## Phase transitions summary

| Transition | When to move | Headline trigger |
|---|---|---|
| Tier 1 → Tier 2 | 12 months after Tier 1 go-live (target); sooner if triggers fire | Any two of: route count >25, BU count ≥3, P1 single-point-of-failure incident, compliance SLA requirement, on-call burnout documented |
| Tier 2 → Tier 3 | 18+ months after Tier 1 go-live | Any two of: BU count >15–20, on-call rotation requires >6 FTE to maintain health, chargeback model triggers active BU ownership conversation, DXC M&A event adds distinct compliance regime |

---

## Skill gaps surfaced by the phase plan (for Nolan)

The four-phase plan exposes gaps the existing role definitions do not cleanly cover. These are flagged for Nolan to factor into the hiring sequence and JD set, not to action today.

- **Phase 3 — MCP / agent / tool-use expertise.** None of Roles 1–13 explicitly carry this. As of Kong 3.x (current minor releases), MCP-aware audit and agent-session rate-limiting are not first-class catalog plugins — composition patterns are still being established in the ecosystem. Three sourcing options have been structured and evaluated: (a) net-new senior hire with explicit MCP / agentic-workload experience, (b) upskill an existing Role 1 senior, (c) contract a Phase 3 onramp specialist — plus a hybrid of (c) + (b) in parallel. Decision needs to land 3–4 months before Phase 3 entry, not at entry. **Full options analysis: [[2026-05-13-phase-3-mcp-agent-expertise-options]].** Recommended revisit trigger: Phase 2 Prod stabilization (4 weeks clean in production).
- **Phase 1 — Observability-lift weighting.** The org chart's Role 3 (Observability Engineer) is sized for Tier 2 steady state, but Phase 1 is observability-dominant by design. Tier 1 may need to over-index on observability earlier than the FTE table reads — either by pulling Role 3 forward (start at 0.5 FTE in late Tier 1 rather than 2–3 FTE at Tier 2 entry) or by leaning hard on the consumed DXC observability team during Phase 1. Worth a Nolan-Pax conversation on Role 3 sourcing.
- **Env-tier coverage in Tier 1.** The on-call math assumes one gateway engineer covering one Prod environment. Three Konnect control planes (Dev / Test / Prod) per BU is a 3× multiplier on configuration surface area at Tier 1. The current Tier 1 FTE (1.0 Role 1, 0.5–1.0 Role 2) may be tight; the Tier 1 → Tier 2 trigger of "P1 incident caused by single-point-of-failure" is more likely to fire earlier under the three-env model than the original draft assumed.

---

## Open questions for Jesse — enterprise-scope additions

The following questions are new or upgraded in priority given the 130k-employee scope:

1. **Konnect tenancy decision (Option A vs B) — and the env-tier overlay.** This is the primary architecture constraint for everything else. The 2026-05-13 environment decision adds an env-tier dimension: the recommended Kong-native pattern is one Konnect org with one control plane per environment per BU. Status: **open — arch team to confirm** as a Row 3 sub-decision. Needs to resolve before IaC repo scaffolding.

2. **Phase-1 entry milestone.** Phase 1 begins once arch sign-off lands AND the first Dev / Test / Prod control planes are provisioned AND the first data plane is deployed. No fixed date — relative to arch decision landing. Confirm this is the correct phase-1-entry definition.

3. **Follow-the-sun geography.** Which regions does DXC's AI-traffic footprint actually cover? The on-call math above assumes North America + EMEA as the minimum. If APAC is in scope from Tier 2 (India-based DXC employees, APAC clients), the regional gateway-engineer distribution changes and a third data-plane region may be required from Tier 2 entry rather than Tier 3.

4. **Regulatory scope for early-adopter BUs.** Which business units are Tier 1 pilots, and what is their regulatory profile? An EU-employee BU or a healthcare-client-facing BU immediately activates GDPR and sector-specific compliance requirements that a domestic, commercial BU pilot does not. This drives the consumed-but-active compliance partner's day-one workload significantly.

5. **AI-security team staffing path.** Pax's revised Vex brief anticipates a 3–5 FTE dedicated team at Tier 2. The original brief considered a single stretch AppSec engineer. Jesse should confirm whether the revised brief is being actioned (net-new hire at senior level vs stretch internal candidate vs hybrid) before Tier 2 hiring begins, since the AI-security team must be onboarded alongside the second and third gateway engineers — not after them.

6. **Phase-3 expertise sourcing.** MCP / agent / tool-use experience is the explicit skill gap surfaced by the new phase plan. Decision needs to land 3–4 months before Phase 3 entry. **Full structured options analysis (three options + hybrid + decision criteria + Pax open questions): [[2026-05-13-phase-3-mcp-agent-expertise-options]].** Revisit at Phase 2 Prod stabilization.

7. **Chargeback model ownership.** Who owns the FinOps attribution model for AI spend — the gateway team, the finance function, or a shared model? This determines how the PM function is staffed and where the FinOps dashboards live operationally.

8. **Federation model timing.** The Tier 3 BU-liaison model requires a training and certification program that takes time to build. If DXC plans to be at Tier 3 within 24 months of Tier 1 go-live, the central team needs to start designing the liaison program in parallel with Tier 2 build-out. When does leadership expect the BU-liaison model to be live?

9. **DXC's standard on-call model.** Does DXC operate a centralized NOC that gateway incidents can escalate into, or does each practice own its own on-call rotation end-to-end? This determines whether Tier-1 NOC is consumed (NOC exists) or net-new headcount.

10. **Internal vs. consultant staffing preference.** Is the intent to staff the gateway team entirely with DXC employees, use a mix with SI partner consultants, or outsource operations to a managed service? The first 2–3 hires (staff/principal level) should strongly favor direct hire or long-term engagement — pattern-setting roles should not be contractor-shaped if avoidable.

11. **AI route volume forecast.** The Tier 1 → Tier 2 transition is partly triggered by route count. A rough 12-month route forecast — even a range — lets the team size Tier 2 hiring timelines. At 130k employees, even a 10% AI-tool adoption rate in the first year implies a large route and consumer-team count.

---

*Cross-references: [[2026-05-12-kong-api-gateway-engineer-jd]] (deep-cut JD for Role 1), [[2026-05-11-api-gateway-engineer-hire-research]] (Pax's role research), [[2026-05-12-ai-security-engineer-hire-research]] (Pax's AI-security team research — Vex), [[2026-05-11-dxc-arch-team-ask-kong-ai-gateway]] (architecture decisions still pending), [[PKM/My Life/Projects/kong-ai-gateway]] (project state).*
