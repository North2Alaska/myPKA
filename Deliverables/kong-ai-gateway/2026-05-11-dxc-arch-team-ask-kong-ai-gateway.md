---
type: deliverable
audience: DXC architecture team
status: draft
date: 2026-05-11
updated_at: 2026-05-13
author: Axle (API Gateway Engineer)
project: kong-ai-gateway
---

# Kong AI Gateway — Architecture Decisions Needed

**To:** DXC architecture team
**From:** Jesse Swensen (gateway owner)
**Date:** 2026-05-11 (revised 2026-05-13)
**Decision target:** unblock production scaffolding of the Kong AI Gateway IaC repo

## Changelog

- **2026-05-13** — Gateway-owner additions following two internal decisions: (a) a **three-environment tier** (Dev / Test / Prod) is locked in for all phases; (b) a **four-phase implementation plan** (Discover → Pareto risk reduction → Agentic workloads → Everything else) defines the workload scope progression. Two new sub-rows added to existing rows (1, 3, 5) and three new architecture questions appended (Rows 8, 9, 10). Recommendations carry the gateway-engineering view per Kong's published patterns; final decisions are the arch team's.
- **2026-05-11** — Initial draft. Seven decision rows (data-plane deploy target, IaC host, Konnect tenancy, AI plugin set, secrets backend, compliance posture, observability sinks).

## Context

DXC has assigned us to stand up a Kong AI Gateway with a code-first operating model — declarative state in a versioned IaC repo as the single source of truth, the Konnect console reserved for read-only triage. The proof of concept validated **Kong Konnect (SaaS control plane) with self-managed data planes** as the deployment shape. That outcome is settled; what is not settled is the surrounding architecture — where the data planes run, where the IaC repo lives, which secrets backend Kong references, and how compliance is enforced on the AI routes themselves.

This document lists every decision still gating production work, with the realistic options, the tradeoffs that matter specifically for an AI gateway, and a recommended option where the gateway-engineering view supports one. Decisions on rows 1, 2, 3, and 5 are the minimum needed to move the project from `planning` to `active`. Rows 4 and 6 can iterate as plugin scope firms up. Rows 8, 9, and 10 (added 2026-05-13) are sub-questions raised by the new env-tier and phase plan.

## Two foundational decisions the gateway owner has locked in (context for the arch team)

Two decisions landed internally on 2026-05-13 that the arch team should treat as given context, not as questions to re-litigate:

1. **Three environment tiers: Dev / Test / Prod.** Standard promotion gates between them. Every workload at every phase walks through Dev → Test → Prod under PR-gated promotion via the IaC repo. No traffic flow between tiers; no shared provider keys across tiers.

2. **Four-phase implementation plan** — sequential by entry-gate, overlapping in execution:
   - **Phase 1 — Discover:** All AI traffic routed through the gateway in observe-only posture. File Log + OTel only. Goal: build the risk inventory; surface shadow AI.
   - **Phase 2 — Pareto risk reduction:** Enforcement plugins (`ai-prompt-guard`, `ai-sanitizer`, `ai-rate-limiting-advanced`) land on the highest-risk 20% of traffic representing 80% of risk.
   - **Phase 3 — Agentic workloads:** MCP servers, agent orchestrators, multi-step tool-use traffic. Distinct plugin compositions and likely net-new tooling beyond the current Kong 3.x catalog.
   - **Phase 4 — Everything else:** Catch-all governance for remaining workloads under the federation model.

Phases progress through Dev → Test → Prod independently of which phase the team is in; the two are orthogonal axes. The plugin-policy change from Phase 1 (observe) to Phase 2 (enforce) on a given route is a decK YAML diff promoted through the standard env-tier gates — not a re-architecture, not new infrastructure.

What this means for the arch team: where the rows below have an env-tier or phase implication, this is called out under the row. Rows 8, 9, and 10 below capture the new arch questions that fall out of these two decisions.

## Decisions needed

### Row 1 — Data-plane deploy target

