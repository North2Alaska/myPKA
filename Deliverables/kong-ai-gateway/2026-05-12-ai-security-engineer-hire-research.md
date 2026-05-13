---
type: deliverable
deliverable_kind: hire-research
researcher: Pax
date: 2026-05-12
updated_at: 2026-05-12
for: Nolan
project: kong-ai-gateway
role: AI Security Lead + Team (federated, enterprise scale)
status: draft-revised
linked_sops:
  - SOP-001-how-to-add-a-new-specialist
linked_deliverables:
  - 2026-05-11-api-gateway-engineer-hire-research
  - 2026-05-11-dxc-arch-team-ask-kong-ai-gateway
  - 2026-05-12-kong-ai-gateway-team-org-chart
linked_people:
  - jesse-swensen
tags:
  - hire-research
  - kong-ai-gateway
  - ai-security
  - dxc
  - llm-security
  - federated-team
---

# Hire Research — AI Security Lead and Team (working title for the lead: Vex)

- **Researcher:** Pax
- **Date:** 2026-05-12 (revised same-day for scope change — see revision note below)
- **For:** Nolan, to translate into `Team/<Name> - AI Security Lead/AGENTS.md` and accompanying team contracts
- **Project context:** [[PKM/My Life/Projects/kong-ai-gateway]]
- **Companion roles already hired:** [[Team/Axle - API Gateway Engineer/AGENTS]] (gateway engineering)
- **SOP:** [[SOP-001-how-to-add-a-new-specialist]] §2

---

## Revision note — 2026-05-12 scope change

The first version of this brief assumed first-production-rollout scope (a single AppSec engineer stretched onto the gateway, with a 90-day bridge contractor). Jesse clarified the actual scope same-day: **the Kong AI Gateway will route ALL AI traffic for DXC** — a 130,000-employee multinational — covering every employee, every DXC-developed application, and every region DXC operates in.

That is enterprise-platform scope, not project-team scope. At that scale:

- AI security is a small federated team, not one role.
- The first hire is the lead (staff/principal level), not an IC.
- Red-team becomes a dedicated workstream with its own FTE, not a borrowed cadence.
- Audit/log review at 130k-employee traffic volume becomes a SIEM workstream — the AI-security team partners with a SOC, it does not operate one.

What was revised: §1 role one-liner, §2 day-to-day (lead-level activities added), §6 boundaries (clarified team-shape), §7 hiring paths (now three team-shaped paths, not three single-role paths), **new §8 Federation model**, §10 (formerly §9) open questions, and the framing throughout. **§3, §4, §5 sourcing and content are unchanged** — the underlying threat-model, plugin-chain knowledge, and anti-patterns hold identically at team scale; the difference is that they are now distributed across a lead + N engineers + 1 red-team FTE rather than concentrated in one person.

Nolan is revising the companion org chart in parallel ([[2026-05-12-kong-ai-gateway-team-org-chart]]). Team-shape numbers should converge; if they drift after both revisions land, Larry will reconcile.

---

## 1. Role one-liner — revised for enterprise scope

The AI Security Lead heads a small federated AI-security team that owns the threat model, policy, red-team posture, and SIEM-integration design for the enterprise AI Gateway — the platform that routes **all** of DXC's AI traffic across every region, every business unit, and every customer-facing AI-enabled application. This is a staff/principal-level role with three things a standard senior-AppSec promotion does not carry: a published point of view on LLM-specific failure modes (prompt injection, jailbreak, training-data leakage, model-extraction, token-cost DoS), an existing relationship pattern with central CISO functions and per-region Data Protection Officers, and the temperamental stance that AI guardrails are defense-in-depth-only, not eliminative.

