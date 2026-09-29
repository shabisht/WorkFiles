# Legal Contract Review POC: Developer Implementation Guide
**Platform:** Microsoft Copilot Studio, GitHub Copilot harness
**Scope:** NDAs + Supplier Agreements, India jurisdiction
**Deadline:** 4 days | **Author role:** Copilot Studio developer
**Status:** Build guide. Playbook content and India compliance mappings are ILLUSTRATIVE and must be signed off by a qualified Indian lawyer before any real use.

---

## 0. Read this first (honest constraints)

**What I could verify (Sept 2026, from Microsoft CAT blog + repos):**
- Copilot Studio agents can be powered by the GitHub Copilot harness. Its component model: **Instructions** (always true), **Knowledge** (searchable facts), **Tools** (system actions), **Memory** (persistent context), **Skills** (situational procedures, `SKILL.md`), **Connected agents** (real specialist domains). Rule of thumb from CAT: put each behavior in the smallest component that makes it reliable and inspectable.
- Local authoring loop: create the agent in the Copilot Studio portal, clone it with the **Copilot Studio VS Code extension**, edit YAML with GitHub Copilot, then Preview / Apply changes back.
- The **Copilot Studio plugin** (`microsoft/copilot-studio-plugin`, successor to skills-for-copilot-studio) adds author / test / troubleshoot / migrate commands. It needs **Power Platform CLI (pac) version above 2.9.3**. It is experimental: always review generated YAML.
- Harness agents **consume Copilot Credits during design, preview and evaluation**, not only in production. Set an agent-level credit limit early (PPAC > Licensing > Copilot Studio > Manage Agents).

**What I could NOT verify (spike these on Day 1, max 2 hours total):**
1. How a harness agent receives an uploaded Word/PDF file (direct attachment vs tool). Section 9 has a 3-step fallback.
2. Exact YAML folder layout for harness agents, skills and connected agents. Let the plugin generate it, do not hand-write from memory.
3. Whether your tenant's harness has a code tool you could use for docx generation (Plan B in Section 8).

**Decision rule:** if a spike fails, take the fallback listed. Do not debug past 2 hours on Day 1.

---

## 1. Architecture decision: 1 main + 2 connected = 3 agents

*(This revises my earlier 1+3 suggestion. Reason: the harness gives you Skills for situational procedures, so the Redline agent no longer earns its own agent.)*

| Component | What it is | Why this shape |
|---|---|---|
| **Legal Review Orchestrator** (main) | Agent | Owns the conversation, plan, delegation, merging, and output actions |
| **Clause Analysis Agent** (connected) | Agent | Real specialist domain: extraction + playbook comparison + fallback selection. Redline is the same playbook lookup, so it lives here |
| **Risk & Compliance Agent** (connected) | Agent | Separate specialist domain: severity rubric + India compliance mapping. Independent second opinion on the Clause agent's findings |
| `review-nda` / `review-supplier` | Skills on the Orchestrator | Situational procedures: which clauses, which playbook, in what order |
| `report-payload-schema` | Skill on the Orchestrator | Exact JSON contract used by the Word flow |
| Intake | Instructions + Adaptive Card | Not an agent |
| Legal research | Cut | Free legal research is unevaluable in 4 days. Use the compliance checklist knowledge instead |
| Word report + email | Flows (deterministic) | Never let an LLM format the document or decide when to send |

**Why not 1 agent only?** It would work for a demo, but you lose the independent risk check and the clean separation for evaluation. **Why not 5?** Each extra agent adds a prompt, a schema, a test set and a failure point.

**Credit note:** one review = orchestrator run + 2 connected-agent runs over the full contract text. Keep contracts short in tests (under about 15 pages) and set a dev limit.

---

## 2. Environment and repo setup (Day 1, hour 1)

1. Use a dedicated dev environment (not default). Confirm it is classed as maker-development for credit purposes.
2. Install: VS Code, Copilot Studio extension, GitHub Copilot, pac CLI (latest, above 2.9.3), Node if plugin needs it.
3. In the portal create three empty agents: `Legal Review Orchestrator`, `Clause Analysis Agent`, `Risk Compliance Agent` (choose the GitHub Copilot harness option as available to you).
4. Clone each with the VS Code extension into one monorepo:
   ```
   legal-review-poc/
     agents/
       orchestrator/
       clause-analysis/
       risk-compliance/
     knowledge/            # source docs before upload to SharePoint
       playbooks/
       fallbacks/
       risk/
       compliance/
     flows/                # exported solution or documentation of flows
     templates/            # Word report template
     tests/                # test contracts + ground truth
     docs/
   ```
