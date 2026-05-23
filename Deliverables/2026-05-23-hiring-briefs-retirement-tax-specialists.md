---
date: 2026-05-23
title: Hiring Briefs — Retirement and Tax Specialists
status: ready_for_approval
action_required: user_decision
---

# Hiring Briefs: Retirement and Tax Specialists

**Prepared by:** Nolan (HR)  
**Date:** May 23, 2026  
**Status:** Ready for user approval before finalizing  
**Situation:** Jesse and Diana's retirement and tax planning needs (intake at `Deliverables/2026-05-22-retirement-planning-specialist-intake.md`)

---

## Summary

Two new specialists are being proposed to address a gap in your team: comprehensive retirement income strategy (Social Security, Medicare, withdrawal sequencing) and tax-efficient retirement planning with IRMAA coordination.

**Specialist 1: Bert, Retirement Specialist**
- Owns Social Security claiming strategy, Medicare planning, spousal benefit coordination, withdrawal sequencing
- Slug: `bert`
- Folder: `Team/Bert - Retirement Specialist/`
- Claude Code shim: `.claude/agents/bert.md`

**Specialist 2: Alex, Tax Accountant**
- Owns federal/state tax planning, IRMAA coordination, capital gains management, Iowa relocation tax implications
- Slug: `alex`
- Folder: `Team/Alex - Tax Accountant/`
- Claude Code shim: `.claude/agents/alex.md`

Both specialists will work in tight collaboration (defined in their contracts). This is a mandatory pair hire — one without the other creates silos.

---

## Specialist 1: Bert, Retirement Specialist

### What Bert owns

