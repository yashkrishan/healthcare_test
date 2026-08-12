# Engineering Specification — IntelliHub ViewKit: Chat-Driven Widget Composition

Siemens Healthineers–style collaborative engineering specification. Units are
filled from completed Team QA roles; unanswered roles remain `_Awaiting …_`.

Source requirements: `IntelliHub_ViewKit_Product_Requirements.docx`.

| Role | Status |
| --- | --- |
| Product Manager | Complete (3/3 answered) |
| Product Owner | Complete (3/3 answered) |
| Risk Manager | Complete (3/3 answered — answers thin, see §3/§4 and §8) |
| Domain Architect | Complete (2/2 answered — answers deferred, see §5.1/§5.2 and §8) |
| Team Architect | Complete (2/2 answered — answers deferred, see §5.3 and §8) |
| Test Manager | Complete (2/2 answered — answers thin, see §6 and §8) |

## Title Block

_Awaiting document control (author / approver / doc number)._

---

# 1 Introduction

_Source: Product Manager Team QA answers (complete), grounded in PRD §5, §6.4,
§7, §13._

## 1.1 Purpose

The chat-driven widget capability adds a natural-language way for users to create
and compose ViewKit dashboards. It is delivered as an **alternative entry point
onto the existing ViewKit Configurator** (PRD §13), not a replacement and not a
separate product (Team QA — "alternative entry point"). The no-code configurator
of §13 remains; chat is a second way in.

## 1.2 Scope

**In scope:** a chat-driven builder for composing/configuring ViewKit dashboards,
operating as an alternative entry point onto the §13 configurator.

**Boundary (Team QA — "chat driven builder"):** the deliverable is the
chat-driven builder itself. The Product Manager's answer does **not** explicitly
resolve whether the builder stays strictly within the library/configurator
boundary of §6.4 or introduces any standalone surface. Pending confirmation (see
§8), the spec assumes the builder stays within the ViewKit library/configurator
boundary and remains embedded in a host (per §5, AT icono via iframe), consistent
with §6.4 ("cannot be used as a standalone clinical application"). The widget
catalog is fixed to §12 (see §2.2, Product Owner).

## 1.3 Definitions and Abbreviations

### 1.3.1 Definitions

_Not separately provided; terms carry from the PRD (ViewKit, CIH, cPDM, Patient
Twin, PCI, TAVI)._

### 1.3.2 Abbreviations

_Not separately provided by the Product Manager._

## 1.4 References

- `IntelliHub_ViewKit_Product_Requirements.docx` — §5, §6.4, §7, §12, §13.

## 1.5 Applicable Standard, Law or Regulation

_Not specified by the Product Manager in this batch._

## 1.6 Additional Literature (informal)

_Awaiting Product Manager (not provided)._

---

# 2 Requirement Management

## 2.1 Intended Use (anticipated)

_Source: Product Manager Team QA answers (complete)._

**Intended user (Team QA — "at end-clinicians"):** the chat interface is aimed at
**end-clinicians at the point of care**, not solely the authorized configuration
user (Developer) who uses the no-code configurator today. Of the §7 user groups,
this points at the Cardiologist / Radiologist rather than the Developer.

**Intended use:** end-clinicians use natural language to compose and configure the
patient-information dashboards they need at the point of care, drawing on the
fixed ViewKit widget catalog (§12) and CIH-sourced data, through the same
configurator machinery as §13.

**Cross-role tension (handoff, see §8):** the Product Manager places the chat user
at end-clinicians at the point of care, while the Product Owner (§2.2) framed chat
as an alternative to the no-code configurator historically used by authorized
configuration users. This raises escalation items for the Risk Manager (does
point-of-care, end-clinician authoring change the safety class / require a review
gate?) and the Domain/Team Architect (does point-of-care use change placement of
the chat feature and its confirmation step?).

## 2.2 Software Features

_Source: Product Owner Team QA answers (complete), grounded in PRD §12 and §13._