5. Install the plugin in your AI coding assistant:
   ```
   /plugin marketplace add microsoft/copilot-studio-plugin
   /plugin install mcs-assistant@copilot-studio-plugin
   ```
   Use it as: author (create/edit YAML), test (published-agent test suites), troubleshoot (routing/validation issues).
6. Put everything in a Dataverse **solution** (agents, flows, connection references, environment variables). This is what you export for handover.
7. Git: branch per day, commit before every Apply.
8. Set the agent credit limit for all three agents.

**Environment variables to create in the solution:**
- `ReviewerEmailDefault` (fallback reviewer)
- `SharePointSiteUrl`
- `OutputLibraryPath` (e.g. `/Legal Reviews`)
- `ReportTemplatePath`

---

## 3. Knowledge layer (Day 1, hours 2-6): this decides your accuracy

### 3.1 SharePoint structure
Site: `Legal-POC`
- `Playbooks` library: `NDA-Playbook-India-v1.docx`, `Supplier-Playbook-India-v1.docx`
- `Fallbacks` library: `Fallback-Clauses-India-v1.docx`
- `RiskCompliance` library: `Risk-Rubric-v1.docx`, `India-Compliance-Checklist-v1.docx`
- `Contracts-Inbox` library: contracts to review (fallback ingestion path)
- `Legal Reviews` library: generated reports

**Metadata columns on every knowledge doc:** `ContractType` (NDA / Supplier / Both), `Jurisdiction` (India), `Version`, `Status` (Approved). Only `Status = Approved` docs get attached. Use one doc per contract type so retrieval does not mix NDA and supplier rules.

### 3.2 Document format (important for retrieval)
Use Word docs, **one heading per clause, identical field labels every time**:

```
## N9: Governing Law and Dispute Resolution
Clause ID: N9
Contract type: NDA
Standard position: Indian law; courts of [City] OR arbitration under the Arbitration and Conciliation Act, 1996, seat in India, sole arbitrator, English.
Acceptable range: Indian law with courts of any Indian city where a party is located.
Unacceptable: Foreign governing law or foreign seat without approval.
Severity if unacceptable: High
Fallback ID: FB-N9-1
Fallback wording: [approved text]
Compliance area: Arbitration Act 1996 / Contract Act
Notes for reviewer: ...
```

### 3.3 ILLUSTRATIVE NDA playbook (11 clauses). Replace with client's real positions.

| ID | Clause | Standard position | Unacceptable / High-risk trigger | Sev |
|---|---|---|---|---|
| N1 | Definition of Confidential Information | Marked or reasonably understood as confidential | Covers "all information" with no reasonableness test | Med |
| N2 | Term and survival | 2-3 yr disclosure period, 3 yr survival, trade secrets while secret | Perpetual on all info, or under 1 yr survival | Med |
| N3 | Exclusions | Public domain, prior knowledge, independent development, lawful third-party receipt | All four missing = High; 1-2 missing = Med | High/Med |
| N4 | Permitted disclosure and compelled law | Need-to-know, advisers under duty; notice before compelled disclosure | No compelled-disclosure carve-out | Med |
| N5 | Return / destruction | On request within 30 days; legal/backup retention carve-out | No carve-out for legal retention | Low |
| N6 | No licence / IP | No rights granted | Implied licence or IP assignment | Med |
| N7 | Remedies | Injunctive relief acknowledged | Uncapped indemnity or penalty-style sum | Med |
| N8 | Non-solicit / non-compete | Employee non-solicit up to 12 months | Post-term non-compete (Contract Act s.27 concern) | High |
| N9 | Governing law and disputes | See 3.2 | Foreign law/seat, or one-sided arbitrator appointment | High |
| N10 | Stamp duty and execution | Stamped per applicable state stamp law; cost allocation stated | No mention where physical signing in India | Med |
| N11 | Personal data | If personal data is shared, DPDP-aligned handling clause | Personal data shared with no data protection clause | Med |

