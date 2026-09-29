# Legal Contract & Compliance Review Agent

## 4-Day POC Implementation Guide --- Microsoft Copilot Studio GitHub Copilot Harness

**Version:** September 2026\
**POC scope:** India jurisdiction; NDA and Supplier Agreement\
**Primary builder role:** Copilot Studio / Agent Developer\
**Target:** End-to-end demonstrable MVP in 4 days\
**Implementation principle:** Agents reason; workflows execute; approved
knowledge grounds decisions; humans make final legal decisions.

------------------------------------------------------------------------

# 1. POC Objective

Build a governed, evidence-backed, agentic legal contract review POC
that demonstrates this complete journey:

``` text
Contract upload
      ↓
Legal Review Orchestrator
      ↓
Contract classification
      ↓
Contract / clause analysis
      ↓
Playbook comparison
      ↓
Legal / regulatory research
      ↓
Compliance & risk assessment
      ↓
Approved fallback recommendation
      ↓
Consolidated legal review package
      ↓
Word review memorandum
      ↓
Human legal approval
      ↓
Store final review / matter / obligations
```

The POC is **not** intended to provide autonomous legal advice, approve
contracts autonomously, negotiate with counterparties, submit
information to regulators, or replace a legal professional.

The POC demonstrates:

1.  Contract understanding.
2.  Clause-level extraction.
3.  Comparison against an approved legal playbook.
4.  Evidence-backed deviation identification.
5.  India-specific legal/regulatory consideration.
6.  Compliance and risk matrix generation.
7.  Recommendation of approved fallback clauses.
8.  Consolidation of specialist-agent outputs.
9.  Generation of a standardized Word review memorandum.
10. Human review and approval.
11. Basic matter/obligation follow-through.

------------------------------------------------------------------------

# 2. Why the GitHub Copilot Harness Changes the Implementation

The GitHub Copilot harness is a natural-language-first agent
authoring/runtime model. The main building blocks are:

-   Instructions
-   Knowledge
-   Tools
-   Skills
-   Connected agents
-   Model
-   Memory
-   Workflows

The Build tab is the primary configuration surface. The Preview tab is
used for interactive testing, Evaluate for test sets/evaluation, and
Monitor for runtime activity.

Unlike the standard harness, do not design this POC around a large set
of explicit topics and topic branches.

For this POC:

``` text
Agent instructions
        +
Knowledge
        +
Connected agents
        +
Workflows
        +
Tools
        =
Solution
```

Official Microsoft documentation:

-   GitHub Copilot harness overview:
    https://learn.microsoft.com/en-us/microsoft-copilot-studio/agents-experience/overview
-   Agent instructions:
    https://learn.microsoft.com/en-us/microsoft-copilot-studio/agents-experience/authoring-instructions
-   Knowledge:
    https://learn.microsoft.com/en-us/microsoft-copilot-studio/agents-experience/knowledge-add-existing-copilot
-   Connected agents:
    https://learn.microsoft.com/en-us/microsoft-copilot-studio/agents-experience/authoring-add-other-agents
-   Workflows:
    https://learn.microsoft.com/en-us/microsoft-copilot-studio/workflows-experience/flows-overview

------------------------------------------------------------------------

# 3. Final POC Architecture

## 3.1 Recommended agent count

Build **5 agents**.

### Agent 1 --- Legal Review Orchestrator

Main/front-door agent.

Responsibilities:

-   Receive the legal review request.
-   Identify contract type.
-   Establish jurisdiction.
-   Identify applicable playbook.
-   Decide which specialist agents are needed.
-   Delegate specialist analysis.
-   Consolidate findings.
-   Resolve duplicate findings.
-   Identify uncertainty/disagreement.
-   Produce the final review package.
-   Trigger the document-generation workflow.
-   Present the human-review outcome.

### Agent 2 --- Contract Review Agent

Specialist contract-analysis agent.

Responsibilities:

-   Read the supplied contract.
-   Extract relevant clauses.
-   Identify missing clauses.
-   Compare clauses against the approved playbook.
-   Identify deviations.
-   Link findings to contract sections.
-   Assign preliminary severity using the defined risk rules.
-   Identify applicable fallback IDs.
-   Return structured findings.

### Agent 3 --- Legal Research Agent

Specialist legal/regulatory research agent.

Responsibilities:

-   Review legal/regulatory implications of material findings.
-   Use only approved legal/regulatory sources.
-   Distinguish law from guidance and interpretation.
-   Provide source/evidence.
-   Identify where legal validation is required.
-   Avoid presenting AI interpretation as final legal advice.

### Agent 4 --- Compliance & Risk Agent

Specialist risk/compliance agent.

Responsibilities:

-   Map contract findings to compliance controls.
-   Assess privacy, supplier, security, regulatory and contractual
    risks.
-   Generate the risk/compliance matrix.
-   Apply the POC severity rules.
-   Recommend escalation.
-   Identify missing controls.

### Agent 5 --- Redline / Fallback Agent

Specialist fallback recommendation agent.

Responsibilities:

-   Find an approved fallback clause.
-   Match fallback to the deviation.
-   Explain the rationale.
-   Return the approved fallback text or fallback ID.
-   Never invent an authoritative clause when no approved fallback
    exists.
-   Mark recommendations as AI-generated and subject to legal approval.

------------------------------------------------------------------------

# 4. Why Only Five Agents?

The original concept contains six conceptual agents:

1.  Intake
2.  Contract Analysis
3.  Legal Research
4.  Compliance/Risk
5.  Redline
6.  Matter/Obligation

For a four-day POC, combine Intake into the Orchestrator and implement
Matter/Obligation using deterministic workflow actions.

This avoids unnecessary agent proliferation.

## Agent vs workflow rule

Use an agent when the step requires:

-   Interpretation
-   Reasoning
-   Classification
-   Comparing language
-   Determining relevance
-   Synthesizing evidence
-   Handling ambiguity

Use a workflow when the step requires:

-   Creating a SharePoint item
-   Saving a document
-   Populating a Word template
-   Sending an email
-   Starting an approval
-   Updating a status
-   Creating an obligation record
-   Moving a file

This distinction is central to the POC.

Microsoft describes workflows as deterministic automation and agent
nodes as appropriate when a workflow step requires judgment/reasoning.
See:

https://learn.microsoft.com/en-us/microsoft-copilot-studio/workflows-experience/agent-node-workflow

------------------------------------------------------------------------

# 5. Solution Components

Create these components.

``` text
LegalReviewPOC
│
├── Agents
│   ├── Legal Review Orchestrator
│   ├── Contract Review Agent
│   ├── Legal Research Agent
│   ├── Compliance Risk Agent
│   └── Redline Fallback Agent
│
├── Workflows
│   ├── Generate Legal Review Memorandum
│   ├── Legal Review Approval
│   └── Post Approval Matter & Obligation Update
│
├── SharePoint
│   ├── Knowledge
│   │   ├── Playbooks
│   │   ├── ClauseLibrary
│   │   ├── FallbackLibrary
│   │   ├── LegalSources
│   │   └── Compliance
│   │
│   ├── Contracts
│   ├── ReviewReports
│   ├── Matters
│   └── Obligations
│
└── Word
    └── LegalReviewMemorandumTemplate.docx
```

