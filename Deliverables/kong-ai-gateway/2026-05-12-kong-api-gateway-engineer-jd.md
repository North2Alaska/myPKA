---
type: deliverable
audience: DXC hiring managers, interview panels
status: draft
date: 2026-05-12
updated_at: 2026-05-12
author: Nolan (HR)
project: kong-ai-gateway
---

# Job Description — Kong API Gateway Engineer

**Organization:** DXC Technology — AI Gateway Practice  
**Date:** 2026-05-12 (revised same day)  
**Status:** Draft for Jesse's review before submission to DXC HR  
**Companion doc:** [[2026-05-12-kong-ai-gateway-team-org-chart]] (org-chart context, on-call math, enterprise-scope staffing model)

> **Scope revision note (2026-05-12):** The scope has been updated from "first production rollout" to enterprise-platform scale: routing ALL AI traffic from ALL 130,000 DXC employees and DXC-developed applications through a single governed gateway. Two additions to the JD follow from this: (1) the first 2–3 hires must be at staff/principal level to set patterns the rest of the team will follow; (2) multi-region awareness and follow-the-sun on-call readiness are required, not preferred. The core role competencies are unchanged — the bar is simply higher and the context is enterprise-platform, not project-team.

---

## Role title and one-line summary

**Kong API Gateway Engineer** — Owns the Kong AI Gateway as infrastructure-as-code: Konnect control-plane provisioning, self-managed data-plane operations, AI plugin composition, and the GitOps repo that keeps all three in verified, auditable, drift-free state. At DXC, this gateway routes ALL AI traffic from every employee and application — not a project, a platform.

---

## A note on the first 2–3 hires

The initial hires for this role are not joining a team — they are founding one. The first 2–3 Kong Gateway Engineers will set the IaC patterns, decK repo structure, on-call runbook discipline, CI gating standards, and plugin-chain conventions that every subsequent hire will inherit and extend. These patterns are extremely durable: a repo structure laid down by the first engineer will still be in use three years later.

For this reason, the first 2–3 hires should be sourced and calibrated at the **staff or principal IC level**, even if the role title is "Senior Engineer" in DXC's leveling framework. The first hire is effectively a tech lead — they make the foundational decisions, and those decisions compound. A strong senior engineer who cannot operate independently at pattern-setting scope is a mis-hire for the first three seats, even if they would be an excellent fourth or fifth hire.

Concretely: the first hire should be someone who has operated a production Kong AI Gateway (not a standard Kong proxy, but the AI plugin stack) at enterprise scale, has strong opinions about IaC discipline, and has set standards for a team before. The interview rubric (below) is calibrated for this bar. For hires 4+, the bar on independent pattern-setting can relax; the patterns will already exist.

---

## Reports to / works closely with