### 3.4 ILLUSTRATIVE Supplier playbook (15 clauses)

| ID | Clause | Standard position | High-risk trigger | Sev |
|---|---|---|---|---|
| S1 | Scope / SOW | Defined deliverables, acceptance criteria | Open-ended scope, no acceptance | Med |
| S2 | Term and renewal | Fixed term; auto-renew needs 60-day notice | Auto-renew with under 30-day notice window | Med |
| S3 | Fees and payment | Net 30-45 days from valid invoice | Over 45 days when supplier is a micro/small enterprise (MSMED Act) | High |
| S4 | Taxes / GST | Fees exclusive of GST; tax invoice; supplier bears its own GST compliance | No GST clause, or customer bears supplier's non-compliance | Med |
| S5 | Warranties | Service quality, compliance with law, non-infringement | No warranties | Med |
| S6 | Limitation of liability | Cap at 12 months' fees; carve-outs: fraud, wilful misconduct, confidentiality, IP indemnity | Uncapped on our side, or cap under 3 months' fees on supplier side | High |
| S7 | Indemnity | Mutual, third-party claims, IP infringement | One-sided broad indemnity against us | High |
| S8 | IP ownership | Customer owns bespoke deliverables; supplier retains background IP | Supplier owns all deliverables | Med |
| S9 | Confidentiality | Mutual, survives termination | Missing | High |
| S10 | Data protection and security | DPDP-aligned processor obligations, security safeguards, breach notification timeline | Personal data processed and no clause | High |
| S11 | Termination | For cause with 30-day cure; convenience 30-60 days; insolvency | Supplier-only termination for convenience | Med |
| S12 | SLA and service credits | Measurable SLAs, credits | No SLA for critical service | Med |
| S13 | Audit and subcontracting | Audit rights; subcontracting with consent | Free subcontracting, no audit | Med |
| S14 | Force majeure | Defined events, notice, long-stop termination right | Payment obligations excused | Low |
| S15 | Governing law, disputes, stamp duty | See N9, N10 | Foreign law/seat without approval | High |

### 3.5 Fallback library
For every clause above, at least one approved fallback with an ID (`FB-<clauseID>-1`). Write 2-3 sentence wording each. **The agent may only output fallback text that exists in this file.** If none exists it returns `"No approved fallback, escalate"`.

### 3.6 Risk rubric (`Risk-Rubric-v1.docx`)
- **High:** uncapped liability outside carve-outs; foreign governing law without approval; post-term non-compete; missing confidentiality or data protection where personal data is involved; one-sided indemnity; payment beyond statutory MSME limit.
- **Medium:** non-standard term/survival; missing exclusions (partial); no SLA; auto-renewal traps; weak audit rights.
- **Low:** drafting gaps with no legal exposure; missing return/destruction carve-out.
- **Overall rating:** any High = `Escalate to Legal Head`; 3+ Medium = `Full legal review`; else `Standard review`.

### 3.7 India compliance checklist (`India-Compliance-Checklist-v1.docx`), illustrative
For each area the agent should check whether the contract addresses it. Have Legal confirm current text and status of each law.

| Area | Law | What to check |
|---|---|---|
| Restraint of trade | Indian Contract Act 1872, s.27 | Post-term non-compete; employee non-solicit scope |
| Damages / penalty | Contract Act ss.73-74 | Fixed penalty sums, liquidated damages reasonableness |
| Arbitration | Arbitration and Conciliation Act 1996 | Seat, arbitrator appointment, institution, language |
| Data protection | Digital Personal Data Protection Act 2023 (+ Rules) | Roles (fiduciary / processor), safeguards, breach notification, cross-border. **Verify current commencement status of provisions.** |
| IT security | Information Technology Act 2000 | Reasonable security practices |
| Stamp duty | Indian Stamp Act 1899 / state stamp acts | Stamping requirement varies by state; unstamped documents face admissibility issues |
| Tax | CGST Act 2017 | GST clause, tax invoice, supplier compliance |
| MSME payments | MSMED Act 2006 | Payment timelines for micro/small suppliers |
| Cross-border | FEMA | Payments to foreign party, remittance clause |

---

## 4. Shared JSON contract (the backbone of the whole POC)

Flat string values only in arrays, because the Word repeating-section control cannot handle nested objects.