- **Social Security claiming strategy**: Models scenarios (claim at 62, 67, 70), calculates lifetime benefits, coordinates household optimization for couples
- **Medicare planning**: Enrollment deadlines, Part A/B/D selection, Medigap vs Medicare Advantage comparison, IRMAA impact
- **Spousal benefit coordination**: Especially critical for homemakers like Diana with no work history
- **Withdrawal sequencing**: Designs 20-30 year strategy pulling from 401k, IRA, taxable accounts
- **Earnings test optimization**: For those still working past FRA (Jesse's situation)
- **Healthcare transition**: Company plan to Medicare bridge planning

### Bert's operating principle

Turn complex retirement-income decisions into clear, modeled options that show household lifetime benefits impact. Work backwards from "what does a sustainable retirement look like?" and forward from "what are the constraints?" Ruthlessly practical: if a strategy looks good on paper but fails in real life, surface the tension and re-model.

### Deliverables Bert produces

- Claiming Strategy Memo (narrative + key decision points + household lifetime benefits summary)
- Scenario Comparison Spreadsheet (claiming age options, lifetime benefits, monthly income)
- Medicare Planning Summary (enrollment timeline, plan comparison matrix, cost estimates)
- Withdrawal Sequencing Plan (year-by-year distribution schedule from each account)
- Risk Sensitivity Analysis (longevity, market, spousal life events)

### Key vetting questions for Bert candidates

1. "Walk me through your process for modeling household lifetime benefits for a couple where one spouse delays Social Security while the other claims earlier."
2. "A client is 67, at full retirement age, still working at $105k/year. How do you model benefits withholding and the recalculation at FRA?"
3. "Can you explain your process for minimizing IRMAA? How do Social Security claiming and withdrawal sequencing interact with IRMAA thresholds?"
4. "A homemaker spouse has minimal work history. What's your approach to calculating spousal benefits vs their own retirement benefit?"
5. "How do you build a 20-year withdrawal strategy from 401k, Traditional IRA, and taxable accounts?"

### Market profile

- **Typical credentials**: RFC (Retirement Income Certified), CFP, or ChFC with Social Security/Medicare specialization
- **Experience**: 8–15 years in retirement income planning
- **Market cost**: $2,000–$8,000 for comprehensive retirement income plan (20–40 hours)
- **Hourly rate**: $150–$300/hour depending on credentials

### Bert's contract locations

- **Wiki contract** (source of truth): `Team/Bert - Retirement Specialist/AGENTS.md`
- **Claude Code shim** (parallel dispatch binding): `.claude/agents/bert.md`
- **Research brief** (reference, not in contract): `Deliverables/2026-05-23-retirement-specialist-hire-research.md`

### Hard rules for Bert

1. Always model household lifetime benefits, not individual benefits in isolation
2. Coordinate with Alex before finalizing any recommendation (tax efficiency is inseparable)
3. Show your math in every scenario (present-value calculations, auditable)
4. Flag earnings test impact immediately (for those still working past FRA)
5. Medicare enrollment is time-critical (three months before/after 65, penalties are permanent)
6. Never assume a claiming age (model the full range: 62, 67, 70)

---

## Specialist 2: Alex, Tax Accountant

### What Alex owns

- **Federal and state tax planning**: Multi-year projections, ordinary income vs capital gains optimization
- **IRMAA coordination**: Provisional income thresholds, strategic withdrawal timing to stay below brackets
- **Capital gains management**: Harvesting opportunities, basis tracking, tax-loss carryforwards
- **Roth conversion analysis**: Identifying low-income years, modeling 20-year benefit, coordination with Social Security claiming
- **Iowa relocation tax impact**: State-specific advantages (Iowa does NOT tax Social Security income — major planning lever)
- **Quarterly estimated tax planning**: Payment schedule, safe harbor guidance
- **Tax return preparation**: Accurate federal and state returns, correctly reporting Social Security, Medicare, investment income

### Alex's operating principle

Make sure every retirement income decision is tax-efficient. Model multi-year tax impact and flag opportunities (Roth conversions, capital gains harvesting, strategic withdrawal timing) that reduce lifetime tax burden. Do not just file returns; plan proactively to minimize taxes before the year happens.

### Deliverables Alex produces

- Multi-Year Tax Projection (10–20 year forecast, federal + state, all scenarios)
- IRMAA Threshold Analysis (provisional income mapping, strategic withdrawal recommendations)
- Capital Gains Harvest Plan (year-by-year schedule, basis tracking, loss carryforwards)
- Roth Conversion Analysis (opportunity years, conversion amounts, tax cost, 20-year benefit)
- Quarterly Estimated Tax Schedule (if applicable, payment amounts and due dates)
- Tax Return Preparation (federal Form 1040, state returns, accurate reporting)

### Key vetting questions for Alex candidates

1. "A client's provisional income is $190k. They're deciding between Social Security at 67 (which adds $25k to provisional income) or delaying to 70. How do you model the IRMAA impact on Medicare premiums?"
2. "What software or method do you use to build a 20-year tax projection? How do you handle variables like market return, Social Security COLA, RMD changes?"
3. "A 65-year-old has $50k AGI before a $40k Roth conversion at 12% tax rate. How do you analyze whether this conversion is worth it, and what 20-year benefit analysis do you show?"
4. "A client has $150k appreciation in one holding and $20k loss in another. How do you handle wash-sale rules and document the harvest?"
5. "Your client is moving to Iowa. What's the tax advantage compared to their prior state? How does this affect Social Security claiming strategy?"
6. "You're working with a Retirement Specialist on the same client. How do you coordinate recommendations?"

### Market profile

- **Typical credentials**: CPA (required for returns), often with EA or CFP
- **Experience**: 8–15 years in retirement tax planning, not just compliance
- **Market cost**: $2,500–$6,000 for comprehensive retirement tax plan (15–30 hours)
- **Hourly rate**: $200–$400/hour for CPAs with retirement focus

### Alex's contract locations

- **Wiki contract** (source of truth): `Team/Alex - Tax Accountant/AGENTS.md`
- **Claude Code shim** (parallel dispatch binding): `.claude/agents/alex.md`
- **Research brief** (reference, not in contract): `Deliverables/2026-05-23-tax-accountant-hire-research.md`

### Hard rules for Alex

1. IRMAA is not separate from tax planning (provisional income thresholds drive tax recommendations)
2. Coordinate with Bert on every major recommendation (claiming age, withdrawal sequencing, tax efficiency form one system)
3. Show multi-year impact, not single-year (a Roth conversion that costs $5k one year but saves $30k over 20 years is smart)
4. Iowa Social Security rule is a gift (Iowa does not tax Social Security income — highlight this advantage)
5. Provisional income is your north star (for Social Security taxation and IRMAA, organize analysis around provisional income thresholds)
6. Capital gains harvesting is proactive work (model year-by-year realized gains and identify harvest opportunities annually)

---

## Collaboration Model (Critical)

**Bert and Alex must work in tight coordination.** They are not siloed. Here's the loop:

1. **Bert proposes** claiming and withdrawal scenarios (e.g., "claim at 67 with $30k/year from 401k").
2. **Alex models** tax impact, IRMAA impact, and Roth conversion opportunities for each scenario.
3. **Bert and Alex jointly** present 2–3 options to you with trade-offs (lifetime benefits, monthly income, tax burden, healthcare costs).
4. **You choose** the path that matches your values and goals.

Both contracts explicitly state: "Coordinate with [the other specialist] before finalizing any recommendation."

---

## Timeline and Hiring Process

### Quick-turnaround recommendation

This is not a back-burner hire. You are 67 at FRA, still working, facing imminent Medicare enrollment decisions (deadlines are hard stops). Speed matters.

### Proposed sequence

**Week 1 (now):** 
- You approve both specialists (this brief).
- Nolan locks in the hires and updates agent-index.
- Bert and Alex review situation brief and Jesse's intake document.

**Week 2:** 
- Bert and Alex request missing documents: Social Security statements (Jesse and Diana), current 401k statements, healthcare plan details, investment holdings, prior-year tax returns.
- They build initial scenario model and tax projection.

**Week 3:** 
- Bert models claiming scenarios (62, 67, 70) and household lifetime benefits.
- Alex models tax impact, IRMAA thresholds, and Roth conversion opportunities for each scenario.
- They coordinate findings.

**Week 4:** 
- Bert drafts Medicare planning summary and withdrawal sequencing plan.
- Alex produces capital gains harvest plan and quarterly tax schedule.
- Joint presentation memo to you with recommendations and trade-offs.

**Week 5:** 
- Tax return prep (2026) and ongoing engagement setup.

### Approval gate (you are here)

**This brief requires your approval.** Review:

1. **Do the specialist profiles match your needs?** (Bert for Social Security/Medicare/withdrawal strategy, Alex for tax efficiency and IRMAA coordination)
2. **Are the vetting questions the right ones?** (If you want to vet candidates yourself, these are the questions that separate world-class from adequate.)
3. **Does the collaboration model make sense?** (Bert + Alex working together, not siloed)
4. **Are you comfortable with the market costs?** ($2,500–$8,000 per specialist for a comprehensive plan is typical; you can negotiate based on complexity.)

### Next step (after your approval)

1. Nolan finalizes the hires (creates folders, registers in agent-index).
2. Bert and Alex are live and ready for dispatch.
3. Either you or Nolan can begin the screening/vetting process for actual candidates.

---

## Files Ready for Your Review

**Specialist Contracts (ready to approve):**
- `Team/Bert - Retirement Specialist/AGENTS.md` — Bert's full operating contract
- `Team/Alex - Tax Accountant/AGENTS.md` — Alex's full operating contract

**Claude Code Shims (for parallel dispatch):**
- `.claude/agents/bert.md` — Routes Bert in Claude Code
- `.claude/agents/alex.md` — Routes Alex in Claude Code

**Research Briefs (reference, for deep context):**
- `Deliverables/2026-05-23-retirement-specialist-hire-research.md` — Detailed market research on what the role should look like
- `Deliverables/2026-05-23-tax-accountant-hire-research.md` — Detailed market research on tax accounting specialization

**Agent-Index Update:**
- `Team/agent-index.md` — Updated with Bert and Alex (entries added, awaiting your approval to lock in)

---

## Decision Required

**Do you approve hiring Bert (Retirement Specialist) and Alex (Tax Accountant)?**

If yes:
- Review the two AGENTS.md contracts above.
- Let Nolan know if you want any changes to scope, boundaries, or collaboration model.
- Once approved, Nolan finalizes the hire and both specialists are live in your agent-index.

If no or if you want revisions:
- Let Nolan know what changes are needed.
- Nolan will update the contracts and resubmit.

---

**Prepared by:** Nolan  
**Date:** May 23, 2026  
**Status:** Ready for your decision