------------------------------------------------------------------------

# 6. SharePoint Knowledge Architecture

Create one SharePoint site or approved document location.

Recommended structure:

``` text
LegalReviewPOC
│
├── Knowledge
│   │
│   ├── Playbooks
│   │   ├── India_NDA_Playbook.docx
│   │   └── India_Supplier_Agreement_Playbook.docx
│   │
│   ├── ClauseLibrary
│   │   ├── NDA_Confidentiality.docx
│   │   ├── NDA_Definitions.docx
│   │   ├── NDA_Return_Destruction.docx
│   │   ├── NDA_Survival.docx
│   │   ├── NDA_Governing_Law.docx
│   │   ├── Supplier_Liability.docx
│   │   ├── Supplier_Indemnity.docx
│   │   ├── Supplier_Termination.docx
│   │   ├── Supplier_IP.docx
│   │   ├── Supplier_Audit.docx
│   │   └── Supplier_Privacy.docx
│   │
│   ├── FallbackLibrary
│   │   ├── NDA_Fallbacks.docx
│   │   └── Supplier_Fallbacks.docx
│   │
│   ├── LegalSources
│   │   ├── India_Contract_Law_Approved_Reference.docx
│   │   ├── India_Privacy_Approved_Reference.docx
│   │   └── Approved_Legal_Guidance.docx
│   │
│   └── Compliance
│       ├── Legal_Compliance_Matrix.xlsx
│       ├── Supplier_Risk_Control_Matrix.xlsx
│       └── Data_Privacy_Control_Matrix.xlsx
│
├── Contracts
│
├── ReviewReports
│
├── Matters
│
└── Obligations
```

Do not give all knowledge sources to every agent.

Use knowledge boundaries deliberately.

------------------------------------------------------------------------

# 7. Knowledge Source Design

## 7.1 Playbook format

Do not create a 100-page generic legal playbook for the POC.

Create a concise structured playbook.

Example:

``` text
CLAUSE ID: SUP-LIAB-001

Contract type:
Supplier Agreement

Clause:
Limitation of Liability

Standard position:
Liability should be capped at the agreed commercial threshold.

Preferred position:
Fees paid/payable during the preceding 12 months.

Fallback position:
Alternative negotiated cap defined by the approved fallback library.

Unacceptable position:
Unlimited liability without approved exception.

Risk if deviated:
High

Required escalation:
Legal reviewer

Fallback ID:
SUP-FB-LIAB-001
```

Use the same pattern for each clause.

------------------------------------------------------------------------

# 8. POC Clause Coverage

## NDA

Target approximately 10--12 clauses:

1.  Parties
2.  Purpose
3.  Definition of Confidential Information
4.  Exclusions
5.  Confidentiality obligation
6.  Permitted disclosure
7.  Security requirements
8.  Return/destruction
9.  Term
10. Survival
11. Remedies
12. Governing law / jurisdiction

## Supplier Agreement

Target approximately 15--18 clauses:

1.  Parties
2.  Scope/services
3.  Fees/payment
4.  Term
5.  Renewal
6.  Termination
7.  Confidentiality
8.  Data protection
9.  Information security
10. Intellectual property
11. Indemnity
12. Limitation of liability
13. Insurance
14. Audit rights
15. Subcontracting
16. Compliance/anti-bribery
17. Business continuity
18. Governing law/dispute resolution

Do not attempt to cover every possible legal clause in four days.

------------------------------------------------------------------------

# 9. Fallback Library Design

Every fallback should have a unique ID.

Example:

``` text
FALLBACK ID: SUP-FB-LIAB-001

Contract type:
Supplier Agreement

Clause:
Limitation of Liability

Trigger:
Unlimited supplier liability or deviation from approved liability cap.

Approved fallback:
[Approved clause wording]

Rationale:
Aligns the provision with the approved supplier playbook.

Permitted use:
Supplier Agreement - India

Escalation:
Legal approval required

Source:
India Supplier Agreement Playbook
```

Additional fallback IDs:

``` text
SUP-FB-TERM-001
SUP-FB-AUDIT-001
SUP-FB-IP-001
SUP-FB-DATA-001
SUP-FB-INDEM-001
SUP-FB-SUB-001

NDA-FB-CONF-001
NDA-FB-RET-001
NDA-FB-SURV-001
NDA-FB-JUR-001
```

------------------------------------------------------------------------

# 10. Compliance Matrix

Create a simple matrix.

Suggested columns:

``` text
Control ID
Category
Requirement
Applicable Contract Type
Trigger
Risk if Missing
Severity
Required Evidence
Escalation
```

Example:

``` text
CTRL-PRIV-001
Privacy
Appropriate data protection obligations
Supplier Agreement
Supplier processes personal data
High
Contract privacy provisions
Legal review
```

``` text
CTRL-SUP-001
Supplier Risk
Audit rights
Supplier Agreement
Material supplier/service
Medium
Audit clause
Legal/Procurement review
```

``` text
CTRL-COM-001
Compliance
Anti-bribery/compliance commitment
Supplier Agreement
Third-party supplier
High
Compliance clause
Legal review
```

The POC should demonstrate control mapping rather than claim that it
performs a complete legal compliance certification.

------------------------------------------------------------------------

# 11. Agent 1 --- Legal Review Orchestrator

## 11.1 Agent name

`Legal Review Orchestrator`

## 11.2 Description

Use:

> Coordinates AI-assisted legal contract review for India-jurisdiction
> NDAs and Supplier Agreements. Classifies the request, selects the
> applicable review path, delegates specialized analysis, consolidates
> evidence-backed findings, prepares a legal review package, and routes
> the result for human legal approval.

## 11.3 Knowledge

Do not attach the entire knowledge repository.

For the orchestrator, use only minimal routing information if required:

-   Contract type routing guide
-   Review process guide
-   Risk escalation rules

The specialist agents should own the detailed legal knowledge.

## 11.4 Connected agents

Connect:

-   Contract Review Agent
-   Legal Research Agent
-   Compliance Risk Agent
-   Redline Fallback Agent

## 11.5 Instructions

Use the following as the starting instruction set:

``` text
ROLE

You are the Legal Review Orchestrator for an AI-assisted contract review process.

PURPOSE

Coordinate the review of India-jurisdiction NDAs and Supplier Agreements.
You are responsible for intake, classification, review planning, delegation,
consolidation and final review-package preparation.

You are NOT the final legal decision-maker.

SCOPE

Supported contract types:
- NDA
- Supplier Agreement

Supported jurisdiction:
- India

PRIMARY OBJECTIVES

1. Identify the contract type.
2. Confirm or infer the applicable jurisdiction from the supplied request/document.
3. Select the appropriate playbook.
4. Delegate contract analysis to Contract Review Agent.
5. Delegate material legal/regulatory questions to Legal Research Agent.
6. Delegate compliance and risk assessment to Compliance Risk Agent.
7. Delegate fallback recommendations to Redline Fallback Agent when deviations
   have an approved fallback.
8. Consolidate all findings.
9. Remove duplicate findings.
10. Preserve evidence and source references.
11. Highlight uncertainty, conflicting findings and missing information.
12. Produce a structured legal review package.
13. Trigger the document-generation workflow only when sufficient information
   exists to produce a review memorandum.
14. Route material recommendations for human legal review.

DELEGATION RULES

Always use Contract Review Agent for detailed clause analysis.

Use Legal Research Agent when:
- a finding has a legal/regulatory implication;
- a privacy/data issue is identified;
- a jurisdiction-specific question exists;
- interpretation of approved legal material is required.

Use Compliance Risk Agent after contract findings are available.

Use Redline Fallback Agent when:
- a clause deviates from the playbook;
- a fallback may exist;
- the finding is material enough to require a proposed remediation.

Do not invent fallback clauses.

EVIDENCE RULES

Every material finding must identify:
- contract section;
- relevant contract wording where available;
- playbook position;
- source or knowledge basis;
- confidence/uncertainty.

If evidence is unavailable, state that it is unavailable.

LEGAL BOUNDARY

Do not provide final legal advice.
Do not state that a contract is legally compliant.
Use wording such as:
- "identified deviation"
- "potential legal consideration"
- "requires legal validation"
- "recommended for legal review"

Never present an AI-generated fallback as an approved final legal position.

OUTPUT

Produce:
1. Executive summary.
2. Contract metadata.
3. Clause deviation table.
4. Compliance and risk matrix.
5. Legal/regulatory considerations.
6. Fallback recommendations.
7. Open questions.
8. Items requiring legal approval.
9. Evidence/source references.

Do not hide uncertainty.
```

------------------------------------------------------------------------

# 12. Agent 2 --- Contract Review Agent

## 12.1 Purpose

This is the core document-analysis specialist.

## 12.2 Knowledge

Attach:

``` text
Playbooks
ClauseLibrary
```

For a more controlled POC, add both playbooks but instruct the agent to
use only the playbook matching the detected contract type.

Do not attach broad legal research sources.

## 12.3 Instructions

``` text
ROLE

You are the Contract Review Specialist.

PURPOSE

Analyze an India-jurisdiction NDA or Supplier Agreement and compare its
contractual provisions against the approved playbook and clause library.

SCOPE

Supported:
- NDA
- Supplier Agreement
- India jurisdiction

TASK

1. Identify contract metadata.
2. Extract relevant contractual clauses.
3. Identify missing expected clauses.
4. Compare each applicable clause with the approved playbook.
5. Identify compliant provisions.
6. Identify deviations.
7. Identify material deviations.
8. Identify potential risk.
9. Identify whether an approved fallback appears to exist.
10. Preserve the exact section/reference location where available.

IMPORTANT

Do not invent clause text.
Do not invent contract sections.
Do not assume that a missing clause is present.
If the document is ambiguous, mark the finding as uncertain.

PLAYBOOK RULE

The playbook is the source of truth for the POC's contractual standard.

Do not replace the playbook position with general model knowledge.

RISK RULES

HIGH:
- Material deviation from mandatory position.
- Unlimited liability where the playbook requires a cap.
- Missing material privacy/data obligation where applicable.
- Material IP ownership conflict.
- Material indemnity deviation.
- Regulatory/legal conflict requiring legal escalation.

MEDIUM:
- Material preferred-position deviation.
- Missing preferred control.
- Commercially unfavorable but negotiable provision.

LOW:
- Drafting inconsistency.
- Administrative issue.
- Non-material deviation.

OUTPUT

For every finding provide:

Finding ID
Contract Type
Clause
Section
Current Position
Relevant Contract Text
Playbook Position
Status
Risk
Reason
Evidence
Fallback Required
Fallback ID if known
Legal Validation Required
Confidence

STATUS VALUES

Compliant
Deviation
Missing
Unclear
Not Applicable
```

------------------------------------------------------------------------

# 13. Contract Review Output Contract

Target structure:

``` json
{
  "contractType": "Supplier Agreement",
  "jurisdiction": "India",
  "contractMetadata": {
    "parties": [],
    "effectiveDate": "",
    "term": "",
    "renewal": ""
  },
  "findings": [
    {
      "findingId": "F-001",
      "clause": "Limitation of Liability",
      "section": "12.3",
      "currentPosition": "Unlimited liability",
      "playbookPosition": "Approved liability cap",
      "status": "Deviation",
      "risk": "High",
      "reason": "Material deviation from approved position",
      "fallbackRequired": true,
      "fallbackId": "SUP-FB-LIAB-001",
      "legalValidationRequired": true,
      "confidence": "High"
    }
  ]
}
```

If the current harness does not expose a strict schema for a
connected-agent response in the exact way you need, still enforce this
structure through the agent instructions and keep the Orchestrator's
expected output consistent.

------------------------------------------------------------------------

# 14. Agent 3 --- Legal Research Agent

## 14.1 Purpose

Answer:

> Does this contract finding create an India-specific legal or
> regulatory consideration?

## 14.2 Knowledge

Use only:

``` text
LegalSources
```

Do not provide unrestricted general legal research.

If approved public websites are available and governance permits them,
add only authoritative sources.

For the POC, curated internal documents are easier to control.

## 14.3 Instructions

``` text
ROLE

You are the Legal and Regulatory Research Specialist.

PURPOSE

Assess India-specific legal and regulatory considerations arising from
contract-review findings.

SCOPE

India jurisdiction only.

KNOWLEDGE BOUNDARY

Use approved legal and regulatory sources provided to you.

Do not treat general model knowledge as authoritative legal research.

For each issue:
1. Identify the legal/regulatory topic.
2. Identify the relevant approved source.
3. Summarize the applicable requirement or consideration.
4. Distinguish mandatory requirement from guidance or interpretation.
5. Explain why the contract finding may be relevant.
6. Identify uncertainty.
7. Identify whether legal validation is required.

DO NOT

- Provide final legal advice.
- Declare a contract legally compliant.
- State that a provision is unlawful unless the approved source clearly supports that conclusion.
- Invent citations.
- Invent legislation or regulatory requirements.
- Treat an interpretation as a statutory requirement.

OUTPUT

Finding ID
Legal Topic
Potential Requirement
Contract Relevance
Source
Evidence
Requirement Type
Confidence
Legal Validation Required
Open Question
```

------------------------------------------------------------------------

# 15. Agent 4 --- Compliance Risk Agent

## 15.1 Knowledge

Attach:

``` text
Compliance/
Legal_Compliance_Matrix.xlsx
Supplier_Risk_Control_Matrix.xlsx
Data_Privacy_Control_Matrix.xlsx

Relevant playbook
```

## 15.2 Instructions