```json
{
  "matter": {
    "matter_id": "LR-2026-0001",
    "contract_type": "NDA | Supplier",
    "counterparty": "",
    "our_entity": "",
    "jurisdiction": "India",
    "governing_law_found": "",
    "source_file": "",
    "reviewed_on": "YYYY-MM-DD",
    "reviewer_name": "",
    "reviewer_email": ""
  },
  "summary": {
    "overall_rating": "Escalate to Legal Head | Full legal review | Standard review",
    "headline": "2-3 sentences",
    "count_compliant": "0",
    "count_deviation": "0",
    "count_missing": "0",
    "count_high": "0",
    "count_medium": "0",
    "count_low": "0"
  },
  "clauses": [
    {
      "clause_id": "N9",
      "clause_name": "",
      "status": "Compliant | Deviation | Missing",
      "contract_section": "e.g. Clause 12.2 or 'Not found'",
      "extracted_text": "verbatim, max ~60 words",
      "playbook_ref": "N9",
      "deviation_note": "",
      "fallback_id": "FB-N9-1 or 'None'",
      "fallback_text": ""
    }
  ],
  "risk_matrix": [
    {
      "risk_id": "R1",
      "clause_id": "N9",
      "risk_level": "High | Medium | Low",
      "compliance_area": "",
      "legal_basis": "e.g. Arbitration and Conciliation Act 1996",
      "evidence": "section ref + short quote",
      "recommended_action": "",
      "escalate_to": "Legal Head | Legal Reviewer | Business Owner"
    }
  ],
  "open_questions": [ { "question": "" } ],
  "disclaimer": "AI-generated draft for legal professional review. Not legal advice."
}
```

**Rules that make it reliable:**
- Every `clause_id` must exist in the playbook. Reject and re-ask if not.
- `extracted_text` must be a verbatim substring of the contract. The orchestrator spot-checks.
- `status = Missing` requires `contract_section = "Not found"`.

---

## 5. Agent specs

### 5.1 Legal Review Orchestrator (main)

**Purpose:** run one review from request to delivered report. Does not analyse clauses itself.

**Tools:**
- Connected agents: Clause Analysis Agent, Risk Compliance Agent
- Flow: `Generate-Review-Report` (Section 8)
- Flow: `Send-For-Review` (Section 8)
- Fallback ingestion flow `Get-Contract-Text` (Section 9), only if direct file reading fails
**Skills:** `review-nda`, `review-supplier`, `report-payload-schema`
**Knowledge:** none needed directly (specialists hold the playbooks)

**Instructions (draft, paste and tune):**
```
You are the Legal Review Orchestrator for an internal legal team in India.
You coordinate contract reviews. You never give final legal approval or legal advice.

WORKFLOW
1. Collect: the contract file, contract type (NDA or Supplier Agreement), counterparty name,
   and reviewer email. If any are missing, ask once, in a single message.
2. If the type is not NDA or Supplier Agreement, or the jurisdiction is not India, stop and
   say this POC only supports NDAs and supplier agreements under Indian law.
3. Load the skill for the contract type and follow its steps exactly.
4. Send the full contract text to the Clause Analysis Agent. Wait for its JSON.
5. Send the contract text plus the clause findings to the Risk Compliance Agent. Wait for its JSON.
6. Merge into the report payload using the report-payload-schema skill. Compute summary counts
   yourself from the arrays.
7. Verify: every clause finding has a contract_section; every High risk has evidence;
   extracted_text appears in the contract. Fix or mark "Unverified" anything that fails.
8. If the two agents disagree (e.g. clause Compliant but risk High), list it under open_questions.
9. Call Generate-Review-Report with the payload, then Send-For-Review.
10. Reply with: overall rating, counts, top 3 issues, and the report link. Keep it under 150 words.

RULES
- Every finding must cite a contract section and a playbook ID. If evidence is missing, say "Not found".
- Never invent clause text, statutes, or fallback wording.
- Only use fallback wording returned by the Clause Analysis Agent.
- Label all suggested wording as AI-generated.
- Do not send the report if the payload failed verification; tell the user why.
```

### 5.2 Clause Analysis Agent (connected)