Beneath the lead sits a small team — likely 3 to 4 AI-security engineers split between central standards work and per-region or per-BU compliance overlay, plus 1 dedicated AI red-team engineer running continuous adversarial CI (Garak / promptfoo / llm-guard) against the gateway's plugin chain. The team partners with — but does not operate — DXC's SIEM/SOC, which receives `ai-sanitizer` redaction events and File Log audit streams from billions of requests and runs them through automated review with human-in-the-loop for high-confidence anomalies ([Microsoft / Security Insider — Sentinel/Splunk as the canonical destination for LLM audit logs](https://www.microsoft.com/en-us/security/security-insider/emerging-trends/ai-security-guide-data-governance-and-security), [Radiant Security — SIEM 2026 guide](https://radiantsecurity.ai/learn/siem-security-information-and-event-management-explained-2026-guide/)).

The distinction from a standard senior-AppSec stretch matters concretely. A senior AppSec engineer reading `ai-prompt-guard` documentation will treat it like a WAF rule and move on. An AI-security lead reads the same documentation and immediately knows three things: (a) regex on user-role messages cannot stop indirect prompt injection coming from a retrieved document, (b) the documented zero-width / bidirectional unicode caveat is exploitable today ([Kong AI Prompt Guard docs](https://developer.konghq.com/plugins/ai-prompt-guard/), [arXiv 2504.11168 — Bypassing Prompt Injection and Jailbreak Detection in LLM Guardrails, April 2025](https://arxiv.org/html/2504.11168v1)), and (c) the same plugin chain still needs application-level controls because OWASP's own LLM01 guidance lists privilege control, RAG segregation, and human-in-the-loop as mitigations a gateway *cannot* enforce ([OWASP LLM01 — Prompt Injection 2025](https://genai.owasp.org/llmrisk/llm01-prompt-injection/)). Multiply that distinction by 130,000 employees and every region DXC operates in and the lead-level scope becomes obvious.

## 2. What the best-in-world version does, day to day

The best AI-security teams operate as the policy authority and red-team partner for the enterprise gateway, not as a downstream reviewer. At 130k-employee scale, day-to-day work splits into **lead-level activities** (which the AI Security Lead personally owns or chairs) and **team-level activities** (which the lead authors the standard for and the team executes).

### Lead-level — what the AI Security Lead personally does

- **Authors the enterprise AI-security standards.** The policy that says "every AI route running through the gateway must have plugin chain X, with redaction categories Y active, with rate-limit budget Z, and with audit events landing in SIEM channel W." Published as a document the gateway practice, central CISO, and per-region DPOs all sign off on.
- **Chairs the plugin-chain review board.** Every new plugin entering the gateway's stable catalog gets a threat-model pass at the board before Axle ships it. Every plugin upgrade across a minor version (3.10 → 3.12, 3.12 → 3.14) gets re-reviewed for behavior drift. The lead chairs; the team members vote.
- **Owns the relationship with central DXC CISO and per-region DPOs.** A multinational running a single gateway for all AI traffic touches GDPR (EU), regional residency regimes (Australia, UK, Switzerland, Canada, Japan, Brazil), and sector-specific compliance overlays for DXC's regulated customer base (healthcare, financial services, government). The lead is the named counterpart for each of those functions; the team executes against the resulting policy.
- **Owns the AI red-team program.** Defines the cadence, the in-scope route classes, the success criteria, and the disclosure path when a bypass is found in a production route. The dedicated red-team engineer executes; the lead owns the program.
- **Sets the SIEM integration contract.** Defines what events the gateway emits, what the SOC is expected to review, and what triggers human-in-the-loop escalation back to the AI-security team. Partners with DXC's existing SIEM/SOC function — does not operate it.
- **Cross-region coordination.** Runs the weekly sync where regional liaisons surface compliance issues, incidents, and new regulatory developments. The lead translates these into standards updates.
- **Maintains the guardrails-bypass dossier.** AI guardrails are bypassable as a class — character injection, unicode smuggling, semantic obfuscation, AML word substitution all achieved up to 100% evasion against named commercial guardrails in published research ([arXiv 2504.11168, April 2025](https://arxiv.org/html/2504.11168v1), [HackRead — HiddenLayer bypass of OpenAI Guardrails, October 2025](https://hackread.com/openai-guardrails-bypass-prompt-injection-attack/)). The lead owns the living dossier; engineers contribute findings.

### Team-level — what the AI-security engineers and red-team engineer do day-to-day

- **Threat-models the plugin chain per route**, before it ships. Each new AI route gets a STRIDE-style pass mapped to the OWASP LLM Top 10 (LLM01 Prompt Injection, LLM02 Sensitive Info Disclosure, LLM07 System Prompt Leakage, LLM10 Unbounded Consumption) and the MITRE ATLAS techniques that apply — primarily `AML.T0051.000` (Direct Prompt Injection) and `AML.T0051.001` (Indirect Prompt Injection) ([MITRE ATLAS — AML.T0051](https://atlas.mitre.org/), [startupdefense.io — AML.T0051 reference](https://www.startupdefense.io/mitre-atlas-techniques/aml-t0051-llm-prompt-injection), [OWASP LLM Top 10 2025](https://genai.owasp.org/llm-top-10/)). At 130k-employee scale, the volume of routes makes this a queue, not a one-off.
- **Authors the prompt-guard and sanitizer rules** that ship to production. Not the gateway engineers (Axle and his team). The AI-security engineers own the *policy text* — which deny patterns, which PII categories, which semantic-prompt-guard embeddings, which languages — and sign off on the rule diff in PR.
- **Owns the vault-reference posture.** Reviews every `{vault://...}` reference in decK YAML and Terraform; verifies no provider key ever appears in plaintext, in state, or in committed config. Sets the rotation cadence and the workload-identity vs static-credential decision with the platform team ([Kong Gateway Secrets Management docs](https://developer.konghq.com/gateway/secrets-management/)).
- **Audits log retention and content.** Confirms File Log and OpenTelemetry payloads are PII-redacted *before* leaving the regulated environment, that retention windows match the compliance regime per region, and that audit events include the redaction count from `ai-sanitizer` (the plugin emits per-redaction events; the AI-security team partners with the SIEM/SOC for the review cadence at billion-request scale — see §8) ([Kong AI PII Sanitizer docs](https://developer.konghq.com/plugins/ai-sanitizer/)).
- **Runs the continuous AI red-team CI suite.** A dedicated red-team engineer (likely 1 FTE at this scale) runs Garak for broad probe coverage, llm-guard for runtime-style guardrail testing, promptfoo for CI-gated regression suites on every plugin-rule PR, and manual adversarial prompts against routes where automated scanners under-cover ([Garak — NVIDIA, latest release v0.15.0 dated May 2026](https://github.com/NVIDIA/garak), [llm-guard — Protect AI](https://github.com/protectai/llm-guard), [promptfoo red-team docs](https://www.promptfoo.dev/docs/red-team/) — note: promptfoo was acquired by OpenAI on 2026-03-09 ([OpenAI / promptfoo acquisition coverage](https://github.com/promptfoo/promptfoo)); the OSS project remains active but ownership concentration is a fresh staleness signal worth tracking).
- **Liaises with per-region or per-BU compliance** (see §8 federation). Translates DXC's data-residency, PII handling, and audit retention obligations per region into concrete plugin configuration changes the gateway team can ship.

## 3. Core competencies — four distinct skill bundles

*(Unchanged from v1. These competencies apply identically at team scale — the lead carries strong fluency across all four; team engineers may be strong on 3a + 3c and ramping on 3b; the dedicated red-team engineer is strongest on 3b and the adversarial-toolchain pieces of 3c.)*

These are independent. A candidate strong on (a) and weak on (b) is a generalist AppSec engineer. A candidate strong on (b) but weak on (c) is an academic. The role needs all four — distributed across the team if not present in any one head.

### 3a. Traditional AppSec foundation (the baseline)

OWASP Top 10 (web), OWASP API Top 10, secrets management discipline, IAM / RBAC modeling, vault administration patterns. Not novel for AppSec engineers; non-negotiable as the floor. Anchors include CWE / CVE literacy, threat modeling (STRIDE / LINDDUN), incident response basics. This is the bundle a stretch hire from existing DXC AppSec already has.

### 3b. LLM-specific security (the differentiator)

The body of knowledge AppSec curricula do not yet teach. Load-bearing references:

- **OWASP Top 10 for LLM Applications 2025** — the canonical taxonomy. LLM01 Prompt Injection, LLM02 Sensitive Information Disclosure, LLM03 Supply Chain, LLM04 Data and Model Poisoning, LLM05 Improper Output Handling, LLM06 Excessive Agency, LLM07 System Prompt Leakage, LLM08 Vector and Embedding Weaknesses, LLM09 Misinformation, LLM10 Unbounded Consumption ([OWASP LLM Top 10 2025](https://genai.owasp.org/llm-top-10/)).
- **MITRE ATLAS** — the adversarial-techniques catalog for AI systems. As of v5.4.0 (February 2026): 16 tactics, 84 techniques, 56 sub-techniques. `AML.T0051` (LLM Prompt Injection) splits into `.000` Direct and `.001` Indirect; recommended mitigations include `AML.M0019` (access control), `AML.M0020` (Gen AI Guardrails), `AML.M0024` (AI Telemetry Logging), `AML.M0033` (Input/Output Validation for AI Agent Components) ([MITRE ATLAS](https://atlas.mitre.org/), [vectra.ai — ATLAS overview](https://www.vectra.ai/topics/mitre-atlas)).
- **NIST AI RMF (AI 100-1, Jan 2023) and Generative AI Profile (AI 600-1, July 2024)** — the Govern / Map / Measure / Manage functions, plus the Gen-AI-specific risk actions. Required for DXC-regulated-customer conversations ([NIST AI RMF home page](https://www.nist.gov/itl/ai-risk-management-framework)).
- **The unsolvability literature.** Prompt injection cannot be fully eliminated by input filtering — the LLM processes prompt and data in one token stream with no privilege boundary. This is consensus across primary sources (OWASP LLM01 itself names "defense-in-depth" as the strategy, not "elimination"), independent practitioner analysis ([Simon Willison — recurring writing on prompt injection unsolvability, 2023–2025](https://simonwillison.net/), [GuidePoint Security — Prompt Injection: The AI Vulnerability We Still Can't Fix, August 2025](https://www.guidepointsecurity.com/blog/prompt-injection-the-ai-vulnerability-we-still-cant-fix/)), and empirical guardrail-bypass research ([arXiv 2504.11168, April 2025](https://arxiv.org/html/2504.11168v1), [HiddenLayer / OpenAI Guardrails bypass, October 2025](https://hackread.com/openai-guardrails-bypass-prompt-injection-attack/)). An engineer who treats `ai-prompt-guard` as the answer is doing the job wrong.

### 3c. Gateway-plugin-specific knowledge (Kong AI Gateway)

How each Kong plugin actually works, and — more importantly — how each can be misconfigured. The single most useful thing this team brings that an external AI-security consultancy does not is fluency in the *specific plugin chain* the gateway runs. Concrete:

- **`ai-prompt-guard`** — PCRE regex allow/deny on user-role chat messages (or full prompt for completion models). Deny rules take precedence over allow rules; a request matching a deny rule returns 400 ([Kong AI Prompt Guard docs](https://developer.konghq.com/plugins/ai-prompt-guard/)). Known limitation, documented by Kong: zero-width and bidirectional unicode characters render as blank space in human interfaces but remain visible to the LLM — an exploitable channel that the regex layer does not catch by default. The team must either canonicalize input before the regex or layer semantic guard behind it.
- **`ai-semantic-prompt-guard`** — embedding-based allow/deny, supports OpenAI / Azure / Bedrock / Gemini / Hugging Face / Mistral embedding providers and Redis / pgvector / MemoryDB / Valkey as the vector store ([Kong AI Semantic Prompt Guard docs](https://developer.konghq.com/plugins/ai-semantic-prompt-guard/)). Catches paraphrased / obfuscated requests that regex misses. Failure mode: embedding-similarity thresholds are sensitive to phrasing drift; tuning is an AI-security-team responsibility, not a deploy-time default.
- **`ai-sanitizer` (AI PII Sanitizer)** — runs as a separate Docker sidecar (≥600 MB RAM per instance), anonymizes ~17 field categories per the plugin docs ([Kong AI PII Sanitizer docs](https://developer.konghq.com/plugins/ai-sanitizer/)). **Discrepancy to flag inline:** Kong's 3.10 release blog claims "20+ PII categories across 12 different languages" ([Kong blog — AI Gateway 3.10, April 2025](https://konghq.com/blog/product-releases/ai-gateway-3-10)) while the current plugin docs list 17 field types and 9 supported languages (English, Spanish, French, German, Italian, Japanese, Korean, Portuguese, Turkish). Re-verify the active version against the live docs at config time; the marketing claim is not load-bearing for production rules. Other documented constraints: requires Kong Gateway 3.10+ and Enterprise tier; response-side sanitization requires v3.12+; one NLP model per container. Per-redaction events emit to the log pipeline — the team reviews these in partnership with SIEM/SOC for both compliance and detection (see §8).
- **`ai-rate-limiting-advanced`** — token-cost-aware rate limiting (Enterprise-only). The security-relevant lens: this is the primary mitigation for LLM10 Unbounded Consumption and for token-cost DoS attacks against the gateway's budget. Mis-tuned limits either accept abuse or rate-limit legitimate traffic.
- **Vault references** — `{vault://[provider]/[secret-path]}` resolves to env / Konnect Config Store / HashiCorp Vault / AWS Secrets Manager / Azure Key Vault / GCP Secret Manager / CyberArk Conjur (3.11+) ([Kong Secrets Management docs](https://developer.konghq.com/gateway/secrets-management/)). The AI-security team owns the rotation cadence and the access-pattern decision (workload identity vs static credential) with the platform team.

### 3d. Compliance / regulatory (DXC context)

DXC operates in regulated markets and serves regulated customers. At 130k-employee scale, this is no longer a quarterly checkpoint — it is a continuous workstream. The team does not own compliance certification — that's the compliance officer's mandate — but it owns translating each regime into plugin configuration. Specifically:

- **Data residency.** Konnect (Kong's SaaS control plane) is US-hosted; data planes are self-managed in the region the user picks. PII Sanitizer is a self-hostable sidecar specifically so prompt content does not leave the regulated environment ([Kong AI Gateway product page](https://konghq.com/products/kong-ai-gateway)). The regional liaison confirms region constraints per route per BU.
- **PII categories.** Which of `ai-sanitizer`'s field types are active is a per-route, per-region decision driven by what regulated data may transit. Default-on across categories is safe but high false-positive; tuning is a regional liaison's responsibility against central-team standards.
- **Audit retention.** File Log destination, retention window, and access controls are compliance-driven per region; SIEM landing zone differs per regulatory regime.
- **EU AI Act exposure.** As of 2026 the Act is in phased implementation; GPAI provider obligations (transparency, risk management) ([artificialintelligenceact.eu](https://artificialintelligenceact.eu/)) and downstream-deployer obligations apply to DXC's European footprint directly — not just to customers. For a 130k-employee multinational routing all AI traffic through one gateway, EU AI Act exposure pulls forward record-keeping, traceability, and risk-management controls. This is a lead-level concern, owned in partnership with DXC legal.

## 4. Anti-patterns the team must avoid

*(Unchanged from v1 — anti-patterns are stable across scales. At enterprise scale, each anti-pattern matters more, not less.)*

These are the failure modes a mediocre version of this team produces. Be ruthless about each.

- **Treating `ai-prompt-guard` as a perimeter.** It is a *layer* in defense-in-depth, not a boundary. Empirical guardrail-bypass rates against named commercial systems (Microsoft Azure Prompt Shield, Protect AI v1/v2, Meta Prompt Guard, Nvidia NeMo Guard, Vijil) hit 100% with simple character-injection techniques ([arXiv 2504.11168, April 2025](https://arxiv.org/html/2504.11168v1)). A team that ships routes with prompt-guard alone and says "we're covered" is doing the wrong job.
- **Trusting plugin defaults.** Kong ships sensible defaults, but every regulated route needs the deny-list, the active PII categories, the semantic-guard threshold, and the rate-limit budget *authored* — not defaulted. Default-trust is the most common AppSec-engineer-stretched-into-AI-security failure mode.
- **Allowing un-redacted PII in log files.** File Log emits before-redaction content if the `ai-sanitizer` is wired downstream of the log plugin. Plugin chain order is load-bearing for compliance. The team reviews chain order on every route.
- **Provider API keys anywhere outside the vault.** No inline secrets in decK YAML, Terraform state, CI logs, error responses, or test fixtures. Rotation cadence is the team's call, not "whenever the vendor key leaks." This is shared discipline with Axle's gateway team; the AI-security team owns the *policy*.
- **LLM-as-judge for safety enforcement.** The HiddenLayer / OpenAI Guardrails bypass (October 2025) confirmed: using the same model to generate and to judge creates a "same model, different hat" problem that is structurally bypassable ([HackRead, October 2025](https://hackread.com/openai-guardrails-bypass-prompt-injection-attack/)). When Kong adds LLM-judged plugins (the `ai-request-transformer` / `ai-response-transformer` shape, or future LLM-judge guardrails), the team flags the architectural risk explicitly.
- **One-shot red-teaming.** A single pre-launch red-team is a checkpoint, not a posture. At enterprise scale, Garak / promptfoo run in CI on every plugin-rule PR with a fast subset on PR and a full suite nightly; the dedicated red-team engineer owns the suite.
- **Skipping indirect prompt injection.** Direct injection (user-typed) is the easy case. Indirect injection — malicious content in a RAG-retrieved document, a tool output, an email body the LLM summarizes — is the harder and more dangerous case ([MITRE ATLAS AML.T0051.001](https://atlas.mitre.org/), [startupdefense.io](https://www.startupdefense.io/mitre-atlas-techniques/aml-t0051-llm-prompt-injection)). Every route that ingests retrieved content gets an indirect-injection threat-model row.
- **Conflating with the compliance officer's job.** This team authors policy at the *configuration* layer; the compliance officer owns certification, customer attestations, and the regulatory mapping. Confusing the two leads to engineering work being blocked on legal sign-off, or compliance commitments being made the gateway cannot enforce.
- **Pinning to `latest` for any security-critical plugin.** Same discipline as Axle: every AI plugin version is pinned and re-verified on upgrade. Plugin behavior for `ai-sanitizer` changed materially between 3.10 and 3.12 (response-side sanitization arrived in 3.12), and the rate-limiting model-based limits arrived in 3.14. Implicit version drift is a security event.
- **Centralizing what should be federated, or federating what should be central.** At 130k-employee scale, both directions of failure show up. Centralizing per-region compliance overlays creates a bottleneck and regional incidents go uninvestigated; federating standards authoring creates inconsistent plugin configurations across regions and audit findings the team cannot defend in front of a regulator. The split is concrete (see §8).

## 5. Deliverables this team produces

*(Largely unchanged from v1. Now distributed across the team — the lead owns the standards-level artifacts; engineers and red-team engineer own the per-route and CI-suite artifacts.)*

**World-class:**

- **Enterprise AI-security standard** (lead-authored) — the document that names the required plugin chain, redaction categories, rate-limit budget shapes, and audit-event contract for every AI route on the gateway. Signed off by central CISO and per-region DPOs. Versioned in IaC.
- **Per-route threat model** mapped to OWASP LLM Top 10 and MITRE ATLAS, stored in the IaC repo alongside the decK YAML for that route. Authored by the engineer assigned to the route's BU/region; reviewed in PR by the lead or a peer; updated on plugin-chain change.
- **Plugin-chain review checklist** that every new AI route passes before merge: plugin order, denies before allows, sanitizer before log, vault references for all keys, OTel + File Log present, rate limit set, indirect-injection considered, SIEM channel correct for region. One page. Lives in the repo.
- **PII redaction policy per route, per region**, naming the active `ai-sanitizer` field types, the language set, and the failure-open vs failure-closed decision if the sanitizer sidecar is unhealthy. Regional overlays clearly delineated.
- **Vault rotation runbook** — provider keys, decK signing keys if used, rotation cadence per regime, who approves, how to verify post-rotation. Owned by the lead with central-platform-team co-sign.
- **Red-team CI suite** (dedicated red-team engineer-owned) — Garak baseline as a nightly job, promptfoo regression suite as a per-PR check (~10 minute fast subset, full suite nightly per practitioner guidance — [Promptfoo CI integration docs](https://www.promptfoo.dev/docs/red-team/)), manual adversarial set on a quarterly cadence, plus the "indirect injection via RAG corpus" exercise that scanners don't cover.
- **Guardrails-bypass dossier** — the living document. Names every known bypass class the team has tested, what the gateway catches and does not, and the compensating application-side controls each upstream service must implement. Lead-curated; team-contributed.
- **Compliance mapping note per region** — OWASP LLM Top 10 ↔ MITRE ATLAS ↔ NIST AI 600-1 ↔ region-specific obligations (GDPR/EU AI Act, UK DPA, regional residency regimes) ↔ DXC's customer-specific obligations. One source of truth per region; cross-linked in a master index the lead owns.
- **SIEM integration contract** (lead-authored, jointly held with DXC SOC) — names the events the gateway emits, the SIEM landing zones per region, the auto-review rules vs human-in-the-loop escalation thresholds, and the playbook for an `ai-sanitizer` redaction-spike anomaly.

**Adequate (and to be improved on):** the team reviews PRs reactively, copies OWASP LLM Top 10 into a Confluence page once, runs a single pre-launch red-team, and trusts plugin defaults. This is what most orgs ship in 2026; it is below the bar a 130k-employee enterprise gateway demands.

## 6. Boundaries — what the AI-security team does NOT do

- **Plugin chain authoring and deployment** — that's Axle and the gateway-engineering team. The AI-security team authors *the rules inside* the plugins (regex, embeddings, redaction categories, rate-limit budgets); Axle's team authors the plugin-chain declarative state and ships it.
- **Vault administration and secrets-backend operation** — that's the DXC platform team. The AI-security team consumes Vault, names rotation cadences, and reviews vault-reference syntax in IaC; they do not run the Vault cluster.
- **Model selection, fine-tuning, prompt engineering** — that's data science / LLM engineering / product. The team reviews prompts adversarially but does not author them for product use.
- **Compliance certification and customer attestations** — that's the compliance officer. The team authors the *evidence* (audit logs, threat models, red-team reports) the compliance officer uses.
- **General K8s / cluster security** — that's the DXC platform-security team. The team focuses on the gateway and what flows through it.
- **Penetration testing of the entire application stack** — outside scope. The team red-teams the *AI surface*; conventional pen-testing of the wrapping web app remains with the standard AppSec function.
- **SIEM/SOC operation** — that's the existing DXC SOC. The AI-security team designs the integration, names the event contract, and partners on triage — it does not staff the SOC console or run alerts 24/7.

## 7. Hiring path question — three team-shaped paths (revised for enterprise scope)

The earlier version of this section evaluated three single-role paths and recommended a stretched DXC AppSec engineer with a 90-day bridge contractor. **That recommendation does not survive contact with 130k-employee scale and "all AI traffic" scope.** A single stretch engineer cannot author enterprise standards, chair a plugin-chain review board, own central-CISO and per-region-DPO relationships, run an AI red-team program, and review per-route threat models concurrently. The minimum viable team shape is a **lead + 3 engineers + 1 dedicated AI red-team engineer = 5 FTE**, sitting in Tier 2 of the org-chart proposal ([[2026-05-12-kong-ai-gateway-team-org-chart]]). Below that shape, work either does not get done or it is done badly enough to fail a regulator's audit.

The remaining hiring question is *sourcing*: which mix of external hire, internal stretch, and bridge contracting builds the team fastest with the highest probability of success.

### (a) Build the team from external senior hires

**Shape:** Lead + 3–4 engineers, all external. External hires from FAANG AI-security functions, security-tooling vendors (Lakera, Protect AI, HiddenLayer, Robust Intelligence), or specialized AI-security consultancies.

**Case for:** Fastest path to senior-level fluency in LLM-specific security across the whole team. Candidates bring the OWASP LLM Top 10 / MITRE ATLAS / NIST AI 600-1 vocabulary, the tooling fluency (Garak, llm-guard, promptfoo), and — most usefully — pattern recognition from having seen production AI-security failures already. Skips the ramp.

**Case against:** Small candidate pool. The discipline is two to three years old as a named specialty; senior practitioners are concentrated at FAANG and a handful of vendors. Salary expectations track that scarcity — a senior AI-security engineer in the US runs $300K–600K fully loaded ([ToxSec — Promptfoo red-team economics analysis, 2026](https://www.toxsec.com/p/promptfoo-red-teaming)), so a 5-FTE team of externals is roughly $1.5M–$3M loaded TC per year. Candidates also lack DXC internals — customer constraints, the platform-team operating model, the compliance regime per customer, the 130k-employee context — and the **collective ramp risk** is non-trivial: 5 simultaneous 3–6 month onboardings against a moving regulatory landscape (EU AI Act phased rollout, NIST AI 600-1 updates) and a moving plugin-version surface (Kong 3.x rapid release cadence) means real coverage gaps in the first 6–9 months. Hiring market thinness compounds: shipping 5 senior AI-security hires inside 6 months is not realistic in any market we are aware of.

**Verdict:** Wrong answer at this scale. The market cannot supply the team fast enough, the ramp risk is high, and DXC loses the institutional-knowledge advantage.

### (b) Build the team by promoting/stretching internal AppSec engineers

**Shape:** Lead + 3–4 engineers, all internal stretch assignments from DXC's existing AppSec function with structured upskilling.

**Case for:** Internal candidates already know DXC's compliance regime, the customer constraints, the platform-team relationships, and the per-region operating model. AppSec foundation (§3a) is present across the whole team. Cost is incremental, not net-new headcount. Retains institutional knowledge that external hires take 6–12 months to acquire.

**Case against:** §3b (LLM-specific) is a real ramp — three to six months of focused study, hands-on red-teaming, and shadowing per engineer, before any of them are operating at senior fluency. The lead-level role is the bottleneck: an AppSec engineer promoted to lead an enterprise AI-security team for a 130k-employee company without prior staff/principal scope is being set up to fail on stakeholder management alone, regardless of their technical fluency. The temperamental-fit issue is real and rare: AppSec backgrounds skewed toward "we can patch this" struggle with prompt-injection-as-unsolvable, and the pool of AppSec engineers genuinely interested in making the LLM-security jump is small. Concrete ramp curriculum per engineer: OWASP LLM Top 10 + LLM01 deep reading; MITRE ATLAS walkthrough; the bypass-research papers (arXiv 2504.11168 and follow-ons); Garak + promptfoo + llm-guard hands-on against a sandbox Kong AI Gateway; pairing with Axle on every plugin-chain PR for 90 days; rotation through the SIEM/SOC partnership.

**Verdict:** Wrong answer as a pure path. DXC does not have a staff/principal AI-security IC internally (if it did, Jesse would have named them already), so the lead role cannot be filled from internal stretch alone. The internal stretch is the right *engineer-level* play, not the right *lead-level* play.

### (c) Hybrid — external staff/principal lead + internal stretch engineers + external red-team specialist + bridge consultancy (RECOMMENDED)

**Shape:** **1 external lead** (net-new hire OR senior contractor with a conversion path) + **3–4 internal stretch engineers** from DXC AppSec + **1 external dedicated AI red-team engineer** (specialty too rare internally) + **a 90-day overlap engagement with a fractional AI-security consultancy** for knowledge transfer during the lead's onboarding.

**Case for:**
- **Lead-level expertise where the market has it.** The staff/principal AI-security IC pool — small as it is — is the right shape for the role; buying it externally is the only viable path.
- **DXC context where DXC has it.** The 3–4 internal stretch engineers carry the compliance-regime, customer-constraint, and platform-team-relationship knowledge from day one. Their LLM-specific ramp happens in parallel with the lead's DXC-context ramp; both 3–6 months.
- **Red-team specialty bought externally.** Adversarial AI / red-team is a distinct sub-specialty (Garak/promptfoo/llm-guard fluency, jailbreak-research awareness, adversarial-mindset temperament). Stretching an internal AppSec engineer into red-team alongside the rest of the LLM ramp doubles the ramp time. Cheaper and faster to hire externally for this one specific role.
- **Bridge engagement front-loads knowledge transfer.** A fractional AI-security consultancy (named candidates: HiddenLayer, Protect AI's professional services, Lakera's deployment team, smaller boutiques) for 90 days during the lead's onboarding produces the initial threat models, the bypass dossier baseline, and the red-team baseline. The internal engineers shadow; the lead orchestrates. By day 90, the consultancy hands off and the team owns it.

**Case against:**
- Hybrid sourcing is operationally harder than a single sourcing path. Three different hiring tracks (external lead, internal stretch, external red-team specialist) plus a procurement track for the consultancy = four concurrent workstreams Nolan and the hiring function must coordinate.
- The lead must be a *strong* external hire — a weak external lead can't make the internal stretch engineers feel respected, and the team falls apart inside 12 months. Hiring rigor for the lead role is non-negotiable.
- The consultancy bridge has a known failure mode: producing handsome deliverables that gather dust because the internal team cannot operationalize them. Mitigation: the contract specifies *pair-programming-style* delivery, with the lead and named engineers attached to every consultancy artifact.

**Verdict: Highest-probability-of-success path at 130k-employee scale.** Recommend explicitly.

### Strongest single recommendation (revised)

**Path (c) — hybrid sourcing for a team of 5 (lead + 3 engineers + 1 red-team specialist), with a 90-day consultancy bridge during the lead's onboarding.**

Headline team shape: **1 external staff/principal lead + 3 internal AppSec stretch engineers + 1 external AI red-team engineer = 5 FTE**, with a 4th internal engineer as a stretch goal for the first 12 months once the team's standards and review cadence have stabilized.

**Where this is wrong:** if DXC's central CISO function already has a staff/principal-level AI-security IC who could step into the lead role internally (a possibility Jesse should confirm or rule out — see §10), the external lead hire becomes an internal stretch and Path (b)-with-tweaks becomes viable. This is the single fact most likely to change the recommendation.

## 8. Federation model — how the team is shaped across DXC

At 130k-employee scale, with a single gateway routing all AI traffic across every region DXC operates in, a centralized team cannot keep up with per-region compliance overlays and a fully federated team produces inconsistent standards a regulator will not accept. The pattern is well-established in enterprise security generally — central CISO + BISO ("Business Information Security Officer") / embedded liaisons model is the named industry pattern at this scale ([GitLab / The New Stack — federated security at scale, 2026](https://thenewstack.io/federate-security-gitlab/), [Gartner — CISO FAQs on AI Governance and team sizing](https://www.gartner.com/en/cybersecurity/cybersecurity-faqs), [TechTarget — BISO role in scaling CISO function across business units](https://www.techtarget.com/searchSecurity/feature/How-BISOs-enable-CISOs-to-scale-security-across-the-business), [Cisco Blogs — Domains and Organizational Functions of AI Security](https://blogs.cisco.com/ai/the-domains-and-organizational-functions-of-ai-security)) — and the same pattern transfers to AI security.

The split:

### Central AI-security team responsibilities

- **Enterprise standards.** The plugin-chain standards, redaction-category standards, rate-limit-budget standards, audit-event contract, SIEM integration contract. One document set, owned by the lead.
- **Plugin-chain review board.** Every new plugin entering the catalog, every plugin upgrade across a minor version. Chaired by the lead with team-engineer board members.
- **AI red-team program ownership.** Cadence, scope, success criteria, disclosure path. The dedicated red-team engineer executes; the lead owns the program.
- **Lead investigations.** Any incident where bypass crosses regions, where an `ai-sanitizer` redaction failure surfaces in production, where a new MITRE ATLAS technique lands and needs threat-model propagation across the route catalog.
- **Cross-region coordination.** Weekly sync with regional liaisons; standards revisions; vendor-bypass-research monitoring.
- **Relationship with central DXC CISO and DXC legal.** The lead is the named counterpart; engineers may be pulled in for specific topics.

### Embedded / liaison responsibilities (per-region or per-major-BU)

- **Local compliance overlay.** Which redaction categories are active per region; which audit-event retention windows apply per regime; which SIEM landing zone per region. Liaisons author the regional overlay against the central standard.
- **Local incident response coordination.** First-line response on region-specific incidents; escalation to central team for cross-region findings.
- **Local regulator engagement.** GDPR DPO interaction in the EU; sector-specific regulator interaction (FCA, OCC, ASIC, etc.) where DXC's customer base demands it.
- **Local route reviews.** Per-route threat-model authoring for routes serving primarily that region's traffic; central peer review on PR.

**Note on the liaison role at this team size:** the math suggests the 3–4 engineers under the lead serve as the liaisons themselves at first, each carrying a region or BU bucket — not net-new regional headcount. As route volume grows and a region's compliance burden exceeds one engineer's capacity, a region-specific AI-security engineer (sitting *inside* the regional CISO function with a dotted line back to the central team) becomes the next hire. This is also the standard BISO scaling pattern.

### Reporting line — central CISO with dotted line to gateway practice (recommended)

Two viable options:

- **(i) Report into the gateway practice tech lead** with dotted line to central DXC CISO.
- **(ii) Report into central DXC CISO** with dotted line to the gateway practice tech lead.

**Trade-off:** Option (i) prioritizes day-to-day coordination with Axle's gateway-engineering team — fast turnaround on plugin-rule PRs, tight feedback loops on threat models, shared on-call posture for AI-route incidents. The cost is that AI-security standards drift away from central CISO standards over time, and at a regulator audit the central CISO cannot defend gateway-specific deviations. Option (ii) prioritizes enterprise alignment — AI-security standards stay defensibly part of DXC's overall security posture, and the central CISO can present a single story to regulators across the 130k-employee footprint. The cost is slower turnaround on plugin-rule decisions and a higher risk of the AI-security team becoming a bottleneck for the gateway-engineering team.

**Recommendation: Option (ii) — report into central DXC CISO with a strong dotted line to the gateway practice.** Three reasons:
1. At 130k-employee scale, regulator-facing alignment matters more than per-plugin-PR turnaround.
2. Federated security governance models specifically position security under the CISO with embedded BU implementation ([GitLab / The New Stack — federated security at scale](https://thenewstack.io/federate-security-gitlab/), [TechTarget BISO article](https://www.techtarget.com/searchSecurity/feature/How-BISOs-enable-CISOs-to-scale-security-across-the-business)) — DXC inherits this pattern, doesn't reinvent it.
3. The dotted line to the gateway practice can be operationalized via SLA: the AI-security team commits to plugin-rule-PR turnaround of, e.g., 1 business day for routine reviews and same-day for security-incident triage. This addresses Option (i)'s real concern without sacrificing the central-CISO benefits.

**Caveat — single recommendation, not unanimous best practice:** Option (i) is genuinely viable and is sometimes the better fit when the gateway practice is itself reporting into the CISO function (in which case (i) and (ii) collapse). The recommendation assumes the gateway practice reports into a CTO/engineering line, not a CISO line. **Jesse should confirm DXC's structure here** — see §10.

### Decision rights — central vs. delegated

| Decision | Owned by | Notes |
|---|---|---|
| Enterprise AI-security standard | Central (Lead) | One document, all routes, all regions |
| Plugin-chain review board verdict | Central (Lead chairs, engineers vote) | Required gate for new plugins / version bumps |
| AI red-team program scope and cadence | Central (Lead + red-team engineer) | Liaisons feed in regional concerns |
| Region-specific redaction-category overlay | Liaison (with central peer review) | Liaison authors against central standard |
| Region-specific audit retention overlay | Liaison (with central peer review) | Must comply with regional regime |
| Per-route threat model | Engineer assigned (with central peer review) | Lives in IaC repo alongside decK YAML |
| Incident triage — single region | Liaison | Escalate to central for cross-region patterns |
| Incident triage — cross-region | Central (Lead leads) | Coordinate with central CISO |
| Vendor red-team tool selection | Central (Lead + red-team engineer) | Standardize the tool surface |
| Vendor bypass-research monitoring | Central (Lead) | Feeds standards updates |
| Regulator engagement — local | Liaison | With central CISO co-sign |
| Regulator engagement — cross-region | Central (Lead with central CISO) | Single story |

## 9. Tools the role needs (host subagent shim)

*(Unchanged from v1 — applies to the lead's subagent shim. Team-engineer and red-team-engineer shims would carry the same toolset.)*

Minimal list, justified:

- **Bash** — runs Garak probes, llm-guard scans, promptfoo eval suites, `deck diff` for reviewing plugin-rule changes, container builds for the AI PII Sanitizer sidecar. Non-negotiable.
- **Read** — reads existing threat models, plugin configs, runbooks, IaC. Standard.
- **Write** — authors threat models, redaction policies, red-team reports, runbook contributions, standards documents. Standard.
- **Edit** — surgical edits to existing plugin rule files, vault-reference fixes, threat-model updates.
- **WebFetch** — pulls Kong plugin docs (which move release-to-release), OWASP LLM Top 10 updates, MITRE ATLAS technique pages, NIST publications, vendor advisories on guardrails bypasses.
- **WebSearch** — researches new bypass techniques, CVEs against LLM tooling, plugin-version-pinning checks against the moving Kong 3.x cadence.
- **Glob** — navigates the IaC repo to find every route's plugin chain.
- **Grep** — searches plugin configs for forbidden patterns (plaintext secrets, missing sanitizer references, wrong plugin order).

**Tooling the team uses outside the subagent shim** (real human tooling, not Claude Code tooling): Garak ([NVIDIA, Apache 2.0, latest v0.15.0 May 2026](https://github.com/NVIDIA/garak)), Protect AI's llm-guard ([MIT, active](https://github.com/protectai/llm-guard)), promptfoo (red-team module, CI-friendly — **note: acquired by OpenAI on 2026-03-09; the OSS project continues but ownership-concentration risk is a new staleness signal, particularly given OpenAI's first-party guardrails offerings**), PyRIT ([Microsoft, MIT — **note: archived 2026-03-27, treat as historical**](https://github.com/Azure/PyRIT) — a real staleness signal: PyRIT was the Microsoft AI Red Team's flagship and its archival means the team should source from current alternatives), a sandbox Kong AI Gateway for hands-on validation, and a scratch vector store for indirect-injection RAG-corpus tests.

**Not needed:** image tools (Pixel / Charta), MCP server tooling (Mack handles connectors), database administration (Silas).

## 10. Open questions for Jesse — revised for enterprise scope

These would sharpen the hiring decision:

- **Does DXC's central CISO function already have a staff/principal-level AI-security IC** who could step into the lead role internally? This is the single fact most likely to flip the recommendation from Path (c) (hybrid with external lead) to Path (b)-with-tweaks (internal stretch for the lead role, hybrid for the rest).
- **DXC's current AppSec team size and structure** — feeds Path (b)/(c) feasibility math. How many AppSec engineers globally? What is the typical career path out of AppSec at DXC? Which regions have AppSec engineers who could be candidates for the stretch assignment?
- **Existing AI red-team capability anywhere in DXC** — research labs, customer-services / managed-security-services arms, central CISO red-team function. Partner vs. build. If a capability exists, the recommendation shifts from "hire 1 external red-team engineer" to "partner with existing function, with one named liaison FTE on the AI-security team."
- **SIEM/SOC integration: which DXC-internal SOC will receive `ai-sanitizer` and File Log audit events?** Single global SOC, or per-region SOCs federated under a global function, or a mix? This determines the event-contract design and whether per-region liaisons need direct relationships into regional SOCs.
- **Regulatory framework prioritization at the company level.** EU AI Act exposure is significant for a multinational and may pull forward certain controls (record-keeping, traceability, risk-management documentation) that affect plugin-chain standards. Which regimes does DXC's legal function consider priority-1 for the first 12 months?
- **Reporting line for the AI-security lead.** Gateway practice (Option (i) in §8) vs. central DXC CISO with dotted line (Option (ii), recommended). Confirmation here unblocks Nolan's contract draft.
- **Budget shape.** Sized for a team of 4–5 FTE plus a dedicated red-team engineer (~5 FTE total), plus a 90-day consultancy engagement, not a single stretch hire. Rough sizing: 5 senior FTE in a US-cost-loaded model lands at ~$1.5M–$3M loaded TC per year ([ToxSec — Promptfoo red-team economics, 2026](https://www.toxsec.com/p/promptfoo-red-teaming)); DXC's global distribution likely reduces the average. Consultancy bridge sized at $200K–$500K for 90 days at the senior-AI-security-firm tier.
- **Time-zone strategy for the team.** Does the team co-locate (e.g., all US-based) and accept the per-region liaison as a remote model, or distribute the team itself across time zones (e.g., one engineer EMEA-based for follow-the-sun review coverage)? Follow-the-sun matters more for plugin-rule PR turnaround than for incident response, which the regional SOC handles first-line.
- **First production-route customer mix.** Which DXC business units' first-AI-route customers are most regulator-sensitive? This determines whether the first 90 days prioritize EU AI Act, HIPAA, or financial-services regulatory work.
- **Naming preference for the lead.** Working title is Vex (see §11). Larry can substitute.

## 11. Suggested name candidates

*(Unchanged — names apply to the AI Security Lead specifically; team-engineer naming is downstream of Nolan's contract draft.)*

All checked against the existing nine (Larry, Nolan, Pax, Penn, Mack, Silas, Charta, Pixel, Iris) and the tenth (Axle). No collisions.

- **Vex** (working title) — short, sharp, connotes adversarial / probing posture. Single syllable, distinct. Fits "red-team mindset" and reads at lead/principal level when paired with a small team.
- **Sentry** — guarded posture, watchful. Slightly longer; very clear role association.
- **Cipher** — cryptographic / security signal. Distinct, slightly mysterious.
- **Redact** — directly evokes the PII-sanitizer / log-redaction part of the job. Slightly on-the-nose but memorable.
- **Warden** — guardian framing without being generic. Two syllables; reads as senior — possibly the strongest fit now that the role is lead-level rather than IC.

Pax recommendation, revised: **Warden** has moved up given the lead-level scope (the name reads as more senior than Vex, and pairs with Axle in a different register — Axle drives, Warden polices). **Vex** still works if the team prefers the adversarial-thinking framing. Larry's call.

## Sources

### Primary — frameworks and standards

- [OWASP Top 10 for LLM Applications 2025 — home](https://genai.owasp.org/llm-top-10/)
- [OWASP LLM01:2025 Prompt Injection](https://genai.owasp.org/llmrisk/llm01-prompt-injection/)
- [MITRE ATLAS — home](https://atlas.mitre.org/) (v5.4.0 as of February 2026: 16 tactics, 84 techniques, 56 sub-techniques)
- [Vectra — MITRE ATLAS overview](https://www.vectra.ai/topics/mitre-atlas)
- [Startup Defense — AML.T0051 LLM Prompt Injection reference](https://www.startupdefense.io/mitre-atlas-techniques/aml-t0051-llm-prompt-injection)
- [NIST AI Risk Management Framework — home](https://www.nist.gov/itl/ai-risk-management-framework) (AI RMF 1.0 January 2023; Generative AI Profile AI 600-1 July 2024)
- [EU AI Act — artificialintelligenceact.eu](https://artificialintelligenceact.eu/)

### Primary — Kong AI Gateway documentation

- [Kong AI Prompt Guard plugin](https://developer.konghq.com/plugins/ai-prompt-guard/)
- [Kong AI Semantic Prompt Guard plugin](https://developer.konghq.com/plugins/ai-semantic-prompt-guard/)
- [Kong AI PII Sanitizer plugin (`ai-sanitizer`)](https://developer.konghq.com/plugins/ai-sanitizer/)
- [Kong AI Rate Limiting Advanced plugin](https://developer.konghq.com/plugins/ai-rate-limiting-advanced/)
- [Kong Gateway Secrets Management](https://developer.konghq.com/gateway/secrets-management/)
- [Kong AI Gateway product page](https://konghq.com/products/kong-ai-gateway)
- [Kong blog — AI Gateway 3.10 release, April 2025](https://konghq.com/blog/product-releases/ai-gateway-3-10) (**note: claims "20+ PII categories across 12 languages"; plugin docs as of 2026-05 list 17 field types and 9 languages — flagged inline in §3c**)

### Primary — Red-team and guardrails tooling

- [Garak — NVIDIA, GitHub](https://github.com/NVIDIA/garak) (Apache 2.0, latest release v0.15.0 dated 2026-05-01)
- [llm-guard — Protect AI, GitHub](https://github.com/protectai/llm-guard) (MIT, active)
- [promptfoo red-team docs](https://www.promptfoo.dev/docs/red-team/) — **note: promptfoo acquired by OpenAI on 2026-03-09; OSS project continues; ownership-concentration risk worth tracking**
- [PyRIT — Microsoft AI Red Team, GitHub](https://github.com/Azure/PyRIT) (MIT; **note: repository archived 2026-03-27 — historical reference only**)
- [ToxSec — Promptfoo Red Teaming: DAST for Your LLM Pipeline, 2026](https://www.toxsec.com/p/promptfoo-red-teaming) (practitioner write-up; cited for AI-security team cost benchmarks)

### Primary — enterprise federation and SIEM integration (added in this revision)

- [The New Stack — The one structural shift CISOs must make before AI outpaces their security strategy (GitLab, 2026)](https://thenewstack.io/federate-security-gitlab/) — federated security governance at multinational scale
- [Gartner — CISO FAQs: AI Governance and Cybersecurity Strategies](https://www.gartner.com/en/cybersecurity/cybersecurity-faqs) — cybersecurity FTE-as-percent-of-IT benchmark and federated-model framing
- [TechTarget — How BISOs enable CISOs to scale security across the business](https://www.techtarget.com/searchSecurity/feature/How-BISOs-enable-CISOs-to-scale-security-across-the-business) — central-CISO + embedded-liaison pattern
- [Cisco Blogs — The Domains and Organizational Functions of AI Security](https://blogs.cisco.com/ai/the-domains-and-organizational-functions-of-ai-security) — AI-security organizational design
- [Microsoft / Security Insider — AI Security Guide, Data Governance and Security](https://www.microsoft.com/en-us/security/security-insider/emerging-trends/ai-security-guide-data-governance-and-security) — LLM audit logs into SIEM (Sentinel/Splunk)
- [Radiant Security — SIEM 2026 guide](https://radiantsecurity.ai/learn/siem-security-information-and-event-management-explained-2026-guide/) — SIEM architecture at enterprise scale
- [Conifers AI — The Enterprise AI SOC: A CISO's Guide From Pilot to Production in 2026](https://www.conifers.ai/blog/the-enterprise-ai-soc-a-cisos-guide-from-pilot-to-production-in-2026) — AI SOC integration model

### Third-party / practitioner cross-checks (independent of Kong)

- [arXiv 2504.11168 — Bypassing Prompt Injection and Jailbreak Detection in LLM Guardrails, April 2025](https://arxiv.org/html/2504.11168v1)
- [HackRead — HiddenLayer bypass of OpenAI Guardrails, October 2025](https://hackread.com/openai-guardrails-bypass-prompt-injection-attack/)
- [GuidePoint Security — Prompt Injection: The AI Vulnerability We Still Can't Fix, August 2025 (Sarah Kent)](https://www.guidepointsecurity.com/blog/prompt-injection-the-ai-vulnerability-we-still-cant-fix/)
- [Simon Willison — recurring writing on prompt injection unsolvability](https://simonwillison.net/) (practitioner-authority cross-check on the "guardrails are not a solution" thesis)
- [Lakera — Guide to Prompt Injection, April 2026](https://www.lakera.ai/blog/guide-to-prompt-injection) (**vendor source — Lakera sells Lakera Guard; product claims are not neutral. Taxonomy and incident examples are useful; "you need our product" framing is not.**)

### Triangulation and staleness notes

**Triangulation:** Every load-bearing claim is supported by at least one primary source (OWASP, MITRE, NIST, Kong) plus at least one independent third party (arXiv research, practitioner write-up, vendor cross-check). Three claims with explicit per-claim verification:

1. *Prompt injection cannot be fully eliminated by gateway-level filtering.* Primary: OWASP LLM01 itself names defense-in-depth, not elimination. Independent: arXiv 2504.11168 (April 2025) empirically; HackRead (October 2025) OpenAI Guardrails bypass; Simon Willison and GuidePoint as practitioner consensus. **Confidence: High.**
2. *Kong `ai-prompt-guard` has documented unicode-smuggling limitations.* Primary: Kong's own plugin docs name the limitation. Independent: arXiv 2504.11168 empirically demonstrates 100% evasion via character injection against named commercial guardrails. **Confidence: High.**
3. *Kong AI PII Sanitizer field-type and language counts.* Plugin docs vs Kong's 3.10 release blog disagree (17 fields / 9 languages in docs vs 20+ fields / 12 languages in blog). **Confidence: Medium — re-verify against the active plugin version at config time. Treat the docs count as load-bearing, the blog count as marketing.**

**New in this revision — federated-team-shape claims:**

4. *Central CISO + embedded BU liaisons is the named industry pattern at multinational scale.* Triangulated across The New Stack (GitLab, 2026), Gartner CISO FAQs, TechTarget BISO article, and Cisco Blogs (AI security organizational functions). **Confidence: High** — pattern is consistent across four independent sources covering security generally and AI security specifically.
5. *Senior AI-security engineer US loaded TC range $300K–$600K.* Source: ToxSec promptfoo economics analysis (2026). **Confidence: Medium — single source for the specific dollar range; the qualitative "AI-security senior talent is scarce and expensive" claim is multi-source.** Re-verify against DXC's compensation bands before locking budget.
6. *Promptfoo acquired by OpenAI on 2026-03-09.* Source: promptfoo GitHub project page. **Confidence: Medium — single source for the specific date; cross-source confirmation recommended before naming the date in stakeholder communication.** The fact of acquisition is broadly reported; the date is single-sourced here.

**Staleness — AI security moves faster than Kong itself:**

- MITRE ATLAS v5.4.0 dated February 2026; new techniques landed in the 12 months prior. Re-check on quarterly cadence.
- OWASP LLM Top 10 2025 is the current version; 2026 revision may appear within the brief's life. Re-check on announcement.
- Kong AI Gateway plugin behavior changed materially across 3.8 → 3.10 → 3.12 → 3.14 (response sanitization, model-based rate limits). Pin versions; re-check plugin docs on every Kong upgrade.
- **PyRIT archival (2026-03-27)** is a concrete staleness signal: tooling the team would have reached for first is no longer maintained. Source from current alternatives (Garak v0.15.0 is current; promptfoo and llm-guard are active).
- **Promptfoo / OpenAI acquisition (2026-03-09)** is a new staleness/concentration signal: the OSS red-team toolchain is consolidating under model-vendor ownership. Track for license, governance, and roadmap changes that may affect red-team tooling neutrality. Keep Garak (NVIDIA) and llm-guard (Protect AI) as alternatives to avoid single-vendor lock-in.
- Guardrails-bypass research is fast-moving — the named systems bypassed in arXiv 2504.11168 (Azure Prompt Shield, Protect AI v1/v2, Meta Prompt Guard, NeMo Guard, Vijil) all have shipped updates since; treat the *technique classes* (character injection, AML evasion, transferability) as the durable finding, not the per-product results.
- EU AI Act enforcement is in phased rollout; obligations on GPAI providers and downstream deployers shift quarterly. Track with DXC legal — particularly relevant given DXC's EU footprint as a 130k-employee multinational.
- Federated-security-governance patterns are stable conceptually but the specific BISO / embedded-liaison job titles and reporting lines vary by industry. The pattern transfers; the specific titles may not match DXC's internal taxonomy.

This brief reflects sources dated 2023–2026, with the bulk of the load-bearing material from 2024–2026. Treat any claim older than 18 months as candidate-for-refresh.