``` text
ROLE

You are the Compliance and Contract Risk Specialist.

PURPOSE

Map identified contract findings against the approved compliance and risk
controls for the POC.

TASK

1. Review contract findings.
2. Identify applicable controls.
3. Determine whether the control is satisfied, missing, or uncertain.
4. Determine severity using the approved risk matrix.
5. Identify evidence.
6. Recommend an escalation route.
7. Identify dependencies on legal, privacy, security, procurement or other review.

CATEGORIES

- Privacy
- Information Security
- Supplier Risk
- Contractual Risk
- Regulatory
- Compliance
- Intellectual Property
- Commercial Risk

SEVERITY

HIGH:
Material exposure or mandatory/critical control concern.

MEDIUM:
Material deviation requiring review or negotiation.

LOW:
Non-material issue or administrative concern.

DO NOT

- Invent compliance requirements.
- Convert a potential issue into a definitive legal conclusion.
- Approve a contract.
- Override the legal playbook.

OUTPUT

Finding ID
Risk ID
Category
Control ID
Requirement
Current State
Evidence
Severity
Rationale
Recommended Action
Escalation Route
Legal Validation Required
Confidence
```

------------------------------------------------------------------------

# 16. Agent 5 --- Redline / Fallback Agent

## 16.1 Knowledge

Attach only:

``` text
FallbackLibrary
ClauseLibrary
Relevant Playbook
```

## 16.2 Instructions

``` text
ROLE

You are the Redline and Approved Fallback Recommendation Specialist.

PURPOSE

Recommend approved fallback wording for contractual deviations.

RULE

Never invent an authoritative fallback.

Use only approved fallback clauses from the Fallback Library.

TASK

For each material deviation:
1. Identify the clause.
2. Identify the current contract position.
3. Identify the playbook position.
4. Search the approved fallback library.
5. Select the most appropriate fallback.
6. Provide the fallback ID.
7. Provide the approved fallback wording if available.
8. Explain why the fallback is relevant.
9. State that legal approval is required.

IF NO FALLBACK EXISTS

Return:

"No approved fallback identified."

Do not generate replacement legal language unless the POC's approved
fallback policy explicitly permits generated draft language.

If generated draft language is permitted, label it:
"AI-generated draft — not approved legal language."

OUTPUT

Finding ID
Clause
Current Position
Playbook Position
Fallback ID
Fallback Text
Rationale
Source
Approval Required
Confidence
```

------------------------------------------------------------------------

# 17. Connected-Agent Setup

Create the four specialist agents first.

Then open the Orchestrator's Build configuration and add them as
connected agents.

The connected-agent descriptions matter because the orchestration
runtime uses them to determine when delegation is appropriate.

Use concise descriptions.

### Contract Review Agent

> Analyze India NDA and Supplier Agreements clause-by-clause against
> approved playbooks and return structured deviations, missing
> provisions, risk and evidence.

### Legal Research Agent

> Assess India-specific legal and regulatory considerations using only
> approved legal/regulatory sources and identify issues requiring legal
> validation.

### Compliance Risk Agent

> Map contract findings to approved compliance and supplier-risk
> controls and return severity, evidence, rationale and escalation
> route.

### Redline Fallback Agent

> Identify approved fallback clauses for contract deviations using the
> approved fallback library; never invent authoritative fallback
> language.

Connected agents run in their own orchestration context with their own
instructions, knowledge and tools, while the primary agent delegates
relevant tasks to them.

------------------------------------------------------------------------

# 18. Do Not Make the Specialist Agents User-Facing

The POC should have one front door:

``` text
Legal Review Orchestrator
```

The specialist agents exist to perform specialized work.

Do not publish all five as separate end-user experiences unless you need
to demonstrate them individually.

------------------------------------------------------------------------

# 19. Workflow Architecture

Build three workflows.

## Workflow 1 --- Generate Legal Review Memorandum

Purpose:

``` text
Structured review package
        ↓
Word template
        ↓
DOCX
        ↓
SharePoint
```

## Workflow 2 --- Legal Review Approval

Purpose:

``` text
Generated memorandum
        ↓
Human approval
        ↓
Approve / Reject / Request changes
```

## Workflow 3 --- Post Approval

Purpose:

``` text
Approved review
        ↓
Matter record
        ↓
Obligation records
        ↓
Notification
```

For the four-day POC, Workflow 3 can be basic.

------------------------------------------------------------------------

# 20. Workflow 1 --- Inputs

Define a clear input contract.

Suggested inputs:

``` text
MatterId
ContractName
ContractType
Counterparty
Jurisdiction
ReviewDate
ReviewerEmail
ExecutiveSummary
RiskSummary
ClauseFindings
ComplianceMatrix
LegalResearchFindings
FallbackRecommendations
OpenQuestions
ApprovalItems
```

The exact dynamic-content mechanics depend on how you invoke the
workflow from the Orchestrator.

The principle is more important:

> The workflow should receive structured data, not a giant unstructured
> conversation transcript.

------------------------------------------------------------------------

# 21. Workflow 1 --- Word Template

Create:

`LegalReviewMemorandumTemplate.docx`

Store it in:

``` text
LegalReviewPOC/ReviewReports/Templates/
```

Enable the Word Developer tab and add content controls.

Microsoft's Word Online (Business) connector supports Plain Text, Combo
Box, Drop-Down, Image and Repeating Section content controls. It can
populate a Word template and save/send the generated document through
Power Automate. See:

https://learn.microsoft.com/en-us/connectors/wordonlinebusiness/

------------------------------------------------------------------------

# 22. Word Template Structure

Use this document structure.

``` text
LEGAL CONTRACT REVIEW MEMORANDUM

Matter Information

Matter ID:
Contract Name:
Contract Type:
Counterparty:
Jurisdiction:
Review Date:
Reviewer:

1. EXECUTIVE SUMMARY

[ExecutiveSummary]

2. REVIEW SCOPE

Contract:
Playbook:
Jurisdiction:
Review Method:

3. RISK SUMMARY

High:
Medium:
Low:

4. CLAUSE-BY-CLAUSE REVIEW

[Repeating Findings Table]

5. COMPLIANCE AND RISK MATRIX

[Repeating Risk Table]

6. LEGAL / REGULATORY CONSIDERATIONS

[Repeating Legal Research Table]

7. FALLBACK RECOMMENDATIONS

[Repeating Fallback Table]

8. OPEN QUESTIONS

[OpenQuestions]

9. ITEMS REQUIRING LEGAL APPROVAL

[ApprovalItems]

10. AI REVIEW NOTICE

This memorandum contains AI-assisted analysis and recommendations.
It does not constitute final legal advice or legal approval.
Final decisions remain with an authorized legal professional.
```

------------------------------------------------------------------------

# 23. Word Repeating Sections

For the clause table, use:

``` text
Finding ID
Clause
Section
Current Position
Playbook Position
Status
Risk
Recommended Action
```

For risk table:

``` text
Risk ID
Category
Control
Finding
Severity
Evidence
Action
Escalation
```

For fallback table:

``` text
Finding ID
Clause
Current Position
Fallback ID
Recommended Fallback
Rationale
Approval
```