**Purpose:** for a given contract and type, extract each playbook clause, compare, classify, and attach the approved fallback.
**Knowledge:** the relevant Playbook doc + Fallback doc (filter by `ContractType`).
**Tools:** none. Pure reasoning + retrieval.
**Input (from orchestrator):** `contract_type`, `contract_text`.
**Output:** JSON `{ "clauses": [...] }` matching Section 4, nothing else.

**Instructions (draft):**
```
You analyse contracts against an approved playbook for India. You output JSON only.

For the given contract type, go through EVERY clause ID in the playbook, in order.
For each clause:
1. Find the relevant text in the contract. Quote it verbatim (max 60 words) and give the section number.
2. Compare to the playbook standard, acceptable range and unacceptable triggers.
3. Set status: Compliant (within standard or acceptable range), Deviation (outside), Missing (no such clause).
4. For Deviation or Missing, look up the fallback in the fallback library and return its ID and exact text.
   If none exists, set fallback_id to "None" and fallback_text to "No approved fallback, escalate".
5. Write deviation_note in one sentence: what differs and why it matters.

RULES
- Output one object per playbook clause. Never skip a clause. Never add clauses not in the playbook.
- Never paraphrase in extracted_text. If you cannot find text, status is Missing and section is "Not found".
- Do not rate risk. That is another agent's job.
- Do not write your own fallback language.
- If the contract is unreadable or not the stated type, return {"error": "reason"}.
- Output valid JSON only, no commentary.
```

### 5.3 Risk & Compliance Agent (connected)

**Purpose:** convert clause findings into a risk matrix using the rubric and India compliance checklist; add compliance gaps not tied to a single clause.
**Knowledge:** `Risk-Rubric-v1`, `India-Compliance-Checklist-v1`.
**Tools:** none.
**Input:** `contract_type`, `contract_text`, `clauses[]`.
**Output:** JSON `{ "risk_matrix": [...], "open_questions": [...], "overall_rating": "..." }`.

**Instructions (draft):**
```
You assess contract risk for an India-based legal team. Output JSON only.

INPUT: contract text and a list of clause findings.
1. For each clause with status Deviation or Missing, create a risk row. Rate High/Medium/Low strictly
   using the risk rubric. Do not invent your own severity logic.
2. For each row, name the compliance area and legal basis ONLY from the India compliance checklist.
   If nothing in the checklist applies, write "Policy/commercial" and leave legal_basis as "N/A".
3. Provide evidence: the section reference and a short verbatim quote.
4. Set escalate_to per the rubric.
5. Separately, scan the contract for compliance gaps in the checklist that have no clause row
   (e.g. personal data shared but no data protection clause, or foreign party but no remittance/FEMA note).
   Add them as rows with clause_id "GEN".
6. Anything you are unsure about, or that needs jurisdiction-specific advice, goes to open_questions.
7. Compute overall_rating from the rubric.

RULES
- Never cite a statute that is not in the checklist.
- Never state that something is unlawful. Say "may conflict with" and flag for legal review.
- No evidence, no rating: put it in open_questions instead.
- Valid JSON only.
```

---

## 6. Skills on the Orchestrator

Each skill is a folder with a `SKILL.md`. Have the plugin's author command place them in the correct location.

### 6.1 `review-nda/SKILL.md`
```
---
name: review-nda
description: Procedure for reviewing an NDA under Indian law. Use when contract_type is NDA.
---
1. Confirm the document is an NDA (mutual or one-way). Record which.
2. Ask the Clause Analysis Agent to use the NDA playbook (clauses N1-N11).
3. Record governing law found and the dispute forum in matter.governing_law_found.
4. Pass results to the Risk Compliance Agent with contract_type = NDA.
5. In the headline, state mutual/one-way and the top 3 risks.
```

### 6.2 `review-supplier/SKILL.md`
```
---
name: review-supplier
description: Procedure for reviewing a supplier or services agreement under Indian law.
---
1. Confirm it is a supplier/services agreement. Identify who is the customer and who is the supplier.
2. Ask the Clause Analysis Agent to use the Supplier playbook (clauses S1-S15).
3. Ask whether the supplier may be a micro/small enterprise; if unknown, add it to open_questions.
4. Pass results to the Risk Compliance Agent with contract_type = Supplier.
5. Headline: contract value if stated, term, liability cap, payment terms, top 3 risks.
```

### 6.3 `report-payload-schema/SKILL.md`
Paste Section 4 schema and the validation rules. This is the single source of truth the orchestrator follows when merging.

