# Imaging Review Module — Behavioral Spec

Status: Draft (Phase 1)
Source: IntelliHub_ViewKit_Product_Requirements.docx §12.3, §12.4 (with §6, §8, §10, §13)
Scope owner: SCIM / SSC (ViewKit); first consumer: Advanced Therapies (AT Cardio Dashboard)

## 1. Purpose and scope

The imaging review module is the imaging-facing part of the IntelliHub ViewKit toolkit.
It provides two reusable, CIH-connected, frontend-agnostic widgets that display visual
evidence to a clinician on a configured dashboard:

- **Image Viewer Widget** — displays a static image associated with a clinical finding
  (§12.3).
- **Prior Angiography Preview Widget** — displays representative images/scenes from the
  patient's prior invasive coronary angiographies (§12.4).

The module is display-only. It performs no clinical interpretation, generates no data,
and is not a standalone medical device or clinical application (§6.4, §8). Any clinical
benefit is owned by the consuming application (§8.2).

### In scope (Phase 1)
- Static DICOM secondary-capture images.
- Context-driven content selection and visibility.
- Empty-state / fallback rendering when imaging data is absent.
- Embedding in any SHS product host (via the AT iframe container for the first use case).

### Out of scope (Phase 1)
- A full diagnostic image viewer (explicitly not required — §12.3).
- Windowing/level, zoom/pan, measurement, annotation-authoring tools.
- Writing back to CIH or any image processing/AI inference (§8.1).

## 2. Actors and context
- **Interventional cardiologist** — primary viewer on the AT Cardio Dashboard (§5, §7).
- **Radiologist** — viewer in other ViewKit-based applications (§7).
- **Developer / configuration user** — composes and configures the widgets via the
  no-code configurator (§7, §13); does not require ViewKit engineering changes.

Rendering is driven by: selected clinical context, procedure, user role, available CIH
data, and dashboard configuration (§13.3). A widget activates only when relevant patient
data is available in CIH (§10 VK-CORE-004).

## 3. Data source
- Images are read from CIH (the Patient Twin), sourced from DICOM imaging metadata and
  DICOM secondary-capture instances (§4, §12.3).
- The module reads only; it never mutates CIH data (§10 VK-CORE-003).

## 4. Requirements

### 4.1 Image Viewer Widget (IRM-VIEWER)

- **IRM-VIEWER-001 — Static image display.** The widget shall display a static DICOM
  secondary-capture image associated with a clinical finding. It shall not require or
  provide full diagnostic-viewer capabilities.
- **IRM-VIEWER-002 — Context-driven content.** The image shown shall be determined by
  dashboard context. In the AT Cardio Dashboard context, when available, the widget
  shall display a static preview image from the patient's coronary CT angiography
  (which may highlight anatomy or lesions).
- **IRM-VIEWER-003 — Data-driven visibility.** The widget's content and visibility shall
  be driven by structured CIH data (§10 VK-CORE-004). When no associated image is
  available in CIH for the active context, the widget shall render an empty state /
  fallback rather than a broken or empty frame.
- **IRM-VIEWER-004 — Read-only.** The widget shall not offer authoring, measurement, or
  image-modification actions in Phase 1.

### 4.2 Prior Angiography Preview Widget (IRM-ANGIO)

- **IRM-ANGIO-001 — Representative prior images.** The widget shall display representative
  images or scenes from the patient's prior invasive coronary angiographies to support
  rapid recall of coronary anatomy.
- **IRM-ANGIO-002 — Recency.** When multiple prior studies exist, the widget shall
  present the patient's latest coronary angiographies (§12.4 user story: "latest").
- **IRM-ANGIO-003 — Prior-intervention awareness.** The widget shall surface prior
  interventions where they are visible in the available images (no inference is
  performed; visibility depends on the source images).
- **IRM-ANGIO-004 — Missing-data handling.** When historical angiography data is absent,
  the widget shall render an appropriate empty state / fallback message rather than an
  empty field.

### 4.3 Shared module behavior

- **IRM-CORE-001 — Frontend-agnostic embedding.** Both widgets shall be embeddable in any
  SHS product host (§10 VK-CORE-005); the AT Cardio Dashboard embeds them via iframe (§5).
- **IRM-CORE-002 — Role and context adaptation.** Widget presence, ordering, and content
  shall be configurable by user role and clinical context through the no-code
  configurator without ViewKit engineering changes (§10 VK-CORE-002, §13).
- **IRM-CORE-003 — Explicit fallbacks.** Every absent-data path shall render a clear,
  human-readable empty state (never a blank or error-looking frame), consistent with the
  demographics/procedure widgets' "Unknown" / "No … data available" pattern (§12.1, §12.2).
- **IRM-CORE-004 — No clinical function.** The module shall not compute, interpret, or
  alter clinical data; it visualizes CIH-provided images only (§8.1).

## 5. Acceptance scenarios (Gherkin-style)

```
Scenario: CT angiography preview available (AT Cardio context)
  Given the dashboard context is AT Cardio Dashboard
  And CIH holds a coronary CT angiography secondary-capture image for the patient
  When the Image Viewer Widget renders
  Then it displays the static preview image
  And it exposes no diagnostic-viewer or editing controls

Scenario: No finding image available
  Given the active context has no associated image in CIH
  When the Image Viewer Widget renders
  Then it displays a clear empty-state message and no broken frame

Scenario: Prior angiography available
  Given CIH holds one or more prior invasive coronary angiography studies
  When the Prior Angiography Preview Widget renders
  Then it shows representative images/scenes from the latest study/studies

Scenario: No prior angiography
  Given CIH holds no historical angiography data for the patient
  When the Prior Angiography Preview Widget renders
  Then it displays an appropriate fallback message
```

## 6. Open questions
- **OQ-1 (clinical, unresolved):** Is a single static representative frame sufficient for
  prior angiography review, or is basic cine/scroll expected in Phase 1? PRD §12.4 says
  "images or scenes"; §12.3 constrains Phase 1 to static images. Pending clinical
  specialist confirmation.
- **OQ-2 (§12.2 open design question, applies here):** How is the relevant image/context
  identified from the procedure? The design should anticipate a configurable mapping
  between procedure type / coded procedure info and the imaging content shown (§13.3).
- **OQ-3:** Selection rule when multiple representative frames or multiple prior studies
  qualify (which frame, how many, ordering).
- **OQ-4:** Required source image formats beyond DICOM secondary-capture (e.g. rendered
  JPEG/PNG previews already in CIH), and behavior for unsupported instances.
