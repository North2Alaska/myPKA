# SOP-011 - Grill a Plan with Docs

- **Status:** Active (since 2026-05-15)
- **Default owner:** Larry
- **Reusable by any agent.** This is a skill, not a 1:1 ownership. Pax can invoke this SOP when the grill is anchored to outside research. Any specialist who needs to stress-test a plan against its own documented language can run this procedure.
- **Triggered by:** "grill me", "grill this plan", "stress-test this", "challenge this", "pressure-test this", "interrogate this design", the `/grill` slash command.
- **References:** [[GL-001-file-naming-conventions]], [[GL-005-adr-format]], [[SOP-write-session-log]].

## Purpose

Take a plan, decision, or design direction and challenge it through one-question-at-a-time interviewing. Sharpen the language as terms get used. Capture resolutions into a document of the user's choosing — and only promote standalone ADRs when a decision is genuinely durable and worth recording.

The procedure is Socratic. It does not invent answers. The user supplies the plan; this SOP forces precision and surfaces ambiguity.

## What this SOP does not do

- Does not write the user's plan for them. The plan is an input; the grill challenges it.
- Does not assume a canonical project doc exists (no hardcoded `CONTEXT.md`). The user names the target file each session.
- Does not produce ADRs for every decision. The ADR bar is intentionally high — most grills produce zero ADRs.
- Does not write code, run tests, or change anything outside the target document and its sibling `-adr/` folder.

## Inputs

- **The topic.** What the user wants grilled — a plan, decision, design sketch, methodology, or open direction.
- **The target file.** A path to the document the grill will write into. May be new (Larry creates it) or existing (Larry reads it first). Required before Step 3.
- **Optional supporting context.** Pasted text, linked files, or pointers to existing project docs and prior `-adr/` folders.

## Step-by-step procedure

### Step 1 — Establish the target file

Every grill anchors to a document. Ask the user one question to determine the target:

> "Which document should I capture this grilling into? Paste a path, or tell me to create a new one."

Three paths:

1. **User names an existing file.** Larry confirms it exists. If it doesn't, fall through to path 2.
2. **User says "create one."** Larry suggests a default path per [[GL-001-file-naming-conventions]]: `Deliverables/YYYY-MM-DD-grill-<topic-slug>.md`. The user may accept or rewrite the path. Larry creates the file with an empty skeleton (title plus empty `## Language`, `## Decisions`, `## Open questions` sections).
3. **User names a future path that doesn't exist yet.** Same as path 2 but at the user's chosen location.

Confirm the target before continuing.

### Step 2 — Read what's already there

If the target file existed before this session, read it cover-to-cover. Note:

- Any existing `## Language` / glossary entries.
- Any existing `## Decisions`.
- Any existing `## Open questions`.
- Any sibling `<target-slug>-adr/` folder — read every ADR inside it.
- Any wikilinks to other Team Knowledge files (Guidelines, SOPs, Workstreams) that constrain the topic.

If the file is new, skip to Step 3.

### Step 3 — Interview, one question at a time

Walk down the design tree branch by branch, resolving dependencies as you go. For each question:

1. Pose **one** question. Not three. Not a bulleted list.
2. Offer your recommended answer with a sentence of reasoning.
3. Wait for the user's response before continuing.

If a question can be answered by reading the codebase or an existing doc, read first. Don't ask the user what the code already says.

### Step 4 — Challenge against existing language

When the user introduces a term that conflicts with something already in `## Language` of the target file (or any read-in ADR), call it out immediately:

> "You used 'cancellation' here, but the doc defines it as X — and you seem to mean Y. Which is it?"

Resolve the conflict before moving on.

### Step 5 — Sharpen fuzzy language

When the user uses a vague or overloaded term, propose a precise canonical term:

> "You're saying 'account' — do you mean the Customer or the User? Those are different things here."

Once the user picks, add the canonical term to `## Language` in the target file inline. Note the aliases-to-avoid alongside it.