---

## 7. Adaptive Card intake (optional, Day 3 if time)
If the harness supports card input in your channel, one card with: contract type dropdown, counterparty, reviewer email, file. If not, the orchestrator's instruction 1 (ask once) is sufficient for the POC. **Do not spend time here before the core loop works.**

---

## 8. Workflows / flows

### 8.1 `Generate-Review-Report`
**Trigger:** called from agent (Copilot Studio tool). **Input:** `payload` (string, the Section 4 JSON).
1. `Parse JSON` with the Section 4 schema.
2. `Populate a Microsoft Word template` (Word Online Business). Template from `ReportTemplatePath`. Map every content control (Section 8.3).
3. `Create file` in SharePoint `OutputLibraryPath`, name `LR-<matter_id>-<counterparty>-<date>.docx`.
4. `Create sharing link` (view, organisation).
5. **Return** `report_url`, `file_id`.

**Error handling:** scope + catch; return `{ "error": "..." }` so the orchestrator can tell the user.

### 8.2 `Send-For-Review`
**Input:** `report_url`, `reviewer_email`, `matter_id`, `counterparty`, `overall_rating`, `headline`.
1. Get file content by `file_id`.
2. `Send an email (V2)` (Office 365 Outlook): to reviewer, subject `[Legal Review] <contract_type> - <counterparty> - <overall_rating>`, body = headline + report link + "AI-generated draft for legal review", attach the docx.
3. Optional stretch: `Create an approval` (do not wait inside the agent call) and log decision to a SharePoint list.
4. Log to a SharePoint list `ReviewLog`: matter_id, date, reviewer, rating, report link. This is your minimal audit trail.
5. Return `status: sent`.

### 8.3 Word template (`templates/Legal-Review-Report.dotx` or `.docx`)
Build in Word with the Developer tab. Content controls (use the control **Title**; keep names identical to schema fields):

| Section | Controls |
|---|---|
| Header block | matter_id, contract_type, counterparty, our_entity, jurisdiction, governing_law_found, reviewed_on, reviewer_name |
| Executive summary | overall_rating, headline, count_high, count_medium, count_low, count_compliant, count_deviation, count_missing |
| Clause deviation table | **Repeating section** wrapping a table row: clause_id, clause_name, status, contract_section, deviation_note |
| Risk & compliance matrix | **Repeating section**: risk_id, clause_id, risk_level, compliance_area, legal_basis, evidence, recommended_action, escalate_to |
| Suggested redlines | **Repeating section**: clause_id, extracted_text, fallback_id, fallback_text (label "AI-generated, requires legal review") |
| Open questions | **Repeating section**: question |
| Footer | disclaimer |

**Tips:** build and test the flow with hard-coded JSON before connecting the agent. Empty arrays must still render cleanly (test it).

**Plan B if Populate Word template misbehaves:** generate HTML in the flow and convert to docx via OneDrive "Convert file", or use a code tool if your harness exposes one (python-docx). Decide by end of Day 3 morning, no later.

---

## 9. File ingestion: 3-step fallback (Day 1 spike)

Test in this order with one clean text-based DOCX and one text-based PDF:
1. **Direct attachment:** upload in chat, ask the orchestrator to quote the first clause and the last paragraph. If it can, done.
2. **SharePoint file via tool/knowledge:** drop the file in `Contracts-Inbox`, have a flow return file content and see whether the agent can read it.
3. **`Get-Contract-Text` flow:** flow takes file path, converts DOCX to PDF (OneDrive convert file) if needed, runs AI Builder "Recognize text" (or your approved OCR), returns plain text. Works for scanned PDFs too, but slower and consumes AI Builder credits.

**Limit for the POC:** text-based Word/PDF up to about 15 pages. State it in the demo.

---

## 10. Testing and evaluation (start Day 2, formalise Day 4)

### 10.1 Test set (build on Day 1-2)
8 contracts: 4 NDAs + 4 supplier agreements. Take public sample templates, adapt to Indian context, then **seed** known problems.
- Clean baseline (0 deviations) x1 per type
- Seeded: foreign governing law; post-term non-compete; uncapped liability; missing exclusions; 60-day payment terms with an MSE supplier; missing data protection clause with personal data
Create `tests/ground-truth.xlsx`: one row per contract per clause with expected status and expected risk level.

