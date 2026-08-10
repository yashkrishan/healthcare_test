# ViewKit (IntelliHub Widget Library) — Behavioral Spec

Source: `IntelliHub_ViewKit_Product_Requirements.docx` (10 Aug 2026).
This spec restates the PRD as testable behavioral contracts. Each contract carries an
ID (`VK-*`). PRD requirement IDs (`VK-CORE-*`) are preserved for traceability.

## Terminology

- **CIH** — Clinical Information Hub. The source of structured patient data.
- **Patient Twin** — CIH populated with structured clinical data (AI results / DICOM
  SR, DICOM imaging metadata, EMR data such as LUKS / Epic).
- **ViewKit** — the reusable, composable front-end widget toolkit this spec defines.
- **Widget** — a self-contained, CIH-connected display component.
- **Dashboard** — a composition of widgets configured for a clinical context.
- **Host** — the SHS product application that embeds ViewKit (e.g. AT icono via iframe).
- **Configurator** — the no-code capability for composing and tailoring dashboards.
- **Context** — the resolved combination of clinical context, procedure, user role,
  available CIH data, and dashboard configuration that drives rendering.

## Product boundaries (normative)

- **VK-BND-001** ViewKit is a library of reusable widgets only. It MUST NOT operate as a
  standalone medical device or a standalone clinical application.
- **VK-BND-002** ViewKit MUST NOT implement machine-learning algorithms, generate data,
  interpret data clinically, or make clinical decisions. It only visualizes structured
  data made available by CIH; those functions belong to upstream sources or the
  consuming application.
- **VK-BND-003** ViewKit MUST be usable within both medical and non-medical applications.

## Core contracts

### VK-CORE-001 — Reusable widget toolkit
- **VK-C1.1** ViewKit MUST expose a reusable widget toolkit that lets any clinical
  frontend display structured patient information from CIH.
- **VK-C1.2** Displayed information MUST be configurable based on clinical data (not
  hard-coded per integration).

### VK-CORE-002 — Role and context adaptation
- **VK-C2.1** A widget's displayed information MUST adapt to the active **user role**.
- **VK-C2.2** A widget's displayed information MUST adapt to the active **clinical
  context** (context, procedure, available data, dashboard configuration).
- **VK-C2.3** The same underlying information MUST be presentable as different views for
  different roles/contexts without changing widget code.

### VK-CORE-003 — Reusable CIH-connected components
- **VK-C3.1** Display components MUST read structured patient data directly from CIH.
- **VK-C3.2** Rendering MUST require no use-case-specific display development — a widget
  is integrated and configured, not re-implemented per consumer.

### VK-CORE-004 — Data-driven rendering
- **VK-C4.1** Widget **content** MUST be driven by structured CIH data.
- **VK-C4.2** Widget **visibility** MUST be driven by structured CIH data: a widget
  activates only when relevant patient data is available in CIH.

### VK-CORE-005 — Frontend-agnostic integration
- **VK-C5.1** Widgets MUST be embeddable in any SHS product host, independent of the
  host's frontend technology.
- **VK-C5.2** Embedding MUST support the AT icono iframe model, where the host provides
  the container and hosting layout and ViewKit provides the components.

## Cross-cutting rendering rules

- **VK-REN-001 (fallbacks)** When a field's data is missing, a widget MUST render an
  explicit fallback (e.g. "Unknown", or a widget-level empty-state message) rather than
  an empty field.
- **VK-REN-002 (context resolution)** Each widget MUST resolve its content from the
  active context. For coded clinical concepts (e.g. procedure type), resolution MUST use
  a **configurable mapping** from the coded concept to the relevant widgets, fields,
  layouts, and clinical data elements. *(PRD open design question §12.2 — the mapping is
  the anticipated mechanism; exact schema TBD.)*

## Widget contracts

### VK-W-DEMO — Patient Demographics Widget
Goal: let a clinician confirm the correct patient and key context before a procedure
("Who is the patient on the table? Is the patient ready?").
- **VK-W-DEMO-1** MUST display the patient's full name prominently and clearly.
- **VK-W-DEMO-2** MUST display date of birth.
- **VK-W-DEMO-3** MUST display an age automatically calculated from date of birth.
- **VK-W-DEMO-4** MUST display patient sex with a clear icon for rapid visual
  recognition.
- **VK-W-DEMO-5** MUST show a fallback value such as "Unknown" for any missing field
  (per VK-REN-001).
- **Data source:** FHIR Patient resource in cPDM, SHS Patient — SHS Common Data Elements
  v1.7.26052602.