### Step 6 — Stress-test with scenarios

When relationships are being discussed, invent specific scenarios that probe the edges:

> "What happens when an Order has been partially shipped and the Customer cancels the unshipped lines? Does the original Order still exist, or do we split it?"

Edge-case scenarios force precision. Capture the resolutions in `## Relationships` or `## Decisions` as they happen.

### Step 7 — Cross-reference with code (when applicable)

If the project has a codebase, check whether stated behavior matches reality. Surface contradictions immediately:

> "You just said partial cancellation is possible, but the code in `orders.py` cancels the whole order atomically. Which is right — the intent or the code?"

### Step 8 — Capture into the target file inline

Do not batch updates. As terms resolve and decisions firm up, write them into the target file under named sections:

- `## Language` — every term that got sharpened, in `**Term**: definition. _Avoid_: alias1, alias2` shape.
- `## Relationships` — bulleted statements of how concepts connect, with cardinality where obvious.
- `## Decisions` — every decision the user committed to. One short paragraph each. This section is the holding pen for potential ADR promotion in Step 9.
- `## Open questions` — anything unresolved at the end of a branch.

Format ADR-shaped decisions inside `## Decisions` per [[GL-005-adr-format]] — same shape, just inline. Promotion in Step 9 only moves the content to a standalone file.

### Step 9 — End-of-grill ADR review

When the interview winds down, scan every entry in `## Decisions` against the three-criteria bar from [[GL-005-adr-format]]:

1. **Hard to reverse** — the cost of changing your mind later is meaningful.
2. **Surprising without context** — a future reader will wonder "why on earth did they do this?"
3. **The result of a real trade-off** — there were genuine alternatives and you picked one for specific reasons.

If **all three** are true for a decision, offer to promote it to a standalone ADR:

> "Decision #3 — using HCP Vault behind KSM — hits the bar (hard to reverse, surprising without the multi-cloud context, real trade-off against split AKV/ASM). Promote to a standalone ADR?"

If the user accepts:

1. Lazily create the sibling folder: `<target-file-slug>-adr/`. Folder name is the target file's slug plus `-adr`, all lowercase per [[GL-001-file-naming-conventions]] §1. Only create the folder when the first ADR for this target is being promoted.
2. Pick the next ADR number by scanning the folder (zero-padded three digits, no skips on write). First ADR in a doc is `ADR-001-`.
3. Write the ADR at `<target-file-slug>-adr/ADR-NNN-<slug>.md` using the format in [[GL-005-adr-format]].
4. Replace the inline `## Decisions` entry in the target file with a one-line summary plus a `[[wikilink]]` to the ADR.

If the user declines, leave the decision inline in `## Decisions`. That's the expected outcome for most decisions.

If a decision misses any of the three criteria, do not offer the ADR — it stays inline.

### Step 10 — Session-log entry

Write the session log per [[SOP-write-session-log]] under `Team Knowledge/session-logs/YYYY/MM/`. Capture:

- Target file path.
- Topic grilled.
- New `## Language` terms resolved (count plus a few examples).
- Decisions captured inline (count).
- ADRs promoted (count plus paths).
- Open questions left.
- Any conflict surfaced between the user's framing and existing docs.

## Common mistakes to avoid

- **Batching questions.** Three questions at once gives the user permission to answer the easiest one and skip the rest. One at a time.
- **Updating the target file at the end of the session.** Decisions get hazy. Capture inline as they happen.
- **Offering ADRs liberally.** The three-criteria bar is intentionally hard. Most decisions stay inline.
- **Writing ADRs into the target file's parent folder** instead of the per-document `<slug>-adr/` folder. The folder is the unit of ADR scope.
- **Inventing answers the user didn't give.** This is an interview, not a brainstorm. If the user is silent, ask again — don't fill in.
- **Skipping Step 2.** Reading the existing target and any sibling `-adr/` folder is what makes Step 4 work. Without it, you'll surface no conflicts.
