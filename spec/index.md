# IntelliHub ViewKit — Behavioral Spec

Source of truth for *what* IntelliHub ViewKit does. Derived from
`IntelliHub_ViewKit_Product_Requirements.docx` (Siemens Healthineers,
10 August 2026). Where this spec and the PRD disagree, this spec wins;
open questions are called out explicitly and must be resolved before the
affected behavior is implemented.

## What ViewKit is

ViewKit is a reusable, composable front-end widget toolkit that lets clinical
applications display structured patient information from the Clinical
Information Hub (CIH). It is built once and configured per clinical context,
avoiding a bespoke CIH display integration for every product.

- **Not** a standalone medical device or standalone clinical application.
- **Only** a library of reusable widgets for displaying structured patient data.
- Performs **no** clinical function, ML, data generation, or decision-making —
  those belong to upstream data sources (CIH / Patient Twin) or the consuming
  application.

First use case: the **AT Cardio Dashboard** for interventional cardiologists,
embedded in AT icono layouts via an iframe (AT hosts the container; SCIM/SSC
provides the ViewKit components).

## Read order

1. **This file** — scope, glossary, boundaries.
2. **[core.md](core.md)** — foundational requirements every widget/host relies on (VK-CORE-*).
3. **[widgets.md](widgets.md)** — behavior of each dashboard widget.
4. **[configurator.md](configurator.md)** — no-code dashboard configuration.

## Users

| Role | Task | Relationship to ViewKit |
|---|---|---|
| Developer | Create and configure dashboards | Integrates ViewKit and configures reusable widgets |
| Radiologist | Clinical diagnosis and reporting | Uses an application that incorporates ViewKit widgets |
| Cardiologist | Perform a procedure | Uses procedure-focused dashboards such as the AT Cardio Dashboard |

## Intended use / boundaries

- **Intended use:** visualizing structured patient information from CIH —
  radiology findings, laboratory values, procedure-related information,
  imaging previews, patient demographics. Usable in medical and non-medical
  applications.
- **Indications for use:** reusable widgets for developers to build
  disease-specific dashboards integrated into medical and non-medical apps.
- **Limitations:** cannot be used as a standalone medical device or standalone
  clinical application; operates only as a widget library.
- **Not applicable:** intended-purpose statement, contraindications, intended
  patient population, device/body interaction.
- **Clinical benefit:** ViewKit provides no direct clinical benefit itself; any
  benefit is defined by the consuming application's intended-purpose docs.

## Glossary

- **CIH** — Clinical Information Hub; the data source ViewKit reads from.
- **Patient Twin** — CIH populated with structured clinical data (AI results
  such as DICOM SR, DICOM imaging metadata from Helios, EMR data from LUKS/Epic).
- **cPDM** — store where the FHIR Patient resource is available.
- **Consumer business lines** — AI Widget team; Advanced Therapies (AT),
  including the AT Cardio Dashboard; Helios Patient Map; LUKS / Epic EMR; and
  potential future consumers.
- **Host** — the SHS product frontend that embeds ViewKit widgets.

## Requirement ID scheme

- `VK-CORE-NNN` — core toolkit requirements (see core.md).
- `VK-WIDGET-<name>-NNN` — per-widget requirements (see widgets.md).
- `VK-CONF-NNN` — configurator requirements (see configurator.md).

Requirements use "shall" for mandatory behavior. Open design questions are
marked **OPEN** and block the requirements they touch.