Word's Repeating Section Content Control is specifically supported for
repeating rows/sections in the Word Online connector.

Important implementation constraints:

-   Keep template under the connector's file-size limit.
-   Give content controls unique names.
-   Test generated documents in Word desktop.
-   If a generated file is immediately used by another flow action,
    consider a short delay because Microsoft documents that generated
    files may need time to become available.
-   Be aware of connector limitations around sensitivity labels and
    authentication policies.

------------------------------------------------------------------------

# 24. Workflow 1 --- Actions

Suggested sequence:

``` text
Trigger
  ↓
Initialize / receive inputs
  ↓
Validate required fields
  ↓
Populate Word template
  ↓
Create filename
  ↓
Create/save DOCX in SharePoint
  ↓
Return document URL + Matter ID
```

Suggested filename:

``` text
LegalReview_<MatterId>_<ContractType>_<Date>.docx
```

------------------------------------------------------------------------

# 25. Workflow 2 --- Approval

Input:

``` text
MatterId
ContractName
RiskSummary
DocumentURL
ReviewerEmail
ApprovalItems
```

Sequence:

``` text
Start
 ↓
Get review document
 ↓
Create approval
 ↓
Send approval to legal reviewer
 ↓
Wait
 ↓
Condition
 ├── Approved
 │      ↓
 │   Workflow 3
 │
 ├── Rejected
 │      ↓
 │   Update Matter Status = Rejected
 │
 └── Changes Requested
        ↓
     Update Matter Status = Revision Required
```

For the POC, use a single legal reviewer.

Do not implement multi-level approval unless specifically required.

------------------------------------------------------------------------

# 26. Workflow 3 --- Post Approval

When approved:

``` text
Update Matters
    Status = Approved
    ApprovedBy = reviewer
    ApprovalDate = date

Create obligation records

Notify requester

Store final review package
```

Example obligation:

``` text
Obligation ID
Matter ID
Contract
Clause
Obligation
Owner
Due Date
Status
Source Section
```

Only create obligations when they can be reliably extracted.

------------------------------------------------------------------------

# 27. Intake Logic

Do not build a separate Intake Agent.

The Orchestrator handles:

``` text
1. Contract received
2. Identify contract type
3. Identify jurisdiction
4. Identify counterparty
5. Determine playbook
6. Determine whether sufficient information exists
```

If the contract cannot be classified:

> "I could not confidently identify the contract type. Please confirm
> whether this is an NDA or Supplier Agreement."

If jurisdiction is missing:

> "The POC supports India jurisdiction. Please confirm that India is the
> applicable jurisdiction before continuing."

------------------------------------------------------------------------

# 28. Contract Type Routing

Use:

``` text
IF NDA
    → India NDA Playbook

IF Supplier Agreement
    → India Supplier Agreement Playbook

ELSE
    → Unsupported Contract Type
```

Do not allow the agent to silently choose a playbook for an unsupported
contract.

------------------------------------------------------------------------

# 29. Risk Matrix

Use a simple POC rule.

## High

Examples:

-   Unlimited liability where cap is required.
-   Material indemnity deviation.
-   Material IP ownership conflict.
-   Missing material data/privacy provision where applicable.
-   Material regulatory concern.
-   Material security obligation absent.

## Medium

Examples:

-   Termination period deviates from preferred position.
-   Missing audit rights.
-   Broad subcontracting rights.
-   Non-standard renewal.
-   Missing preferred operational control.

## Low

Examples:

-   Minor drafting issue.
-   Formatting inconsistency.
-   Non-material administrative issue.

Do not call this a legally authoritative risk rating.

Call it:

> POC risk classification based on the approved review playbook.

------------------------------------------------------------------------

# 30. Evidence Model

Every finding should contain:

``` text
Evidence Type
Evidence Location
Evidence Text
Source
```

Possible evidence types:

``` text
Contract
Playbook
Clause Library
Fallback Library
Legal Source
Compliance Matrix
```

Example:

``` text
Evidence Type: Contract
Evidence Location: Section 12.3
Evidence Text: "Supplier shall have unlimited liability..."
```

This is one of the strongest elements of the demo.

------------------------------------------------------------------------

# 31. Finding Lifecycle

Use:

``` text
IDENTIFIED
   ↓
VALIDATED
   ↓
RISK ASSESSED
   ↓
FALLBACK IDENTIFIED
   ↓
LEGAL REVIEW
   ↓
APPROVED / REJECTED / MODIFIED
```

Do not imply that the AI completes the final step.

------------------------------------------------------------------------

# 32. Final Review Package Schema

The Orchestrator should aim for:

``` json
{
  "matter": {
    "matterId": "",
    "contractName": "",
    "contractType": "",
    "counterparty": "",
    "jurisdiction": "India",
    "reviewDate": ""
  },
  "summary": {
    "overallStatus": "Legal Review Required",
    "highRiskCount": 0,
    "mediumRiskCount": 0,
    "lowRiskCount": 0,
    "materialDeviationCount": 0
  },
  "findings": [],
  "riskMatrix": [],
  "legalResearch": [],
  "fallbackRecommendations": [],
  "openQuestions": [],
  "approvalItems": []
}
```

------------------------------------------------------------------------

# 33. Overall Status Rules

Use only these POC statuses:

``` text
No Material Deviation Identified
Legal Review Required
Insufficient Evidence
Unsupported Contract Type
Unsupported Jurisdiction
```

Do not use:

``` text
Legally Compliant
Legally Approved
Safe
No Legal Risk
```

------------------------------------------------------------------------

# 34. Prompting Pattern for All Agents

Every specialist instruction should contain:

``` text
ROLE
PURPOSE
SCOPE
KNOWLEDGE BOUNDARY
TASK
DECISION RULES
DO NOT
OUTPUT
```

This is more reliable than a very long narrative prompt.

------------------------------------------------------------------------

# 35. Knowledge Boundary Rules

The following hierarchy should be used:

``` text
Approved Client Playbook
        ↓
Approved Clause Library
        ↓
Approved Compliance Matrix
        ↓
Approved Legal/Regulatory Sources
        ↓
General model knowledge
```

For legal conclusions, do not use general model knowledge as the
authoritative source.

If an approved source does not answer the question:

``` text
UNKNOWN / REQUIRES LEGAL VALIDATION
```

------------------------------------------------------------------------

# 36. Avoid Knowledge Overlap

Do not put:

-   Legal research material into the Contract Review Agent unless
    necessary.
-   Fallback library into Legal Research Agent.
-   Every policy into every agent.

The more irrelevant content each agent can retrieve, the more difficult
it becomes to debug grounding.

------------------------------------------------------------------------

# 37. Agent Tools

For this POC, minimize tools.

### Orchestrator

Tools:

-   Generate Review Memorandum workflow
-   Approval workflow if needed

### Contract Review

Prefer knowledge only.

### Legal Research

Prefer approved knowledge sources only.

### Compliance Risk

Prefer knowledge only.

### Redline

Prefer knowledge only.

### Workflow

Use deterministic connectors:

-   SharePoint
-   Word Online (Business)
-   Approvals
-   Outlook or Teams notification

Do not add MCP merely because the platform supports MCP.

Use MCP only if you have a genuine external system that must be
accessed.

------------------------------------------------------------------------

# 38. Why Not MCP for This POC?

You need to demonstrate:

> agentic contract review.

You do not need:

> agentic infrastructure engineering.

SharePoint + Copilot Studio knowledge + workflows are sufficient for the
first MVP.

Adding MCP creates additional:

-   Authentication
-   Tool description
-   Security
-   Error handling
-   Testing
-   Governance
-   Latency

If time remains after the core POC works, MCP can be an extension.

------------------------------------------------------------------------

# 39. File Intake

The preferred demo input is:

``` text
SupplierAgreement_Test_01.docx
```

Create two or three controlled sample contracts.

## Test Contract 1 --- Clean

Mostly follows the playbook.

Expected:

-   Low number of findings.
-   No material high-risk issues.

## Test Contract 2 --- Deviated Supplier Agreement

Intentionally include:

-   Unlimited liability.
-   Missing audit rights.
-   Weak privacy clause.
-   Broad subcontracting.
-   90-day termination.
-   Broad IP language.

Expected:

-   Multiple findings.
-   High/medium risk.
-   Fallbacks.
-   Legal review required.

## Test Contract 3 --- NDA

Include:

-   Weak confidentiality definition.
-   Broad disclosure rights.
-   Missing return/destruction.
-   Short survival.
-   Non-standard governing law.

------------------------------------------------------------------------

# 40. Build Sequence --- Day 1

## Morning

Create:

-   SharePoint site/folders.
-   Playbooks.
-   Clause library.
-   Fallback library.
-   Compliance matrix.
-   Legal source set.

Do not start with the UI.

Knowledge quality determines POC quality.

## Afternoon

Build Contract Review Agent.

Test:

``` text
What is the contract type?
Extract clause X.
Compare clause X against the playbook.
List deviations.
```

End-of-day target:

> Contract → structured clause findings.

------------------------------------------------------------------------

# 41. Build Sequence --- Day 2

Morning:

Build Legal Research Agent.

Test:

``` text
Given this finding, identify the relevant approved legal source.
```

Afternoon:

Build Compliance Risk Agent.

Then Redline/Fallback Agent.

End-of-day target:

``` text
Contract
 ↓
Contract Findings
 ↓
Legal Considerations
 ↓
Risk Matrix
 ↓
Fallbacks
```

------------------------------------------------------------------------

# 42. Build Sequence --- Day 3

Morning:

Build Legal Review Orchestrator.

Add connected agents.

Test delegation.

Expected behavior:

``` text
User
 ↓
Orchestrator
 ↓
Contract Review
 ↓
Legal Research
 ↓
Compliance
 ↓
Fallback
 ↓
Consolidation
```

Afternoon:

Build Word generation workflow.

Create Word template.

Populate it from structured output.

End-of-day target:

> One button/conversation request generates a usable legal review
> memorandum.

------------------------------------------------------------------------

# 43. Build Sequence --- Day 4

Morning:

Build approval.

Build basic Matter/Obligation follow-through.

Afternoon:

Test end-to-end.

Fix:

1.  Wrong agent routing.
2.  Missing evidence.
3.  Incorrect playbook selection.
4.  Hallucinated fallback.
5.  Risk inconsistencies.
6.  Word formatting.
7.  Approval routing.
8.  Output naming.

Do not add new capabilities on Day 4 unless something is broken.

------------------------------------------------------------------------

# 44. Evaluation Strategy

The GitHub Copilot harness provides an Evaluate experience for creating
and running test sets.

Create a POC evaluation set containing scenarios such as:

### Test 1

> Review this NDA.

Expected:

-   NDA identified.
-   India playbook selected.
-   Contract Review Agent used.

### Test 2

> Review this Supplier Agreement.

Expected:

-   Supplier Agreement identified.
-   Supplier playbook selected.

### Test 3

> What is the liability deviation?

Expected:

-   Contract section cited.
-   Playbook position cited.
-   Risk identified.

### Test 4

> Is the supplier agreement legally compliant?

Expected:

-   Agent refuses to make a final legal compliance determination.
-   Provides findings and recommends legal review.

### Test 5

> Give me a replacement clause.

Expected:

-   Approved fallback returned if available.
-   Otherwise "No approved fallback identified."
-   AI-generated language never presented as approved.

### Test 6

> What source supports the privacy finding?

Expected:

-   Approved source identified.

### Test 7

> Contract is governed by US law.

Expected:

-   Unsupported jurisdiction / legal validation required depending on
    POC policy.

### Test 8

> Review a completely unrelated employment agreement.

Expected:

-   Unsupported contract type.

------------------------------------------------------------------------

# 45. Evaluation Metrics

Track:

## Extraction accuracy

``` text
Correctly identified clauses
/
Expected clauses
```

## Finding accuracy

``` text
Correct material deviations
/
Expected material deviations
```

## False positives

``` text
Findings reported
but not actually supported by contract/playbook
```

## Evidence coverage

``` text
Findings with evidence
/
Total findings
```

## Fallback accuracy

``` text
Fallbacks correctly mapped
/
Fallback recommendations
```

## Abstention quality

Check whether the agent correctly says:

> "Insufficient evidence / legal validation required."

rather than hallucinating.

------------------------------------------------------------------------

# 46. Preview Testing

Use Preview continuously.

Do not wait until Day 4.

For each agent:

1.  Run a normal scenario.
2.  Run a missing-information scenario.
3.  Run an unsupported scenario.
4.  Run an adversarial scenario.
5.  Check knowledge grounding.
6.  Check delegation.
7.  Check final output.

Use activity/debug information to determine:

-   Which agent was called.
-   Which knowledge source was used.
-   Whether the correct specialist was selected.
-   Whether the response came from the right source.

------------------------------------------------------------------------

# 47. Important Harness Testing Rule

If the Orchestrator keeps answering everything itself instead of
delegating:

Improve the connected-agent descriptions and Orchestrator instructions.

Do not immediately add more agents.

Example connected-agent description:

Bad:

> Helps with contracts.

Better:

> Performs clause-by-clause analysis of India NDAs and Supplier
> Agreements against the approved playbook and returns structured
> deviations, missing provisions, evidence and risk.

The runtime needs clear specialization.

------------------------------------------------------------------------

# 48. If Delegation Is Unreliable

For the POC, you have two options.

## Option A --- Connected agents

Preferred for demonstrating multi-agent architecture.

``` text
Orchestrator
 ↓
Connected agents
```

## Option B --- Workflow agent nodes

Use when you need a more deterministic pipeline.

``` text
Workflow
 ↓
Contract Review Agent Node
 ↓
Legal Research Agent Node
 ↓
Compliance Agent Node
 ↓
Fallback Agent Node
 ↓
Consolidation
```

The GitHub Copilot workflow agent-node experience supports invoking an
existing published agent or creating an inline agent and passing its
response to downstream workflow steps.