### 2.2.1 Feature — Chat-Driven Dashboard Composition

The chat capability is an alternative, natural-language entry point onto the
existing ViewKit Configurator (PRD §13). It does **not** introduce a new widget
type or a new widget-authoring path.

"Creating a widget through chat" means the user **selects and configures one of
the existing widget types** from the fixed §12 catalog (Team QA answer "a").
Authoring new widget types or new CIH data bindings is **out of scope**. The
catalog the chat flow operates over:

| Widget (PRD §12) | Configurable via chat |
| --- | --- |
| Patient Demographics Widget (§12.1) | Yes — select + configure |
| Planned Procedure Widget (§12.2) | Yes — select + configure |
| Image Viewer Widget (§12.3) | Yes — select + configure |
| Prior Angiography Preview Widget (§12.4) | Yes — select + configure |

### 2.2.2 Mapping to Software Items

Chat **produces the same configuration artifact as the no-code configurator**
(Team QA answer "yes"). Chat and the §13 no-code configurator are two front ends
over **one shared dashboard-definition artifact**: a dashboard created or edited
by chat is indistinguishable from, and editable by, the no-code configurator, and
vice versa. Chat must not introduce a parallel or divergent dashboard model.

_Exact software-item ids for the chat front end and language-to-config
translation are owned by the Team Architect (unit 5.3) — deferred by that role
(see §5.3, §8)._

### 2.2.3 Feature Specification / Acceptance Behavior

**2.2.3.1 Natural-language configuration actions.** The §13.1 configurator
actions must be expressible in natural language and must resolve to the shared
dashboard definition:

- Compose a dashboard from existing widgets.
- Choose which widgets appear on a dashboard.
- Choose which disease-specific CIH data each widget displays.
- Choose which information is displayed, and its order and presentation.
- Create different views of the same information for different clinical contexts
  and user roles.

Acceptance: any dashboard state reachable through chat must be equivalently
reachable and editable through the no-code configurator, because both operate on
the same artifact.

**2.2.3.1.1 Context-driven composition with confirmation (Team QA answer
"yes").** Chat **shall** let a user drive the procedure/context mapping raised as
the open design question in PRD §12.2 — e.g. "make a PCI dashboard" auto-selects
PCI-relevant widgets, fields, and CIH data elements (target vessel, stent type,
access route); a TAVI request selects valve-related content.

Because chat auto-selects clinical content, the user **shall be shown a
confirmation/preview** of the resulting dashboard (widgets, fields, and data
bindings) **before it is saved**. A chat-generated dashboard is not persisted
until the user confirms the previewed configuration.

---

# 3 Risk Management

_Source: Risk Manager Team QA answers (complete). The three answers are terse and
do not fully specify a classification or a risk table; content is attributed only
as far as each answer states, with the remainder tracked in §8._

## 3.1 Software Safety Classification

**Not resolved by the Risk Manager.** The safety-class question (whether a
chat/LLM-driven config generator that determines which patient data a cardiologist
sees before a procedure changes the classification implied by §8.2 "no direct
clinical benefit" and §6.4 "no standalone clinical use") was answered
"`anuthing`" — this does not name a class or rationale.

No safety class can be recorded without fabrication. The classification remains
**open** (§8), sharpened by the §2.1 point-of-care / end-clinician cross-role
tension.

## 3.2 Legend for Risk Analysis Table

_Not provided by the Risk Manager._

## 3.3 Risks

The Risk Manager's answer to the misconfiguration/hallucination question (chat
omitting a required patient-verification field or the wrong widget from a
generated dashboard) was "`mandatoru fileds`", read as pointing to **mandatory
fields** as the mitigation direction.

Attributable content (only as stated):

- **Hazard direction:** a chat-generated dashboard omits a required §12.1
  patient-verification field ("Who is the patient on the table?") or a required
  widget, or omits a §12.1/§12.2/§12.4 fallback state for missing data.
