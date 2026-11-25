# AI & LLM Agents: Product Portfolio

Agentic AI products I have specified, prototyped and evaluated as a Product Manager. Each project goes from user discovery to a working prototype, a written evaluation and a pilot plan.

| Project | What it does | LLM | Status |
| --- | --- | --- | --- |
| [Plan Update Agent](#plan-update-agent) | Turns a dietitian's review-call transcript into a sourced, safety-checked plan update and client email | Claude | Working prototype, synthetic data |

---

## Plan Update Agent

**An AI agent for independent dietitians.** After a weekly review call, the dietitian uploads the transcript. The agent drafts the updated diet plan and the client email; the dietitian approves before anything is sent.

### Problem

Independent dietitians run weekly review calls with many clients. Today:

- **Updates wait 2–3 days** in the admin queue while client motivation fades.
- **Details are missed**, because notes are taken while keeping the client talking.
- **Promises are forgotten** ("I'll send you a recipe"), and trust drops.

Discovery mapped the current workflow as 11 manual steps with 3 breaking points. The insight: the problem is not writing diet plans. It is continuity between what was said on the call and what gets sent.

### Solution: the LLM reads, code checks, the dietitian decides

| Layer | Responsibility |
| --- | --- |
| **Claude (LLM)** | Reads a messy call transcript and extracts each agreed change, client issue and promise, with the exact quote and timestamp |
| **Deterministic code** | Applies only agreed changes; checks allergies and diet rules; flags medical warning signs; blocks instructions hidden in the transcript; verifies the client identity and that the email matches the plan |
| **Dietitian** | Accepts, edits or rejects each item. Nothing is sent without one confirmed "Approve and send" |

**Design decision:** the LLM handles language understanding; safety-critical rules (allergens, diet rules, approval gate) live in tested code, not in the prompt.

**Boundaries:** the agent never invents plan changes, chooses substitute foods, gives nutrition or medical advice, discusses medication or lab results, or contacts clients directly.

### Success metrics

| Metric | Target | Baseline |
| --- | --- | --- |
| Call-to-client time (upload to approved email delivered) | Under 24 hours | 2–3 days |
| Unagreed changes sent to clients | 0 | n/a |
| LLM recall on agreed changes (live evaluation gate) | ≥ 95% | n/a |
| Safety boundary cases | 100% pass | n/a |

### Evaluation

- **Golden set:** 8 synthetic clients, 40 transcripts with expected answers written before testing, and planted traps: allergy clash, changed mind, pregnancy, hidden instruction, wrong-client upload, same-name clients.
- **Test plan:** 24 cases in three groups (it works, it respects limits, it handles hard cases), with 6 priority cases automated.
- **Result:** 27 of 27 guardrail checks passed in one automated run (1 November 2026) on the scripted prototype.
- **Honest scope:** the run used scripted extraction, so it proves the workflow and safety controls, not live Claude accuracy. Next gate: live Claude on 25 labelled transcripts.

### What changed after review and testing

| Issue found | Fix |
| --- | --- |
| Email listed a recipe before it was confirmed | Attachment appears only after "Attach and mark done" |
| A pregnancy was filed under medicines | New facts get their own profile field |
| Email still said "curd" after the plan was fixed | Email rebuilds from the approved choices |

### Demo

One weekly cycle for Ananya, a synthetic 29-year-old vegetarian client with PCOS and lactose intolerance:

1. The dietitian uploads the Week 2 call transcript.
2. The agent extracts 3 proposed changes, a complaint, a supplement mention, a profile update and a recipe promise, each linked to a quote.
3. It catches a clash (curd vs lactose intolerance) and keeps "Approve and send" disabled until it is resolved.
4. The dietitian edits the snack, attaches the promised recipe and approves.
5. Version 2 is locked and the email goes to a test inbox only.

### Data protection

Synthetic data only, labelled on every file and screen; all emails go to `@example.com` test addresses. Designed around India's DPDP Act (consent, withdrawal, access, correction, deletion). The same principles (data minimisation, explicit consent, right to erasure, human oversight) align with GDPR and the EU AI Act, to be confirmed with legal review before any real pilot.

### Pilot plan

1 dietitian, 5 low-risk clients, 2 weeks in shadow mode, then 4 weeks live, with same-day stop rules. Before any real client: expand allergen checks to about 300 Indian dishes, add an LLM-based medical-warning check, and pass the live Claude evaluation.

### My role

Product Manager, end to end: user discovery and workflow mapping, PRD (4D method: Discovery, Design, Develop, Deploy), agent and guardrail design, synthetic data and evaluation design, prototype, test runs and pilot plan.

### Files

- `AI_PRD_ PlanUpdateAgent.xlsx`: full PRD, from discovery to pilot plan
- `Plan Update Assistant.pptx`: demo-day deck (10 slides)

---

## Kurzfassung (Deutsch)

**Plan Update Agent** ist ein KI-Agent für selbstständige Ernährungsberater:innen. Nach dem wöchentlichen Beratungsgespräch extrahiert Claude die vereinbarten Änderungen mit wörtlichem Zitat aus dem Transkript. Fest programmierte Regeln prüfen Allergien, Ernährungsvorgaben und Warnsignale. Die Beraterin genehmigt jede Änderung, bevor etwas an die Klientin geht.

Ziel: Planaktualisierung in unter 24 Stunden statt 2–3 Tagen, ohne ungenehmigte Änderungen. Ergebnis im Prototyp: 27 von 27 Sicherheitsprüfungen bestanden (skriptgesteuerter Lauf, nur synthetische Daten). Meine Rolle: Product Manager von der Nutzerforschung bis zum Pilotplan.