This can be especially useful if the end-to-end sequence must be
deterministic.

For your POC, start with connected agents. If delegation proves
unreliable by Day 3, move the specialist sequence into a workflow.

------------------------------------------------------------------------

# 49. Recommended Hybrid Architecture

If you want the strongest demo, use:

``` text
                  USER
                    ↓
          Legal Review Orchestrator
                    ↓
             Review Workflow
                    ↓
       ┌────────────┼─────────────┐
       ↓            ↓             ↓
 Contract        Legal         Compliance
 Review          Research       Risk
       └────────────┼─────────────┘
                    ↓
             Fallback Agent
                    ↓
              Consolidator
                    ↓
             Word Workflow
                    ↓
                Approval
```

This gives you:

-   Agentic front door.
-   Specialist reasoning.
-   Deterministic orchestration where required.
-   Deterministic document generation.
-   Human approval.

------------------------------------------------------------------------

# 50. Recommended POC Workflow Names

Use clean names:

``` text
LRP - Generate Review Memorandum
LRP - Submit Legal Approval
LRP - Finalize Approved Review
```

Agents:

``` text
LRP - Legal Review Orchestrator
LRP - Contract Review Agent
LRP - Legal Research Agent
LRP - Compliance Risk Agent
LRP - Redline Fallback Agent
```

This will make the solution look organized in screenshots and demos.

------------------------------------------------------------------------

# 51. SharePoint Lists

Create a `Matters` list.

Columns:

``` text
MatterId
ContractName
ContractType
Counterparty
Jurisdiction
ReviewStatus
RiskLevel
HighRiskCount
MediumRiskCount
LowRiskCount
Reviewer
ReviewDate
ApprovalDate
DocumentURL
```

Create an `Obligations` list:

``` text
ObligationId
MatterId
ContractName
Clause
Obligation
Owner
DueDate
Status
SourceSection
```

For the POC, do not build a full legal matter-management system.

------------------------------------------------------------------------

# 52. Matter Status

Use:

``` text
Submitted
In Review
Review Generated
Pending Legal Approval
Approved
Rejected
Revision Required
Closed
```

------------------------------------------------------------------------

# 53. Security / Governance

For the POC, demonstrate these controls:

## Knowledge access

Only approved documents.

## Identity

Use Microsoft Entra-backed authentication where the environment supports
it.

## Least privilege

Agents only receive required knowledge/tools.

## Human approval

No autonomous legal approval.

## Auditability

Store:

-   Contract
-   Review memorandum
-   Approval result
-   Reviewer
-   Date
-   Matter ID

## AI disclosure

Mark AI-generated recommendations.

## Legal boundary

No final legal advice.

## Data boundary

Do not use real confidential client contracts unless explicitly
approved.

Use synthetic contracts for the demo.

------------------------------------------------------------------------

# 54. POC Responsible AI Rules

Put these rules into the Orchestrator:

``` text
1. Do not invent legal sources.
2. Do not invent contract sections.
3. Do not invent fallback clauses.
4. Do not claim legal compliance.
5. Do not approve a contract.
6. Do not conceal uncertainty.
7. Do not override the approved playbook.
8. Do not treat AI output as final legal advice.
9. Preserve evidence.
10. Escalate material issues to an authorized legal professional.
```

------------------------------------------------------------------------

# 55. Handling Missing Evidence

Example:

The playbook says a privacy clause is required, but the contract is
unclear.

Agent should say:

``` text
Finding:
Privacy obligations could not be reliably determined.

Status:
Unclear

Evidence:
Section 8 contains general confidentiality language but does not clearly
establish the required privacy obligations.

Action:
Legal/privacy validation required.

Confidence:
Low
```

Not:

> "The contract violates Indian privacy law."

------------------------------------------------------------------------

# 56. Handling Conflicting Agent Findings

The Orchestrator must not silently select one.

Example:

``` text
Contract Review Agent:
Medium risk.

Compliance Agent:
High risk.

Orchestrator:
Conflict detected.

Final:
Risk classification requires legal/compliance reviewer confirmation.
```

This is an excellent agentic-demo feature because it demonstrates
orchestration and uncertainty handling.

------------------------------------------------------------------------

# 57. Handling No Fallback

If the Redline agent finds no approved fallback:

``` text
Fallback:
Not available in approved fallback library.

Recommendation:
Legal reviewer to determine appropriate language.

AI-generated replacement:
Not provided because no approved fallback exists.
```

This is preferable to hallucinating a clause.

------------------------------------------------------------------------

# 58. Recommended Demo Script

Use this exact scenario.

### Step 1

User:

> Review this supplier agreement for India.

### Step 2

Orchestrator:

> I identified the document as a Supplier Agreement and will use the
> India Supplier Agreement Playbook.

### Step 3

Contract Review:

``` text
23 clauses reviewed
4 material deviations
3 missing controls
```

### Step 4

Legal Research:

``` text
2 findings have India-specific legal/regulatory considerations.
```

### Step 5

Compliance:

``` text
High: 2
Medium: 4
Low: 1
```

### Step 6

Fallback:

``` text
3 approved fallbacks identified.
```

### Step 7

Orchestrator:

> Legal review is required for 2 high-risk findings.

### Step 8

Generate:

`LegalReview_SUP_001.docx`

### Step 9

Send approval.

### Step 10

Reviewer approves.

### Step 11

Matter status becomes:

`Approved`

### Step 12

Obligations are created.

That is the complete MVP.

------------------------------------------------------------------------

# 59. What NOT to Claim in the Demo

Avoid statements such as:

> "The AI has determined that the contract is legally compliant."

Instead:

> "The agent identified no material deviations from the approved
> playbook based on the configured review scope."

Avoid:

> "This clause is illegal."

Instead:

> "The provision raises a legal/regulatory consideration requiring legal
> validation."

Avoid:

> "This is the correct legal wording."

Instead:

> "The agent identified the following approved fallback from the
> configured fallback library."

------------------------------------------------------------------------

# 60. POC Success Criteria

The POC is successful if you can demonstrate:

### Functional

-   Upload NDA.
-   Upload Supplier Agreement.
-   Identify contract type.
-   Select correct India playbook.
-   Extract relevant clauses.
-   Identify deviations.
-   Generate risk matrix.
-   Perform controlled legal research.
-   Identify approved fallbacks.
-   Generate Word memorandum.
-   Send to reviewer.
-   Capture approval.
-   Store final result.

### Agentic

-   Main agent delegates specialist tasks.
-   Specialists have distinct responsibilities.
-   Specialist knowledge is separated.
-   Orchestrator consolidates outputs.
-   Uncertainty is preserved.
-   Human approval is mandatory.

### Governance

-   Approved sources only.
-   Evidence traceability.
-   No autonomous legal decision.
-   No unrestricted legal research.
-   AI-generated recommendations clearly identified.
-   Audit trail exists.

------------------------------------------------------------------------

# 61. Four-Day Priority Matrix

If time becomes tight, prioritize in this order:

## P0 --- Must have