- **Mitigation direction named by the Risk Manager:** mandatory fields — required
  §12 verification fields must be enforced (not omittable) in any chat-generated
  configuration.

This aligns with the §2.2.3.1.1 confirmation/preview-before-save gate, but the
Risk Manager did **not** specify a full risk table (function / harm / hazard /
cause / assessment / affected party) nor confirm a human-review gate. The complete
risk analysis remains **open** (§8).

---

# 4 Security and Data Privacy Requirements

_Source: Risk Manager Team QA answers (complete). The privacy/security answer was
"`yes`", affirming that controls apply but naming no specifics._

## 4.1 Software PSS Classification

The Risk Manager affirmed ("yes") that privacy/security controls apply to chat
over CIH-sourced data (prompts/generated configs may reference PHI — FHIR Patient
from cPDM §12.1; DICOM imagery §12.3–12.4). **No PSS class was named.** The
classification remains **open** (§8).

## 4.2 Data Privacy Requirements

Affirmed in principle (Risk Manager "yes"): PHI handling constraints apply. The
Risk Manager did **not** state whether patient data may leave CIH boundaries or
which data-handling constraints bind an NLP/LLM backend. This directly gates the
Team Architect's OTS decision (§5.3.2, self-hosted vs external). **Open** (§8).

## 4.3 Security Requirements

Affirmed in principle (Risk Manager "yes"). No concrete security requirements
(access control, boundary controls) were enumerated. **Open** (§8).

---

# 5 Design Considerations

## 5.1 Architecture Context

_Source: Domain Architect Team QA answer (complete)._ **Deferred** — the Domain
Architect answered "`choose wosely`" and did not fix where chat sits (ViewKit
library / configurator tier / new backend) or its neighbors (CIH, cPDM, NLP/LLM).

Attributable only from other completed roles: chat is an alternative entry point
onto the §13 configurator (§1.1), embedded in a host per §5, and edits the shared
dashboard-definition artifact (§2.2.2). The placement of the chat component and any
NLP/LLM service in the §5 SCIM/CIH context is **open** (§8).

## 5.2 Considerations of Cross-Cutting Concerns and Software Qualities

_Source: Domain Architect Team QA answer (complete)._ **Deferred** — answered
"`all ok`". No dominant quality was chosen and no targets were set for
chat-to-render latency, determinism/reproducibility of generated configs, or
embeddability across AT/DI/VAR hosts. The §10 core requirements (VK-CORE-002/004/
005) still apply from the PRD but were not ranked or quantified for chat. **Open**
(§8).

## 5.3 Software Items

_Source: Team Architect Team QA answers (complete)._ Both answers **defer** the
decision to the author; no item list or OTS vendor is attributable without
fabrication.

### 5.3.1 List of Software Items

**Deferred by the Team Architect** ("`choose yourself`"). No authoritative new
item list was provided. The only fixed items are the pre-existing §12 widgets and
§13 configurator, plus the single shared dashboard-definition artifact established
by the Product Owner (§2.2.2), whose owning software item is not yet named.
Candidate new items (chat UI surface, natural-language → configuration translator,
context/procedure mapper) are **not** confirmed. **Open** (§8).

### 5.3.2 List of Off-the-Shelf Software

**Deferred by the Team Architect** ("`upto you`"). No OTS/LLM candidate named, and
the PHI self-hosting question is left unresolved. Must be reconciled with the Risk
Manager's §4.2 CIH-boundary constraint (also open). **Open** (§8).

### 5.3.3 List of Supplied Software

_Not addressed by the Team Architect._

### 5.3.4 Software Item details

_No item instances specified (5.3.1 deferred)._

### 5.3.5 OTS details

_No OTS entries specified (5.3.2 deferred)._

## 5.4 Views

### 5.4.1 Static View

_Cannot be drawn: 5.1 architecture context deferred (Domain Architect)._

### 5.4.2 Dynamic View

_Cannot be drawn: 5.1 architecture context deferred (Domain Architect)._

### 5.4.3 Process View

