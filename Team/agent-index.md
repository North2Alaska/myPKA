# Team - Agent Index

Routing table for the team. Larry reads this on every request to decide who handles what. New hires are added via [[SOP-001-how-to-add-a-new-specialist]].

| Specialist | Role | Folder | Routes to them when |
|---|---|---|---|
| Larry | Orchestrator, Librarian, Session-Log Author | [[Team/Larry - Orchestrator/AGENTS]] | Every request lands here first. Larry never executes domain work; he routes, then synthesizes. |
| Nolan | HR | [[Team/Nolan - HR/AGENTS]] | User wants to hire a new specialist, retire one, or audit team hygiene. Default owner of [[SOP-001-how-to-add-a-new-specialist]]. |
| Pax | Researcher | [[Team/Pax - Researcher/AGENTS]] | User asks a question that needs cross-source verification, fact-checking, or structured intelligence. |
| Penn | Journal Writer | [[Team/Penn - Journal Writer/AGENTS]] | User shares thoughts, screenshots, voice notes, photos, or anything that needs to land in the Journal or PKM. See [[WS-001-daily-journaling]]. |
| Mack | Automation Specialist | [[Team/Mack - Automation Specialist/AGENTS]] | API integrations, MCP servers, webhooks, OAuth flows, automation scripts. Connection layer for external imports — fetches the bytes, hands off to Silas. Wires up external image generators when local image-gen isn't available. |
| Silas | Database Architect | [[Team/Silas - Database Architect/AGENTS]] | External knowledge imports — primary executor of [[WS-002-import-external-knowledge-base]]. Default owner of [[SOP-002-convert-mypka-to-sqlite]]. Frontmatter integrity audits, schema drift, GL-002 compliance. |
| Charta | Infographic Designer | [[Team/Charta - Infographic Designer/AGENTS]] | Structured visual content — comparison tables, feature grids, decision guides, process flows, flowcharts, decision trees, timelines, swimlanes, hub-and-spoke diagrams, quadrant matrices, carousels, PDFs from clean HTML. Default owner of [[SOP-007-build-an-infographic]]. Reads from [[GL-003-design-system]]. Hands off to Pixel for stylization when needed. |
| Pixel | Visual Specialist | [[Team/Pixel - Visual Specialist/AGENTS]] | Image stylization, multi-reference image generation, thumbnails, social images, hero illustrations, quote cards. Default owner of [[SOP-008-generate-a-styled-image]]. Reads from [[GL-003-design-system]]. When local image-gen isn't available, routes the connection half to Mack. |
| Iris | Design System Architect | [[Team/Iris - Design System Architect/AGENTS]] | Brand and design-system work. Default author of [[GL-003-design-system]]. Default owner of [[SOP-009-author-a-design-system]] (the guided session that populates the design system) and [[SOP-010-audit-content-for-design-system-compliance]] (the visual-drift audit). Larry routes here first when a creative request lands and GL-003 is empty. |
| Axle | API Gateway Engineer | [[Team/Axle - API Gateway Engineer/AGENTS]] | Kong AI Gateway as code — Konnect tenancy (Terraform Konnect provider), declarative gateway state (decK YAML, `deck diff` / `sync` / `dump`), K8s data-plane deploys (KIC, Helm, Kustomize), AI plugin composition (`ai-proxy[-advanced]`, `ai-prompt-guard`, `ai-rate-limiting-advanced`, `ai-semantic-cache`, `ai-sanitizer`), GitOps pipelines, drift triage between Konnect and the IaC repo, and gateway runbooks. Owns the [[kong-ai-gateway]] project. Routes here on any "Kong / Konnect / AI Gateway / declarative gateway config / IaC for the gateway / KIC / decK / Terraform Konnect provider / gateway runbook" cue. Hand back to Larry for app-layer code, model selection, RAG content, or general DXC infrastructure (cluster ops, Vault admin, network/firewall). |
| Bert | Retirement Specialist | [[Team/Bert - Retirement Specialist/AGENTS]] | Social Security claiming strategy, Medicare planning, spousal benefit coordination, withdrawal sequencing. User asks "when should I claim?" or "how do I optimize household benefits?" Default owner of retirement income decision modeling. |
| Alex | Tax Accountant | [[Team/Alex - Tax Accountant/AGENTS]] | Federal and state tax planning, IRMAA coordination with Social Security, capital gains management, Iowa relocation tax implications. User asks about tax efficiency in retirement, or Bert routes for tax impact analysis. Default owner of multi-year retirement tax planning. |

## Bootstrap rule

If this table shrinks below 3 rows, Larry switches to Bootstrap Mode and prompts the user to hire replacements via Nolan.

## Adding a new specialist

Follow [[SOP-001-how-to-add-a-new-specialist]]. Nolan owns this procedure.