``` text
Contract upload
Contract classification
Playbook comparison
Clause findings
Risk matrix
Fallback recommendations
Word report
Human approval
```

## P1 --- Should have

``` text
Legal research
Matter record
Obligation records
Evaluation test set
```

## P2 --- Nice to have

``` text
Advanced dashboards
MCP
Dataverse
Custom UI
Teams adaptive cards
Multiple approval levels
Advanced monitoring
```

If you run out of time, cut P2 immediately.

------------------------------------------------------------------------

# 62. Recommended Final Architecture

``` text
                                      USER
                                       │
                                       ▼
                     ┌─────────────────────────────────┐
                     │ LEGAL REVIEW ORCHESTRATOR       │
                     │                                 │
                     │ Intake                          │
                     │ Classification                  │
                     │ Review Planning                 │
                     │ Delegation                      │
                     │ Consolidation                   │
                     │ Human-review routing             │
                     └───────────────┬─────────────────┘
                                     │
              ┌──────────────────────┼───────────────────────┐
              │                      │                       │
              ▼                      ▼                       ▼
   ┌──────────────────┐   ┌──────────────────┐   ┌──────────────────┐
   │ CONTRACT REVIEW  │   │ LEGAL RESEARCH   │   │ COMPLIANCE/RISK  │
   │ AGENT            │   │ AGENT            │   │ AGENT            │
   │                  │   │                  │   │                  │
   │ Playbook         │   │ Legal Sources    │   │ Compliance Matrix│
   │ Clause Library   │   │ India Sources    │   │ Risk Controls    │
   └────────┬─────────┘   └────────┬─────────┘   └────────┬─────────┘
            │                      │                      │
            └──────────────────────┼──────────────────────┘
                                   ▼
                     ┌────────────────────────────┐
                     │ REDLINE / FALLBACK AGENT   │
                     │                            │
                     │ Approved fallback library  │
                     └──────────────┬─────────────┘
                                    │
                                    ▼
                     ┌────────────────────────────┐
                     │ CONSOLIDATED REVIEW PACKAGE│
                     └──────────────┬─────────────┘
                                    │
                                    ▼
                         ┌────────────────────┐
                         │ WORKFLOW           │
                         │ Generate Word      │
                         │ Store Document     │
                         └─────────┬──────────┘
                                   │
                                   ▼
                         ┌────────────────────┐
                         │ HUMAN APPROVAL     │
                         │ Legal Reviewer     │
                         └─────────┬──────────┘
                                   │
                    ┌──────────────┴───────────────┐
                    ▼                              ▼
                 APPROVED                       CHANGES
                    │                              │
                    ▼                              ▼
             Matter Update                  Revision Required
                    │
                    ▼
             Obligation Records
```

------------------------------------------------------------------------

# 63. Microsoft Documentation References

Use these as your implementation references.

## GitHub Copilot Harness

Microsoft Learn:
https://learn.microsoft.com/en-us/microsoft-copilot-studio/agents-experience/overview

## Agent instructions

https://learn.microsoft.com/en-us/microsoft-copilot-studio/agents-experience/authoring-instructions

## Knowledge

https://learn.microsoft.com/en-us/microsoft-copilot-studio/agents-experience/knowledge-add-existing-copilot

## Connected agents

https://learn.microsoft.com/en-us/microsoft-copilot-studio/agents-experience/authoring-add-other-agents

## Workflows

https://learn.microsoft.com/en-us/microsoft-copilot-studio/workflows-experience/flows-overview

## Agent node in workflow

https://learn.microsoft.com/en-us/microsoft-copilot-studio/workflows-experience/agent-node-workflow

## Testing

https://learn.microsoft.com/en-us/microsoft-copilot-studio/agents-experience/authoring-test-bot

## Evaluation

https://learn.microsoft.com/en-us/microsoft-copilot-studio/agents-experience/analytics-agent-evaluation-create

## Word Online connector

https://learn.microsoft.com/en-us/connectors/wordonlinebusiness/

------------------------------------------------------------------------

# 64. Final Build Checklist

## Environment

-   [ ] Correct Copilot Studio environment selected.
-   [ ] GitHub Copilot harness selected.
-   [ ] Required Copilot capacity/credits available.
-   [ ] SharePoint access confirmed.
-   [ ] Power Automate/workflow creation available.
-   [ ] Word Online (Business) connector available.
-   [ ] Approval connector available.

## Knowledge

-   [ ] India NDA playbook.
-   [ ] India Supplier Agreement playbook.
-   [ ] Clause library.
-   [ ] Fallback library.
-   [ ] Legal source set.
-   [ ] Compliance matrix.
-   [ ] Supplier risk matrix.

## Agents

-   [ ] Legal Review Orchestrator.
-   [ ] Contract Review Agent.
-   [ ] Legal Research Agent.
-   [ ] Compliance Risk Agent.
-   [ ] Redline Fallback Agent.

## Connected agents

-   [ ] Contract Review connected.
-   [ ] Legal Research connected.
-   [ ] Compliance Risk connected.
-   [ ] Redline connected.

## Workflows

-   [ ] Generate Review Memorandum.
-   [ ] Submit Legal Approval.
-   [ ] Finalize Approved Review.

## Word

-   [ ] Template created.
-   [ ] Content controls named uniquely.
-   [ ] Repeating sections configured.
-   [ ] Template stored in SharePoint.
-   [ ] Generated DOCX tested.

## Testing

-   [ ] Clean NDA.
-   [ ] Deviated NDA.
-   [ ] Clean supplier agreement.
-   [ ] Deviated supplier agreement.
-   [ ] Unsupported contract.
-   [ ] Unsupported jurisdiction.
-   [ ] Missing evidence.
-   [ ] Missing fallback.
-   [ ] Conflicting findings.

## Governance

-   [ ] No autonomous legal approval.
-   [ ] No unrestricted legal research.
-   [ ] Evidence included.
-   [ ] AI-generated output labelled.
-   [ ] Human approval required.
-   [ ] Synthetic contracts used for demo unless client approval exists.
-   [ ] Final output stored with audit metadata.

------------------------------------------------------------------------

# 65. Bottom Line

For this four-day POC, do not try to build a miniature legal platform.

Build a convincing **agentic review pipeline**:

``` text
CONTRACT
   ↓
ORCHESTRATOR
   ↓
CONTRACT ANALYSIS
   ↓
LEGAL RESEARCH
   ↓
COMPLIANCE/RISK
   ↓
APPROVED FALLBACKS
   ↓
CONSOLIDATED REVIEW
   ↓
WORD MEMORANDUM
   ↓
LEGAL HUMAN APPROVAL
   ↓
MATTER / OBLIGATIONS
```

The strongest architectural message for your internal review is:

> **Specialist agents perform reasoning within controlled knowledge
> boundaries; the Orchestrator coordinates and consolidates;
> deterministic workflows perform document generation and business
> actions; and an authorized legal professional retains final decision
> authority.**

That gives you an MVP that is small enough to build in four days while
still demonstrating the core agentic architecture described in the
original concept.