_Not addressed by the Team Architect (optional unit)._

### 5.4.4 Development View

_Not addressed by the Domain Architect._

---

# 6 Testing

_Source: Test Manager Team QA answers (complete). Both answers are terse; content
is attributed only as far as stated._

**Test level (Team QA — "`UNIT`"):** the Test Manager named **unit-level** testing
for the chat feature. The multi-level ask (unit / config-generation / end-to-end
embed in the AT iframe) was not elaborated beyond the unit level; the additional
levels and environments remain **open** (§8).

**Evidence (Team QA — "`no standalone clinical use`"):** the required evidence the
Test Manager named is that **no chat output violates §6.4 (no standalone clinical
use)**. The broader evidence ask — a corpus of prompts mapped to expected
dashboard configs, coverage of the four §12 widget types, and validation that
required verification fields (§12.1) are never omitted — was not explicitly
confirmed and remains **open** (§8), though it is consistent with the §3.3
mandatory-fields mitigation and the §2.2.3.1.1 preview gate.

**Attributable acceptance target:** every chat-generated dashboard must be
verified not to constitute or enable a standalone clinical application (§6.4), and
required §12 widgets/fields/fallbacks must be checked — verification method beyond
unit level to be defined.

---

# 7 Information for Manufacturer

_Derived at assemble time from risk mitigations. The Risk Manager's inputs are too
thin to derive manufacturer information: no safety class (§3.1), no PSS class
(§4.1), and no complete risk table (§3.3). The one attributable mitigation is
enforcement of mandatory §12 verification fields in chat-generated dashboards.
Remainder awaits substantive Risk Manager input (§8)._

---

# 8 Open issues

Tracked from unanswered / blocked / deferred Team QA items and cross-role handoffs:

- **Standalone-boundary confirmation (§1.2):** the Product Manager named the
  "chat driven builder" as the deliverable but did not explicitly confirm it stays
  within the §6.4 library/configurator boundary (no standalone app). Needs Product
  Manager confirmation. (Note: the Test Manager's evidence answer — "no standalone
  clinical use" — assumes this boundary holds.)
- **Point-of-care user vs configurator user (§2.1 ↔ §2.2):** Product Manager
  targets end-clinicians at the point of care; Product Owner framed chat as an
  alternative to the configuration-user workflow. Resolve intended-user scope and
  its downstream safety/architecture impact.
- **Safety classification unresolved (§3.1):** Risk Manager answered "anuthing" —
  no class or rationale for a chat/LLM-driven config generator. Sharpened by the
  point-of-care end-clinician authoring tension.
- **Risk table incomplete (§3.3):** only a mitigation direction (mandatory
  verification fields) was given; the full function/harm/hazard/cause/assessment/
  affected-party analysis and any human-review gate are unspecified.
- **PSS / privacy / security specifics (§4):** Risk Manager affirmed controls apply
  ("yes") but named no PSS class, no CIH-boundary rule for PHI, and no concrete
  security requirements. Gates the Team Architect OTS decision.
- **Architecture placement deferred (§5.1):** Domain Architect ("choose wosely")
  did not fix the tier hosting chat or its NLP/LLM neighbors. Blocks the static/
  dynamic views (§5.4).
- **Quality goals deferred (§5.2):** Domain Architect ("all ok") set no dominant
  quality and no targets for latency, determinism, or embeddability.
- **Software-item decomposition deferred (§5.3.1):** Team Architect ("choose
  yourself") provided no item list with ids.
- **OTS / LLM dependency deferred (§5.3.2):** Team Architect ("upto you") named no
  OTS candidate; must be reconciled with the unresolved PHI / CIH-boundary
  constraint (§4.2) — self-hosted vs external NLP/LLM.
- **Test strategy beyond unit level (§6):** Test Manager named unit-level testing
  and the no-standalone-use evidence only; config-generation and end-to-end iframe
  levels, environments, and a prompt→expected-config corpus with four-widget
  coverage remain to be defined.