### VK-W-PROC — Planned Procedure Widget
Goal: let a clinician understand what is planned and how before starting the case.
- **VK-W-PROC-1** MUST display the planned procedure name prominently (e.g.
  "PCI prox. LCX").
- **VK-W-PROC-2** MUST display a procedure-type icon for quick recognition (e.g. PCI,
  diagnostic angiography).
- **VK-W-PROC-3** MUST display scheduled date and time.
- **VK-W-PROC-4** MUST display location (e.g. catheterization laboratory room).
- **VK-W-PROC-5** When planning data is available, MUST display a concise planning
  summary including target vessel, stent type (if defined), and access route.
- **VK-W-PROC-6** When the procedure name or planning data is unavailable, MUST display a
  fallback message such as "No pre-procedure planning data available".
- **VK-W-PROC-7 (context-driven content)** Content MUST adapt to the planned procedure:
  a PCI procedure shows PCI-specific planning (target vessel, stent type); a TAVI
  procedure shows valve-related information. Selection MUST follow VK-REN-002.

### VK-W-IMG — Image Viewer Widget
Goal: display visual evidence associated with a clinical finding.
- **VK-W-IMG-1 (Phase 1 scope)** MUST support static DICOM secondary-capture images.
- **VK-W-IMG-2** A full diagnostic image viewer is OUT OF SCOPE for Phase 1.
- **VK-W-IMG-3** Widget content MUST change based on dashboard context.
- **VK-W-IMG-4 (AT Cardio context)** When available, MUST display a static preview image
  from the patient's coronary CT angiography (may highlight anatomy or lesions).

### VK-W-ANGIO — Prior Angiography Preview Widget
Goal: let a cardiologist recall anatomy and prior interventions to plan the PCI.
- **VK-W-ANGIO-1** MUST display representative images or scenes from prior invasive
  coronary angiographies.
- **VK-W-ANGIO-2** MUST support rapid review of coronary anatomy.
- **VK-W-ANGIO-3** MUST enable awareness of prior interventions where visible in the
  available images.
- **VK-W-ANGIO-4** MUST gracefully handle missing historical angiography data with an
  appropriate empty state or fallback message (per VK-REN-001).

## Configurator contracts

### VK-CFG — No-code dashboard configuration
Objective: let clinical/consumer teams configure views without ViewKit engineering
changes.
- **VK-CFG-1** MUST allow authorized users to compose dashboards from existing widgets.
- **VK-CFG-2** MUST allow configuring which widgets appear on a dashboard.
- **VK-CFG-3** MUST allow configuring which disease-specific data each widget displays.
- **VK-CFG-4** MUST allow configuring which information is displayed within a widget.
- **VK-CFG-5** MUST allow configuring the order and presentation of information.
- **VK-CFG-6** MUST allow creating different views of the same information for different
  clinical contexts and user roles.
- **VK-CFG-7** MUST allow reusing the same widget library across products and
  disease-specific dashboards.
- **VK-CFG-8** All of the above MUST be achievable with no ViewKit code changes (no-code).

### VK-CFG-MODEL — Operating model & key principle
- **VK-CFG-MODEL-1** An in-house factory, AT, or another consumer team MUST be able to
  take the ViewKit library and configure it into a new clinical view or dashboard.
- **VK-CFG-MODEL-2 (key principle)** A user selects the *kind* of information to display;
  ViewKit provides multiple configurable views/renderings of that information based on
  the selected clinical context, procedure, user role, available CIH data, and dashboard
  configuration.

## User groups (informative)

| Role | Task | Requirement / competency |
|---|---|---|
| Developer | Create and configure dashboards | Ability to integrate ViewKit and configure reusable widgets |
| Radiologist | Perform clinical diagnosis and reporting | Uses an application that incorporates ViewKit widgets |
| Cardiologist | Perform a procedure | Uses procedure-focused dashboards such as the AT Cardio Dashboard |

## First use case (informative): AT Cardio Dashboard

- First dashboard built on ViewKit; target user is an interventional cardiologist who
  needs structured patient and procedural information before and during a procedure.
- Embedded in AT icono layouts via iframe: AT provides the container/hosting layout;
  SCIM / SSC provides the ViewKit components.
- Composition: Patient Demographics (VK-W-DEMO), Planned Procedure (VK-W-PROC), Image
  Viewer (VK-W-IMG), and Prior Angiography Preview (VK-W-ANGIO).

## Open questions (from PRD)

- **OQ-1 (§12.2)** How is relevant content identified based on the procedure? Working
  answer: a configurable mapping from procedure type / coded procedure information to the
  relevant set of widgets, fields, layouts, and clinical data elements (see VK-REN-002).
  Exact mapping schema and authoring surface are TBD.