**Reports to:** Platform Engineering Lead or Practice Lead, AI Gateway (exact title TBD with DXC HR — likely a Director or Principal Architect depending on DXC's leveling framework).

**Works closely with (Tier 2 org shape — the real team context):**

- **Kong Gateway Engineer peers** (5–9 teammates across 2–3 regions) — distributed team. Follow-the-sun on-call rotation. Regional teammates handle primary-hours coverage; global teammates carry on-call for the other regions' after-hours window on rotation.
- **DevOps / GitOps Engineers** (2–3 teammates) — joint owners of the CI/CD pipeline, plugin test infrastructure, and release management. Gateway engineer owns the config layer; DevOps engineer owns the runner, promotion infrastructure, and release automation.
- **Observability Engineers** (2–3 teammates) — gateway engineer wires OpenTelemetry and File Log on every route; Observability team owns the collector, dashboards, token-usage FinOps attribution, anomaly detection, and alert rules downstream.
- **AI-Aware Security Engineering team** (3–5 FTE dedicated team) — threat-models the plugin chain per route, authors prompt-guard / sanitizer policy, owns the red-team posture. Gateway engineer authors the declarative plugin chain; AI-security team authors the rules inside those plugins. These two functions share PR review on every new or modified AI route.
- **LLM Platform / Model Engineers** (1–2 teammates) — own provider API key lifecycle, enterprise procurement, and model catalog. Joint runbook on key rotation and model-version promotion.
- **Platform / Kubernetes SRE** (3–5 FTE earmarked from DXC platform org) — provides the K8s cluster layer the data planes run on. Named contacts per region with defined P1/P2 SLAs.
- **Compliance / Data-Privacy Officers** (1–2 FTE embedded) — set regulatory constraints (GDPR, regional data-residency, sector-specific overlays) that the gateway team translates into plugin configuration. Active from day one on any route touching regulated data.
- **Product / Program Managers** (2–3 FTE) — own the route roadmap, consumer onboarding experience, and chargeback model. Gateway engineers are consumers of PM prioritization and contributors to the route-request review process.
- **Architect / Tech Lead** (1 FTE) — sets technical direction; gateway engineer escalates architectural judgment calls and brings plugin-ecosystem evaluations to this relationship.
- **Application / API Developer teams** (consumers) — the internal customers whose AI traffic routes through the gateway. Gateway engineer is their technical interface for onboarding, troubleshooting, and plugin policy. At Tier 2 scale, this interface is mediated by a self-service process and runbook library; gateway engineers are not bottlenecked on individual route requests.

In Tier 1 (the on-ramp), the first 1–2 gateway engineers carry most of the above relationships directly. The expanded team described here is the Tier 2 shape that leadership should resource within 12 months of Tier 1 go-live.

---

## Day-in-the-life — four representative scenarios

### Scenario A: Onboarding a new AI route for a consuming team

A DXC internal application team submits a route request for a new GPT-4o endpoint routed through the gateway with PII redaction and rate limiting. The gateway engineer reviews the request against the approved plugin catalog, drafts the decK YAML delta (`services`, `routes`, and a plugin stack of `ai-proxy-advanced` → `ai-sanitizer` (request) → `ai-prompt-guard` → `ai-rate-limiting-advanced` → `ai-sanitizer` (response) → File Log + OTel), runs `deck diff` locally to confirm the changeset, opens a PR, and shepherds it through CI (`deck lint`, `deck diff` in the pipeline, AI-security team review of the plugin-rule set, peer review). On merge to staging, watches the OTel traces to confirm token flows and redaction events appear correctly before promoting to prod. Writes or updates the route's runbook entry with the consuming team's escalation contact and adds a note to the chargeback attribution configuration.

### Scenario B: Responding to a `deck diff` drift alert

CI fires a drift alert: `deck diff` against the production Konnect control plane returns a non-empty diff — someone applied a change outside the repo. The gateway engineer triages: was it an emergency hotfix? An unauthorized console edit? They identify the out-of-band change, trace it to its source (Konnect audit log or a recent incident ticket), reconcile the repo to match intent (not the other way around), and open a PR to bring the state back in sync. If the change was unauthorized, they file a security incident with the AI-security team. Either way, the repo is canonical within the hour, and the incident is logged with a root-cause note. This is a P2 by operating contract — non-empty `deck diff` in production is never acceptable to leave overnight.

### Scenario C: Rotating a provider API key without dropped traffic across three regions

The LLM Platform team notifies the gateway engineer that an OpenAI API key is being rotated. At DXC's multi-region scale, this runbook runs in three regions with traffic active in at least one at any given moment. The gateway engineer follows the key-rotation runbook: (1) provisions the new key in the DXC-mandated secrets backend across all regions, (2) updates the Kong vault-reference path in decK YAML, (3) runs `deck diff` to confirm only the vault reference changes, (4) merges and promotes through staging with a traffic sample across all active regions to confirm auth succeeds with the new key, (5) promotes to prod across regions in a rolling window that avoids simultaneous promotion, (6) revokes the old key after confirmation in all regions. Zero dropped requests globally. Runbook gets a "last executed" timestamp update and a note on any regional variation in the procedure.

### Scenario D: Triaging a plugin version regression after a Kong minor upgrade

A Kong 3.x minor version upgrade to the data plane introduces a behavioral change in `ai-rate-limiting-advanced` — the token-counting window semantics shifted. A consuming team reports that their rate limits are firing at incorrect thresholds. The gateway engineer reproduces in the staging environment, confirms the plugin behavior delta against the Kong 3.x changelog and the plugin's pinned version in decK YAML, opens a bug report with Kong Support, and drafts a workaround (temporary window-size adjustment in the plugin config) that can be merged and promoted the same day. At multi-region scale, the workaround must be promoted across all data-plane regions before the consuming team's global user base is affected — the gateway engineer coordinates with regional peers to confirm each promotion completes before the next. Documents the regression in a known-issues note attached to the upgrade ticket. This scenario illustrates why Kong 3.x minor versions are never promoted without a staged rollout and why `latest` pinning is a hard disqualifier (see anti-patterns below).

---

## Required skills

### Kong / Konnect

Deep working knowledge of Kong Konnect as a declarative API, not a console. Understands the CP/DP architecture (Konnect SaaS control plane, self-managed data planes communicating over mTLS), the distinction between Konnect-managed and Admin-API-managed entities, and the operational implications of each ([Kong docs — Hybrid mode](https://developer.konghq.com/gateway/hybrid-mode/), [CP/DP communication](https://developer.konghq.com/gateway/cp-dp-communication/)). Comfortable navigating the Konnect API directly and treating the UI as read-only triage. At enterprise scale: experience with Konnect's RBAC model and control-plane namespacing sufficient to design a multi-BU tenancy structure.

### decK

Production-grade experience with `deck sync`, `deck diff`, `deck dump`, `deck lint`, and `deck ping`. Understands the decK state model — services, routes, plugins, consumers, upstreams, vaults, consumer groups — and knows which entities are Terraform's responsibility vs decK's. Has wired decK into a CI pipeline as the primary config-push and drift-detection mechanism ([decK GitHub](https://github.com/Kong/deck), [Kong docs — Manage Control Plane Configuration with decK](https://docs.konghq.com/konnect/gateway-manager/declarative-config/)). Pax's brief (§2) identifies PR-gated `deck diff` as the single clearest marker distinguishing a world-class gateway engineer from an adequate one — every production change goes through a pipeline, every time.

### Terraform Konnect provider

Hands-on experience with the `Kong/konnect` Terraform provider for org-level resource management: control planes, teams, team-membership, runtime groups, and (if used) Dedicated Cloud Gateways ([Terraform Registry — Kong/konnect](https://registry.terraform.io/providers/Kong/konnect/latest), [terraform-provider-konnect GitHub](https://github.com/Kong/terraform-provider-konnect)). Understands the split-pipeline discipline: Terraform handles tenancy, decK handles gateway entities — never merge the two into a single mega-pipeline ([Kong blog — Managing Kong with Terraform](https://konghq.com/blog/product-releases/managing-kong-with-terraform)). At enterprise scale: experience managing Terraform state across multiple environments and regions without state-file collisions.

### KIC / Helm / Kustomize

Hands-on experience deploying Kong data planes on Kubernetes using the `kong/ingress` Helm chart (Kong's recommended path for new K8s installs) or the `kong/kong` building-block chart for hybrid mode ([Kong/charts README](https://github.com/Kong/charts/blob/main/charts/kong/README.md), [Kong docs — Install on-prem with Helm](https://developer.konghq.com/gateway/install/kubernetes/on-prem/)). Familiar with Kustomize overlays as an alternative or supplement to Helm values. Understands the Kong Ingress Controller CRD model and the declarative resource lifecycle in a GitOps context. At multi-region scale: experience managing per-region Helm values overlays with a shared base configuration.

### AI plugin composition

Production experience composing Kong AI plugins per route. The canonical DXC regulated-route stack (drawn from Pax's brief §3c): `ai-sanitizer` (request) → `ai-prompt-guard` → `ai-proxy-advanced` (multi-provider, load-balanced) → `ai-semantic-cache` → `ai-sanitizer` (response) → `ai-rate-limiting-advanced` + File Log + OTel. Specific plugin knowledge required:

- **`ai-proxy` / `ai-proxy-advanced`** — universal LLM API abstraction across OpenAI, Anthropic, Azure OpenAI, AWS Bedrock, GCP Vertex/Gemini, Mistral, Hugging Face, Databricks, Cohere ([Kong docs — AI Proxy Advanced](https://developer.konghq.com/plugins/ai-proxy-advanced/)). Understands provider-specific auth patterns and how vault references wire into the plugin config.
- **`ai-prompt-guard`** — regex-based allow/deny on prompt content. Understands the operational cost of overly broad deny rules and tests them in staging before prod.
- **`ai-rate-limiting-advanced`** — token-cost-aware rate limiting; enterprise-only; model-based limiting added in 3.14 ([Kong docs — AI Rate Limiting Advanced](https://developer.konghq.com/plugins/ai-rate-limiting-advanced/)). Understands the window-semantics options and their behavioral differences across Kong minor versions.
- **`ai-semantic-cache`** — Redis-backed vector similarity cache ([Redis blog — Semantic processing with Kong AI Gateway and Redis](https://redis.io/blog/kong-ai-gateway-and-redis/)). Knows when to include it (high-volume, repeated-query patterns) and when to defer it (cold start, insufficient traffic data).
- **`ai-sanitizer` / AI PII Sanitizer** — 20+ PII categories across 12 languages; runs as a self-hostable sidecar so prompt content does not leave the regulated environment ([Kong docs — AI PII Sanitizer](https://developer.konghq.com/plugins/ai-sanitizer/), [Kong blog — AI Gateway 3.10](https://konghq.com/blog/product-releases/ai-gateway-3-10)). Understands the sidecar deployment model, the category-selection trade-offs, and the redaction-event audit log.

### GitOps

Fluent in PR-gated promotion — branching strategy, `deck diff` and `terraform plan` as required CI gates, environment-promotion sequences (dev → staging → prod), and emergency hotfix discipline (hotfix goes in, back-fill PR within 24h, no exceptions). At enterprise scale: experience with multi-region promotion strategies — changes must be promoted sequentially across regions with traffic-sample validation at each stage, not simultaneously. References: ([Kong blog — Deploy Configurations Using Terraform via GitOps](https://konghq.com/blog/engineering/kong-configurations-terraform-gitops)).

### OpenTelemetry

Knows how to configure Kong's OTel plugin to emit traces and metrics, and how to wire the OTel collector to the DXC-mandated observability sink (Datadog / Splunk / Grafana / Azure Monitor). Understands the cardinality implications of high-volume AI traffic — at enterprise scale, token counts amplify metric cardinality significantly and must be managed deliberately. Familiar with the downstream FinOps use case: token-usage metrics are the basis for chargeback attribution across BUs at Tier 2.

### Vault-reference patterns

Has wired Kong vault references against at least one enterprise secrets backend (HashiCorp Vault, AWS Secrets Manager, or Azure Key Vault). Understands the access-pattern options (workload identity vs static service-principal), the reference syntax in decK YAML and Terraform, the rotation runbook discipline, and the multi-region implication (each region's data plane must resolve vault references against a regionally-appropriate secrets backend — cross-region secret resolution is both a latency and a data-residency risk). A provider API key that has ever appeared in plaintext in a committed file is a mandatory rotation event ([AWS APN blog — Kong AI Gateway and Amazon Bedrock](https://aws.amazon.com/blogs/apn/unlock-advanced-ai-control-with-kong-ai-gateway-and-amazon-bedrock/)).

### Multi-region operations

At DXC's scale, data planes run in 2–3 regions minimum. Required awareness:

- **Cross-region IaC promotion discipline.** Each region has its own Terraform workspace and decK environment scope. Promotion from dev to staging to prod runs per region in sequence; a regression in region A must be caught before region B promotion proceeds.
- **Follow-the-sun on-call readiness.** Comfortable with a distributed on-call rotation where regional peers carry primary-hours coverage and the global rotation shares after-hours load. Has operated in a follow-the-sun model or is willing to; has written runbooks designed for execution by engineers in a different time zone who may not share context.
- **Regional data-residency awareness.** EU-employee AI traffic must be processed in EU-region data planes. The gateway engineer must understand which routes map to which regulatory regimes and ensure data-plane placement reflects those constraints. This is a collaboration with the compliance team, but the gateway engineer must not be ignorant of the constraint.

### On-call experience

Has carried a PagerDuty (or equivalent) on-call rotation for a production infrastructure system. Knows how to write a runbook that a non-specialist can follow at 2am, how to author alert thresholds that fire early enough to act but not so broadly they become noise, and how to conduct a post-mortem with a written root-cause analysis. At DXC's scale, the gateway engineer must also be comfortable handing off an active incident across time zones — the EMEA engineer who picks up a P1 at 08:00 GMT must have enough context from the Americas engineer's notes to continue without a synchronous handoff call.

---

## Strongly preferred

- **Lua or Go plugin authoring.** The operating contract requires exhausting built-in plugins before writing custom code — but when a gap genuinely exists, the gateway engineer needs to close it. Prior experience with Kong's PDK (Plugin Development Kit) in Lua or the Go plugin runner demonstrates the capability without it being a daily requirement. Pax's brief (§2) identifies "writes custom Lua/Go plugins only when no built-in covers the policy" as the world-class marker — the key word is "only when." At Tier 3 (mature federated platform), plugin authorship becomes a routine activity as the catalog evolves; the first hires who can do this are valuable.
- **Prior LLM-platform or AI-infrastructure exposure.** Has worked with LLM APIs (OpenAI, Anthropic, Azure OpenAI, Bedrock, Vertex) directly, understands token economics, prompt/response structure, and the security surface specific to AI traffic (prompt injection, data exfiltration, model-version drift). Increasingly essential as AI-specific plugins require reasoning about LLM-specific traffic patterns to tune correctly.
- **Regulated-industry experience.** Has worked in an environment with compliance requirements (HIPAA, GDPR, PCI, FedRAMP, or a major enterprise's contractual data-protection framework) where audit logging, data-residency enforcement, and PII handling were production requirements rather than aspirations. At DXC's 130k-employee multinational scale touching healthcare, financial, and government clients, this experience is directly applicable from day one and elevates a strong candidate significantly.
- **Enterprise-scale prior experience.** Has operated a gateway or platform serving 50,000+ users or equivalent request volume. Enterprise scale introduces problems that smaller deployments do not surface: metric cardinality at high volume, on-call rotation health across a distributed team, multi-region promotion with active traffic, and the organizational complexity of serving hundreds of consuming teams. A candidate whose prior environments were significantly smaller is not automatically unsuitable, but should be probed on how they anticipate scaling their practices.

---

## Nice-to-have

- **Service mesh familiarity (Istio or Linkerd).** The Kong gateway sits at the north-south edge; a service mesh operates east-west. Understanding the boundary — and the cases where they overlap (mTLS policy, traffic shaping, observability integration) — helps the gateway engineer collaborate with platform teams and avoid re-solving problems the mesh already handles.
- **Specific provider SDK experience.** Hands-on with the AWS Bedrock SDK, GCP Vertex AI SDK, or Azure OpenAI service SDK — useful when the `ai-proxy-advanced` plugin's provider abstraction has a gap and the gateway engineer needs to reason about the underlying provider behavior.
- **Kong plugin certification or Kong Konnect certification.** Kong's own certification program ([konghq.com/certifications](https://konghq.com/certifications)) is not a substitute for demonstrated production experience, but it signals structured study of the platform and familiarity with Kong's recommended operating patterns.
- **Prior platform-team or internal-product experience.** Has operated as part of a team that provides a platform service to internal consuming teams — understands the "gateway team as a service provider" posture, has written developer documentation for non-specialist consumers, and has operated a self-service onboarding process for route or API requests.

---

## Anti-patterns and disqualifiers

The following patterns are hard disqualifiers. A candidate who advocates for any of these in an interview is a no-hire, regardless of other strengths. Drawn directly from Pax's hire research (§4):

- **"I'll set up the IaC later."** The first deploy is the IaC. There is no "later" in a production gateway. This is the single most common pattern that produces six months of unrecorded drift and a rewrite project. If a candidate describes a current environment where IaC was added after the gateway was running, probe hard on how they fixed the drift — the answer reveals whether they understand the root problem.
- **Console-driven configuration.** Konnect's console is read-only triage. Any candidate who describes managing production routes via the Konnect UI, or who is unfamiliar with decK as the mechanism for production config, does not meet the baseline.
- **`latest` plugin pinning.** Kong AI plugin behavior changes meaningfully across 3.x minor releases. A candidate who pins to `latest` in production, or who does not have a plugin-version upgrade process, is carrying an invisible risk that surfaces as an unplanned production incident.
- **Secrets in decK YAML or Terraform state.** Provider API keys in committed files — even once, even "just for a test," even in a private repo — is a disqualifier. Kong vault references exist precisely to eliminate this.
- **Writing a custom plugin first.** A candidate who jumps to Lua/Go for a use case that the existing plugin catalog covers demonstrates poor judgment about operational cost and upgrade risk.
- **Ungated promotion.** No `terraform plan` review, no `deck diff` in CI, no PR approval — three separate failure modes, each sufficient to ship an outage. A candidate who describes shipping config changes directly to production without a stated policy for back-filling the PR and runbook has not internalized the operating model.
- **Skipping observability on test routes.** "It's just a test" routes become production. File Log and OTel are non-negotiable from the first route.
- **Single-region mental model for a multi-region platform.** For the first 2–3 hires specifically: a candidate who has only ever operated in a single region and who shows no awareness of multi-region promotion complexity, regional data-residency constraints, or follow-the-sun on-call considerations is a significant risk for a role that is building a global platform from the first day. This is not a disqualifier for hires 4+, but it is a serious gap for the founding engineers.

---

## Interview rubric / scorecard

**Usage instructions for interviewers:** Score each competency independently on a 1–5 scale before discussing with your co-interviewer. Calibrate after both scores are written down. A hire recommendation requires no score below 3 and an average of 4+ across all seven competencies. For the first 2–3 hires (staff/principal calibration), no score below 4 is acceptable.

---

### Competency 1 — Declarative-state discipline

*"Every entity in the gateway is represented in a versioned repo. The runtime is a rendered output of that repo, not the source of truth."*

| Score | Behavioral signal |
|---|---|
| 5 | Candidate articulates the repo-is-canonical principle unprompted. Describes a specific incident where `deck diff` caught a drift they didn't introduce and explains exactly how they resolved it. Has strong opinions about which entities are decK's responsibility vs Terraform's and can articulate why the split matters. |
| 4 | Candidate correctly describes decK sync/diff as the primary mechanism, knows how to wire it into CI, and has done it in a real environment. May not have a strong opinion on the Terraform/decK split but understands it exists. |
| 3 | Candidate knows decK and uses it, but primarily as a "backup" or "documentation" mechanism rather than as the authoritative source. Has not yet internalized the drift-detection use case. |
| 2 | Candidate is familiar with decK but relies on the Konnect UI for day-to-day changes. Describes a workflow where the repo "should" match what's running but admits it often doesn't. |
| 1 | Candidate manages the gateway via the UI or Admin API. Has not used decK or does not know what it is. May describe IaC as a future goal. |

---

### Competency 2 — Plugin composition judgment

*"Built-ins first, custom code last. Every plugin choice has a reason and a cost."*

| Score | Behavioral signal |
|---|---|
| 5 | Candidate can walk through the canonical AI plugin stack for a regulated route (`ai-sanitizer` → `ai-prompt-guard` → `ai-proxy-advanced` → `ai-semantic-cache` → `ai-rate-limiting-advanced`) and articulate why each plugin sits where it does in the chain, what happens if the order changes, and under what conditions they would defer `ai-semantic-cache` to a later release. Has a clear "last resort" standard for custom plugin authoring. |
| 4 | Candidate knows the major AI plugins, can describe a realistic composition for a standard use case, and understands the `ai-proxy-advanced` multi-provider model. May not have used `ai-sanitizer` or `ai-semantic-cache` in production but understands their purpose. |
| 3 | Candidate knows `ai-proxy` and basic rate limiting. Has limited exposure to the newer plugins (`ai-sanitizer`, `ai-semantic-cache`). Can describe when to use `ai-prompt-guard` but may not know the PII Sanitizer's sidecar deployment model. |
| 2 | Candidate has used Kong plugins but primarily in non-AI contexts (basic auth, rate limiting, request transformation). AI plugin knowledge is theoretical or surface-level. |
| 1 | Candidate is unfamiliar with the Kong AI plugin catalog. May know Kong from a proxy/load-balancer context but has not encountered the AI-specific plugin set. |

---

### Competency 3 — Drift triage

*"Non-empty `deck diff` in production is a P2. I know how to diagnose it, reconcile it, and prevent it from happening again."*

| Score | Behavioral signal |
|---|---|
| 5 | Candidate describes a specific drift incident they triaged, explains exactly how they identified the source (audit log, Admin API change history, incident ticket), how they reconciled (repo to match intent, not runtime to match whatever the UI shows), and what prevention mechanism they put in place afterward (CI drift check, Konnect RBAC tightening, etc.). |
| 4 | Candidate understands the drift-detection workflow, can describe how they would triage a non-empty diff in CI, and has a clear preference for repo-as-canonical over runtime-as-canonical. May not have a vivid personal example but can walk through the steps correctly. |
| 3 | Candidate knows drift is a risk and uses `deck diff` to check, but may treat reconciliation as "update the repo to match what's running" rather than "understand why the runtime diverged and fix the source." |
| 2 | Candidate is aware of drift as a concept but has no systematic process. Relies on periodic manual checks. Does not have `deck diff` in CI. |
| 1 | Candidate has not encountered the concept of declarative-state drift in a gateway context. May describe the Admin API or UI as the source of truth. |

---

### Competency 4 — Runbook authoring

*"Every production operation has a runbook. If it doesn't exist yet, I write it before I do the operation the first time."*

| Score | Behavioral signal |
|---|---|
| 5 | Candidate can describe the structure of a runbook they authored (trigger conditions, step-by-step procedure, rollback steps, escalation contacts, "last executed" timestamp). Has a specific example of a runbook that was used successfully by a Tier-1 NOC operator without the author present. Treats runbook creation as part of the definition of done for every new operation. For the first 2–3 hires: has authored runbooks for multi-region operations specifically — e.g., a key-rotation runbook that handles regional promotion sequencing. |
| 4 | Candidate writes runbooks as a standard practice, has examples they can describe, and understands that runbooks must be testable and maintainable. May not have a formal "last executed" discipline but keeps them reasonably current. |
| 3 | Candidate writes runbooks but primarily for their own use or for documentation purposes, not specifically designed for a non-specialist to execute under pressure. Runbooks may be in Confluence and stale. |
| 2 | Candidate acknowledges the value of runbooks but does not write them proactively. Relies on tribal knowledge for most operations. |
| 1 | Candidate has not written runbooks or does not see the value. Describes on-call as "I just know how to fix it." |

---

### Competency 5 — Observability instinct

*"I wire File Log and OTel before the first route goes live. Observability is not a feature; it is a precondition."*

| Score | Behavioral signal |
|---|---|
| 5 | Candidate treats observability configuration as part of route onboarding — not a subsequent task. Can describe the specific OTel configuration for a Kong AI route (traces, token-usage metrics, latency histograms), explain the File Log audit requirement for regulated routes, and articulate why `ai-sanitizer` redaction events need to be captured and retained. Has a view on cardinality risks at AI-traffic volumes and how to mitigate them. At enterprise scale: has thought about how token-usage metrics feed into chargeback attribution across BUs and understands that metric cardinality at 130k-employee scale requires deliberate pipeline design. |
| 4 | Candidate consistently wires OTel and File Log on production routes, can describe how Kong's OTel plugin works, and has a working relationship with an observability platform. Understands that AI-route observability is more complex than standard HTTP (token counts, semantic-cache hit rates, sanitizer redaction events). |
| 3 | Candidate configures OTel and logging as part of their standard process but may not be specific about what signals matter most for AI routes. Treats it as important but has not encountered the AI-specific metrics. |
| 2 | Candidate adds observability when asked or when an incident forces it. Does not treat it as a precondition. |
| 1 | Candidate has minimal observability experience or treats it as someone else's responsibility. |

---

### Competency 6 — Secrets discipline

*"Provider API keys never appear in a committed file. Vault references are the only acceptable pattern in production. I know what to do if a key is ever leaked."*

| Score | Behavioral signal |
|---|---|
| 5 | Candidate describes Kong vault references in detail — syntax in decK YAML, supported backends (HashiCorp Vault, AWS Secrets Manager, Azure Key Vault), the access-pattern options, and the rotation runbook discipline. Has a clear answer for "what happens if a key ends up in git history" (rotate immediately, audit for unauthorized use, post-mortem the process failure that allowed it). Has enforced this standard on a team that did not start with it. At multi-region scale: understands the regional secrets-backend implication (EU-region data planes should resolve vault references against EU-region secrets backends for data-residency compliance). |
| 4 | Candidate uses vault references in production, has wired at least one secrets backend to Kong, and treats secrets-in-YAML as a disqualifying pattern. Can describe the rotation runbook steps correctly. |
| 3 | Candidate knows secrets should not be in YAML and uses environment variables or a secrets manager, but may not have implemented Kong vault references specifically. Understands the concept but has not applied it in a Kong context. |
| 2 | Candidate is aware of the risk but has worked in environments where API keys appeared in config files "temporarily." Does not have a strong enforced policy. |
| 1 | Candidate has not thought about secrets management in a gateway context or describes inline secrets in config as acceptable. |

---

### Competency 7 — Communication with platform, security, and application teams

*"I am a service provider to consuming teams and a consumer of platform services. I translate between gateway-layer concerns and each audience's frame."*

| Score | Behavioral signal |
|---|---|
| 5 | Candidate can describe a situation where they had to translate a gateway-layer constraint (e.g., "this plugin requires a specific vault-reference pattern") into terms an application developer could act on, and separately translate a platform-team constraint (e.g., "the network policy doesn't allow egress to the Konnect SaaS endpoint on port X") into a gateway-layer remediation. Treats consuming teams as customers and proactively writes onboarding docs they can use without asking. At enterprise scale: has operated in a team serving hundreds of internal customers and understands how a self-service process and developer portal changes the gateway team's interface posture. |
| 4 | Candidate communicates effectively with adjacent teams, can adjust their technical depth to the audience, and has a track record of unblocking route onboardings without requiring consuming teams to understand the full gateway internals. |
| 3 | Candidate communicates well within their team but may rely on others to interface with consuming teams or security. Has not owned the "gateway as a platform" relationship explicitly. |
| 2 | Candidate is primarily heads-down on the technical implementation and finds cross-team communication challenging. May have caused friction with consuming teams by moving too fast without communicating changes. |
| 1 | Candidate has minimal cross-functional communication experience or describes their ideal role as purely technical with no stakeholder interface. |

---

## Compensation and level positioning

This role sits at the **Senior IC to Staff / Principal Engineer** band in most enterprise leveling frameworks. For the first 2–3 hires specifically, the sourcing target is **Staff or Principal IC** — even if the open requisition is labeled "Senior Engineer" in DXC's HR system. The first hires are pattern-setters, and the cost of pattern-setting done wrong at a 130k-employee platform is measured in years, not months.

For hires 4+, the Senior IC band is appropriate; they will inherit the patterns the founding engineers set and grow within them. Jesse to calibrate against DXC's internal leveling framework and current market rates with DXC HR — this JD deliberately omits a number to preserve flexibility.

Note: the specialization of Kong AI Gateway (versus general Kong proxy) combined with enterprise-scale multi-region experience and AI-plugin-stack production depth is not widely commoditized. Compensation expectations for candidates who meet all three of these should be positioned toward the top of the Senior/Staff band.

---

## Open questions for Jesse

The following require DXC-specific input before this JD is ready for HR submission or external posting:

1. **Location and remote policy.** Is this role remote-eligible, hybrid, or on-site? For a multi-region team (Tier 2 shape), remote-eligible is strongly recommended to access the candidate pool across Americas, EMEA, and APAC time zones. On-site requirements dramatically constrain the available pool for a role this specialized.
2. **Security clearance requirement.** Does any DXC customer engagement that will route AI traffic through this gateway require the gateway engineer to hold or be eligible for a specific clearance level (e.g., US government contracts)? If yes, this is a sourcing constraint that needs to appear in the JD explicitly and affects which regions the role can be hired in.
3. **Regulated-customer overlay.** Which DXC customer industries will be the first consumers of this gateway (healthcare, financial services, government, retail)? The compliance and on-call sections of this JD can be made more specific once the customer context is known.
4. **Contractor vs. FTE.** The first 2–3 hires (staff/principal, pattern-setters) should strongly favor direct hire or a long-term engagement model. Contractor churn at the pattern-setting level is a significant platform risk. Jesse should confirm DXC's policy on this before the first requisition is opened.
5. **Internal transfer vs. external hire.** If DXC has existing API gateway engineers or SREs who could be upskilled into the Kong AI Gateway specialization, this JD doubles as a development framework for those conversations. Note: the multi-region awareness and AI plugin stack requirements are real ramp items for engineers coming from a non-AI Kong background; factor 3–6 months of ramp time into any internal transfer plan.
6. **Hiring sequence across the team.** The org chart calls for 6–10 gateway engineers at Tier 2 across 2–3 regions. What is the intended hiring sequence — all Americas first, then EMEA, then APAC? Or hire one per region in the first wave? The first hire sets patterns; the second and third hires in different regions establish whether those patterns transfer. Jesse should confirm the intended sequence before the first offer is extended.

---

*Cross-references: [[2026-05-12-kong-ai-gateway-team-org-chart]] (org-chart and on-call math), [[2026-05-11-api-gateway-engineer-hire-research]] (Pax's role research — source for anti-patterns, plugin landscape, and world-class benchmark), [[2026-05-12-ai-security-engineer-hire-research]] (Pax's AI-security team research), [[2026-05-11-dxc-arch-team-ask-kong-ai-gateway]] (architecture decisions gating this hire's first work), [[PKM/My Life/Projects/kong-ai-gateway]] (project state).*