### 10.2 Metrics and POC pass thresholds
| Metric | How | Target |
|---|---|---|
| Clause status accuracy | matches vs ground truth | at least 85% |
| Seeded High risks caught | count | 100% (no misses) |
| Fabricated quotes | `extracted_text` not in contract | 0 |
| Findings with section ref | count | 100% |
| Fallback text not in library | count | 0 |
| End-to-end success | report generated + email sent | at least 7 of 8 |
| Time per review | stopwatch | under 3 min |

Be honest in the demo: 8 contracts is a smoke test, not statistical proof.

### 10.3 How to run
- Use the plugin `test` command or Copilot Studio's built-in evaluation for batch runs of the Clause and Risk agents in isolation first (feed them fixed inputs), then run the orchestrator end to end.
- Log failures in a table: contract, clause, expected, actual, root cause (prompt / playbook wording / retrieval / file parsing).

---

## 11. 4-day schedule

**Day 1: Foundations + spikes**
- H1: environment, repo, three agents created and cloned, credit limits
- H2-5: SharePoint site, write both playbooks + fallbacks + rubric + checklist (get a lawyer to skim if at all possible), upload with metadata
- H6: spikes: file ingestion (Sec 9), JSON-only output from a connected agent, Word populate with dummy JSON
- H7: prepare 8-contract test set skeleton

**Day 2: Specialist agents**
- Clause Analysis Agent: instructions, knowledge, test on 4 contracts in isolation. Iterate until statuses stabilise.
- Risk Compliance Agent: same, using saved Clause outputs as fixed inputs.
- Freeze the JSON schema by end of day.

**Day 3: Orchestrator + outputs**
- Orchestrator instructions, skills, connected agents wired
- Build Word template and `Generate-Review-Report` flow (with hard-coded JSON first)
- Build `Send-For-Review`
- First full end-to-end run by end of day, even if ugly

**Day 4: Test, harden, demo**
- Morning: run all 8 contracts, log failures, fix top 3 causes only
- Midday: freeze. No new features. Re-run.
- Afternoon: demo script rehearsal, record backup video, one-page handover note (what works, limits, next steps)

**Cut order if you slip:** Adaptive Card intake, then Approval step, then PDF support (Word only), then supplier agreements (ship NDA only). Do not cut the test run.

---

## 12. Demo script (10 minutes)
1. Show architecture slide: 1 main + 2 connected, skills, flows, human reviewer.
2. Upload NDA with seeded foreign-law + non-compete. Show orchestration progress.
3. Show the reply: rating, counts, top issues.
4. Open the Word report: clause table, risk matrix, redlines with source IDs.
5. Show the reviewer's email and the `ReviewLog` entry.
6. Repeat quickly with a supplier agreement.
7. Show test results table and stated limits.
8. Next steps: real playbook from Legal, more contract types, obligation tracking, evaluation set expansion.

---

## 13. Risks and mitigations

| Risk | Impact | Mitigation |
|---|---|---|
| Playbook is thin or wrong | Everything downstream | Get real positions from Legal on Day 1; label illustrative content clearly |
| File parsing fails on long/scanned PDFs | Demo failure | Text-based files only for POC, fallback flow in Sec 9 |
| Connected agent returns non-JSON | Flow breaks | JSON-only instruction, orchestrator validates, one retry |
| Agent invents quotes or statutes | Trust loss | Verbatim check, statute list restricted to checklist |
| Credits burn during iteration | Blocked mid-build | Agent-level limit, short test docs, isolate agents when testing |
| Harness/plugin experimental | Schema changes, YAML errors | Pin your pac/plugin versions, review every generated YAML, commit before Apply |
| Latency of chained agents | Poor demo | Progress message, keep docs short |
| Legal accuracy of India mappings | Credibility | Every statute reference reviewed by a lawyer, phrase as "may conflict with" |

---

## 14. Definition of done for the POC
- One NDA and one supplier agreement (Indian law) reviewed end to end without manual intervention
- Word report generated from the template with all sections populated
- Report emailed to a named reviewer with attachment and link
- Every finding traceable to a contract section and a playbook ID
- Test results documented with the thresholds in Section 10.2
- Solution exported, repo committed, handover note written
- Clear statement that outputs are AI-generated drafts requiring legal review