| Option | Tradeoffs (AI-gateway specific) |
|---|---|
| **Kubernetes (KIC + Helm)** | Best GitOps fit: cluster state is declarative, drift from out-of-band changes is structurally prevented. Horizontal autoscaling absorbs token-burst workloads cleanly. Native OpenTelemetry sidecar / DaemonSet patterns. Highest operational maturity on the Kong side (the `kong/ingress` chart is Kong's recommended path for new installs). Requires DXC platform-team K8s namespace + ingress. |
| **Docker / docker-compose** | Fast to stand up, fine for a single-node dev or a small footprint. Weak story for HA, rolling upgrades, and bursty AI workloads. Acceptable for dev; not recommended for prod. |
| **VM / systemd** | Documented and viable. Highest ops cost — patching, HA, service supervision are on us. Only justified when K8s is structurally unavailable or compliance forbids container orchestration. |

**Recommendation: Kubernetes on a DXC platform-team-managed cluster** (AKS / EKS / GKE / on-prem — whichever the platform team already runs). Reasons specific to an AI gateway: (a) token-burst workloads need horizontal scaling that is trivial in K8s and painful elsewhere; (b) the observability story (OTel collector, Prometheus, File Log shipping) is mature on K8s and bespoke on VMs; (c) Kong's own recommended install path for new deployments is the `kong/ingress` Helm chart.

**Blocked on:** which K8s cluster the platform team will host us in, and which namespace/team owns ingress on it.

**Sub-question (added 2026-05-13) — K8s scope per environment.** Two viable shapes for the Dev / Test / Prod tiers:

| Sub-option | Tradeoffs |
|---|---|
| **Namespace-per-env on a shared cluster** (`kong-system-dev`, `kong-system-test`, `kong-system-prod` on one DXC cluster) | Cheaper. Requires strict NetworkPolicy to enforce no-cross-env traffic. Acceptable if the platform team already runs a single multi-tenant cluster and treats namespace isolation as production-grade. Helm values overlay structures the env differences. |
| **Cluster-per-env** (`dxc-dev`, `dxc-test`, `dxc-prod`) | Stronger isolation. Standard at many DXC scopes. Recommended for Prod once it goes multi-region at Tier 2. Higher operational cost — three cluster lifecycles to manage. |

**Recommendation:** follow whichever pattern the DXC platform team already standardizes on. If the platform team has no opinion, prefer **cluster-per-env** for Prod and Test, namespace-per-env acceptable for Dev — the marginal cost of an extra cluster is small relative to the blast-radius reduction at the Prod tier. Either shape is compatible with the View 0 multi-environment topology in the companion diagram doc.

---

### Row 2 — IaC repo host

| Option | Tradeoffs |
|---|---|
| **GitLab (self-hosted or SaaS)** | Strong CI runners out of the box; Terraform state backend native in GitLab; mature merge-request review. |
| **GitHub Enterprise** | Strong ecosystem, broad familiarity, GitHub Actions for CI. Terraform state needs an external backend (Azure Storage / S3 / GCS). |
| **Azure DevOps** | Native if DXC is already Azure-centric; pipelines are mature; tight Azure Key Vault integration if that ends up being the secrets backend. |

**Recommendation: whichever host the DXC platform team already mandates for infrastructure repos** — the gateway IaC repo should not be the exception that introduces a new host. The CI runner shape, the Terraform state backend, and the secrets-injection pattern all follow from this choice, so picking the host the platform team already operates removes three downstream decisions.

**Blocked on:** the DXC-mandated host for infrastructure IaC.

---

### Row 3 — Konnect tenancy

| Option | Tradeoffs |
|---|---|
| **Reuse POC org, promote to prod** | Zero migration cost. Risk: POC-era teams, control planes, and runtime groups carry forward as tech debt. Terraform import of existing resources is doable but tedious. |
| **Fresh production tenant** | Clean RBAC, clean control-plane naming, clean team model. Terraform-provisioned from day one. Cost: re-provision everything and decommission the POC org once parity is reached. |

**Recommendation: fresh production tenant**, with the POC org kept alive read-only for a defined transition window. Reason: the team/role model and control-plane naming convention will be referenced in every PR for the life of the gateway; starting clean is cheap now and expensive later. We will Terraform-provision the org, control planes, teams, and team-membership from a single module set so the structure is auditable.

**Open sub-question for the arch team:** what is the team/role model? Specifically — does a single "gateway-engineers" team hold admin on all environments, or do we want per-environment teams (dev-admin, staging-admin, prod-admin with separation of duties)? Given the env-tier lock-in (see "Two foundational decisions" above), the gateway-engineering recommendation now leans toward **per-environment Konnect teams** — dev-admin, test-admin, prod-admin — with separation of duties on Prod merges. This matches how the IaC repo will be PR-gated.

**Sub-question (added 2026-05-13) — Konnect environment model.** Now that Dev / Test / Prod is locked, the Konnect-native pattern for three environments is the question:

| Sub-option | Tradeoffs |
|---|---|
| **One Konnect org, three control planes** (one per env) | Kong's recommended pattern for multi-environment Konnect (modern replacement for "Runtime Groups"). Single billing, single audit trail, central RBAC, shared plugin catalog. Cross-env isolation is real because each control plane only configures its attached data planes. Composes with the per-BU control-plane recommendation (Tier 1) — IaC repo carries one control plane per (env, BU). |
| **Three Konnect orgs, one per env** | Highest blast-radius isolation. Highest operational cost — three Terraform states, three RBAC perimeters, three sets of plugin-version upgrades to coordinate. Vendor cost scales per org. Not justified for env separation alone. |
| **Tagged data plane groups within a single control plane** | Cheapest. Lowest isolation. A config mistake in a Dev tag propagates to data planes attached to the same control plane. Not acceptable at DXC's regulatory surface — rejected. |

**Recommendation:** one Konnect org, three control planes (Dev / Test / Prod). This is Kong's published pattern and matches View 0 in the companion diagram doc. **Status: open — arch team confirms.**

---

### Row 4 — In-scope AI plugins for v1

The Kong AI Gateway plugin catalog has roughly seven plugins we would actually use. The question is how many to commit to in the v1 production cut.

| Scope | Plugin set | When this is the right call |
|---|---|---|
| **Minimum viable** | `ai-proxy-advanced`, `ai-prompt-guard`, `ai-rate-limiting-advanced`, File Log, OpenTelemetry | We ship one multi-provider AI route with policy, rate limiting, and full audit/observability. Smallest surface to operate and to debug. |
| **Recommended v1** | Minimum viable **plus** `ai-sanitizer` (request + response) | Adds PII redaction on prompts and responses. Required if any prompt content is regulated. The PII Sanitizer runs as a self-hostable sidecar, so prompt content does not leave the regulated environment. |
| **Maximalist** | Recommended v1 **plus** `ai-semantic-cache`, `ai-request-transformer`, `ai-response-transformer` | Adds vector-similarity caching (Redis-backed) and LLM-mediated body transforms. Real value when traffic volume justifies the cache, and when upstream services want shape-shifting handled at the gateway. Larger surface to operate; more plugin-version pinning to track. |

**Recommendation: Recommended v1 set.** Reason: the AI-gateway value proposition for a regulated DXC environment is "policy + audit + PII handling on every prompt/response," not caching or transforms. `ai-semantic-cache` and the transformers are good v2 additions once traffic patterns are observed; adding them on day one inflates the operational surface before we have data to tune them. Every route stacks File Log + OTel from day one — that is a hard requirement of the operating contract, not a plugin choice.

**Open sub-question:** is PII Sanitizer in scope for v1, or deferred to v2? This depends on Row 6 (compliance posture) — if any AI route handles regulated data on day one, Sanitizer is non-negotiable for v1.

**Pinning note:** every plugin version we adopt gets pinned to an exact 3.x minor release. Kong AI plugin behavior moves meaningfully across minor releases (3.8 → 3.10 → 3.14 each shipped non-trivial AI-plugin changes). We do not pin to `latest` under any circumstance.

---

### Row 5 — Secrets backend

Provider API keys (OpenAI, Anthropic, Azure OpenAI, Bedrock, etc.) flow through Kong vault references at runtime. They never appear in decK YAML, Terraform state, or any committed file. The decision is **which vault** Kong references.

| Option | Tradeoffs |
|---|---|
| **HashiCorp Vault** | Vendor-neutral; mature Kong integration; works the same across clouds. Requires DXC to operate a Vault cluster (or already operate one). |
| **AWS Secrets Manager** | Native if DXC's AI workloads sit in AWS; tight IAM integration with EKS service accounts. |
| **Azure Key Vault** | Native if DXC is Azure-centric (and likely the right answer if Row 2 lands on Azure DevOps). Tight integration with AKS workload identity. |
| **GCP Secret Manager** | Native if DXC's gateway-adjacent workloads are on GCP. Less likely default in our context. |

**Recommendation: blocked on DXC mandate.** This is not a gateway-engineering choice — DXC has a secrets-management standard, and the gateway must conform to it rather than introduce a fourth backend. What we need from the arch team: name the backend, and confirm the access pattern (workload identity vs static service-principal credential) the platform team supports for it. Once named, the Kong vault reference shape follows mechanically.

**Sub-question (added 2026-05-13) — env-scoped secret paths.** Whatever backend lands, the gateway will reference provider keys at scoped paths per environment: `vault://dev/<provider>-key`, `vault://test/<provider>-key`, `vault://prod/<provider>-key`. No cross-env key reuse, no shared production keys in Dev or Test. Each env's data planes authenticate to the backend with workload identities scoped to that env. Confirm the chosen backend supports per-env scoping at the access-pattern level (most do — Vault namespaces / AWS SM resource policies / Azure KV per-vault scoping all work). Dev typically uses sandbox provider credentials; Test uses staging credentials where the provider supports them; Prod uses production keys with strict rotation cadence.

---

### Row 6 — Compliance posture

This row is the most arch-team-specific. Three concrete sub-questions, all of which shape plugin composition:

1. **Data residency.** Konnect (the control plane) is SaaS and US-hosted; data planes are self-managed in whichever region we choose. Which region(s) must the data planes run in? Any prohibition on Konnect SaaS itself for regulated traffic? (If Konnect SaaS is disallowed for a given workload class, the architecture shifts to Kong Gateway Enterprise self-hosted, which is a materially different operating model.)
2. **Audit logging destination.** File Log writes structured audit events per AI request — where do those events ship? Options below in Row 7.
3. **PII handling on prompts and responses.** Is `ai-sanitizer` required for v1 (Row 4), and which PII categories matter (the plugin supports 20+ categories across 12 languages — we will configure the active set per route).

**Recommendation: blocked on DXC compliance / data-protection input.** Specifically we need: (a) approved data-residency region(s); (b) confirmation that Konnect SaaS as a control plane is acceptable for the intended workload classes; (c) the PII category list that must be redacted on regulated routes.

---

### Row 7 — Observability sinks

File Log + OpenTelemetry are non-negotiable on every AI route per the gateway-engineering operating contract — the question for the arch team is **where they ship to**, not whether they exist.

| Sink option | Tradeoffs |
|---|---|
| **Datadog** | Strong AI/LLM dashboards out of the box; expensive at AI-traffic volumes (token counts amplify cardinality). |
| **Splunk** | Common in DXC environments; strong audit-grade retention; LLM-specific dashboards are bespoke. |
| **DXC-internal Grafana / Prometheus / Loki stack** | Lowest marginal cost; full control; requires DXC-internal platform support. |
| **Azure Monitor / Application Insights** | Native if Azure-centric; tight Key Vault / AAD integration. |

**Recommendation: ship to the DXC-mandated observability stack.** Again, not a gateway-engineering choice — the gateway emits OTel and File Log in standard formats; the collector forwards to whatever sink DXC operates. Naming the sink lets us configure the OTel collector on day one rather than back-filling later.

**Blocked on:** the DXC observability stack of record for infrastructure-tier services.

**Sub-question (added 2026-05-13) — per-env retention and dashboards.** Prod telemetry feeds FinOps dashboards and chargeback attribution. Dev / Test telemetry is engineering-debug only and short-retention. Confirm that the chosen sink supports per-env retention policies (most do) and that DXC's cost model for the sink scales with the Prod tier without making Dev / Test telemetry expensive (a real concern at AI-traffic volumes).

---

### Row 8 — Network segmentation between environments (new 2026-05-13)

The env-tier lock-in (Dev / Test / Prod) requires hard isolation between tiers — no traffic flow from a Test data plane to a Prod upstream or vice versa, no shared provider keys across tiers (covered in Row 5 sub-question), no cross-tier service mesh reachability.

| Option | Tradeoffs |
|---|---|
| **NetworkPolicy at the namespace boundary** (assumes namespace-per-env K8s, Row 1 sub-question) | Native K8s. Cheap. Effective if the platform team operates NetworkPolicy strictly. Less defensible audit story than infrastructure-level isolation. |
| **Separate VPCs / VNets per env with explicit firewall rules** (assumes cluster-per-env K8s) | Strongest isolation. Standard at many DXC scopes. Higher operational cost; required if the env tier crosses regulatory boundaries (e.g., Prod processes regulated traffic, Test does not). |
| **DXC's existing network-segmentation pattern**, whatever it is | The gateway team's preferred answer — consume the platform team's standard, do not invent one. |

**Recommendation:** consume the DXC platform team's existing pattern. The gateway team does not own network architecture; we conform to it. The arch team should confirm which pattern applies and whether the env-tier requirement (no cross-env flow) is met by the default or requires gateway-specific NetworkPolicy / firewall rules.

**Blocked on:** confirmation of the DXC network-segmentation pattern for env tiers.

---

### Row 9 — Phase-gated traffic routing pattern (new 2026-05-13)

The four-phase plan (Discover → Pareto risk reduction → Agentic workloads → Everything else) defines what AI traffic the gateway carries and what governance posture applies. The arch team should know how Phase 1 (observe-only) transitions to Phase 2 (enforcing) on a given route **without re-architecting**.

| Option | Tradeoffs |
|---|---|
| **decK YAML deltas on existing routes** (recommended) | Phase 1 routes carry File Log + OTel only. Phase 2 routes get enforcement plugins (`ai-prompt-guard`, `ai-sanitizer`, `ai-rate-limiting-advanced`) appended via a decK YAML diff promoted Dev → Test → Prod. Same routes, same data plane, same provider keys — only the plugin chain changes. No new infrastructure. |
| **Separate routes per phase** | Phase 1 and Phase 2 traffic flow through distinct routes; consumers re-target on phase entry. Higher consumer-side friction; no compelling benefit over the YAML-delta approach. Rejected. |
| **Separate data planes per phase** | Phase 1 and Phase 2 traffic flow through distinct data planes with distinct plugin policies. Doubles operational footprint for no clear isolation benefit (env tier already provides isolation). Rejected. |

**Recommendation:** decK YAML deltas on existing routes. The phase transition is a config-driven event, not an infrastructure event. This is the design property that makes the four-phase plan operationally cheap. The arch team should confirm this is the intended shape — there is no architectural decision blocked here, but the assumption matters because it shapes the IaC repo layout (one decK directory per (env, BU) control plane; phase is a property of the plugin chain on each route, not a separate axis).

**Blocked on:** acknowledgment, not decision. Flag for the arch team so they understand the phase plan is a workload axis implemented via configuration, not a tenancy axis implemented via separate infrastructure.

---

### Row 10 — Phase-3 (agentic workload) Konnect catalog gaps (new 2026-05-13)

Phase 3 (Agentic workloads — MCP servers, agent orchestrators, multi-step tool-use traffic) is in scope for the gateway program. As of Kong 3.x (current minor releases), the AI plugin catalog does not have first-class plugins for MCP-aware audit, agent-session rate-limiting, or per-tool authorization. Composition patterns are still being established in the ecosystem.

| Option | Tradeoffs |
|---|---|
| **Compose existing plugins where possible, write minimal custom Lua/Go for the gaps** | Per Axle's operating contract, compose-first / write-last. Custom plugins are the last resort. Phase 3 entry likely requires at least one custom plugin for MCP-aware audit or agent-session correlation. Acceptable; the plugin scaffold is part of the IaC repo from day one. |
| **Defer Phase 3 entry until Kong catalog matures** | Vendor-controlled timeline. Not acceptable if DXC's agent workloads exist before then. |
| **Procure / partner with a third-party tool for the MCP / agent layer** | Adds a second control plane the gateway must integrate with. Larger architecture surface. Worth considering only if the catalog gap is wide enough that custom plugin authorship is more expensive than integration. |

**Recommendation for Phase 3 onramp:** budget for **1–2 custom Kong plugins** (Lua or Go PDK) for MCP-aware audit and agent-session rate-limiting, written by the gateway team during Tier 2 in preparation for Phase 3 entry. The recommendation is preliminary — Pax should be dispatched closer to Phase 3 entry to refresh the catalog state and confirm whether the gaps still exist by then.

**Blocked on:** no immediate arch-team decision. Flag for awareness so that when Phase 3 entry is on the horizon, the arch team understands plugin authorship — not just plugin composition — is a planned activity, requiring a small budget allowance and plugin-authoring expertise on the team (see "Skill gaps" in the org-chart deliverable).

---

## Already decided / out of scope

To save arch-team cycles, the following are settled and not up for re-litigation in this round:

- **Konnect SaaS as the control plane**, with self-managed data planes — validated by the POC.
- **Declarative-first operating model.** decK YAML for Konnect gateway entities (services, routes, plugins, consumers); Terraform for Konnect tenancy (orgs, control planes, teams, runtime groups). One repo, two pipelines split by responsibility.
- **No hand-edits to the Konnect UI or Admin API in production.** decK detects drift; the repo is canonical. Read-only triage in the UI is fine.
- **Pinned plugin versions, never `latest`.** Kong 3.x release cadence is fast and plugin behavior moves across minor versions.
- **Vault references for all provider API keys.** No secrets in YAML, Terraform state, or committed files.
- **File Log + OpenTelemetry on every AI route from day one.** Not deferred, not optional.

## What we need from the arch team to start scaffolding

To move the project status from `planning` to `active`, we need decisions on the following — in priority order:

- **Row 1** — data-plane deploy target and the K8s cluster / namespace if K8s. **2026-05-13 sub-question:** namespace-per-env on a shared cluster vs cluster-per-env (Dev / Test / Prod).
- **Row 2** — IaC repo host (GitLab / GHE / Azure DevOps).
- **Row 3** — Konnect tenancy strategy (fresh tenant strongly recommended) and the team/role model. **2026-05-13 sub-question:** Konnect environment model — recommendation is one Konnect org with three control planes (one per env).
- **Row 5** — secrets backend name + supported access pattern. **2026-05-13 sub-question:** confirmation of per-env scoping support.
- **Row 8** (new) — network segmentation pattern between Dev / Test / Prod tiers.

The following can iterate after `active`:

- **Row 4** — final plugin scope for v1 (we recommend the "Recommended v1" set above; happy to adjust based on Row 6).
- **Row 6** — compliance posture (data residency, audit destination, PII categories).
- **Row 7** — observability sink. **2026-05-13 sub-question:** per-env retention and cost shape.
- **Row 9** (new) — phase-gated traffic routing pattern (acknowledgment, not decision).
- **Row 10** (new) — Phase 3 agentic-workload catalog gaps (awareness, not immediate decision).

Once rows 1, 2, 3, 5, and 8 land, the first scaffolding PR will produce: the Terraform module set for Konnect tenancy (org + three control planes per env, plus per-BU namespacing), the decK directory layout per (env, BU) control plane, the Helm values overlay structure per env (assuming K8s), and the CI gating (`deck diff`, `deck lint`, `terraform plan`) on every PR with Dev → Test → Prod promotion gates.

---

<!-- Internal references (for Jesse / Larry / Axle, not the arch team) -->
<!--
- Project: PKM/My Life/Projects/kong-ai-gateway.md
- Hire research (Pax): Deliverables/2026-05-11-api-gateway-engineer-hire-research.md
- Operating contract: Team/Axle - API Gateway Engineer/AGENTS.md
-->
