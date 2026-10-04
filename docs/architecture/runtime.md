# Production Core Runtime Architecture

This document describes the current high-level runtime architecture of Production Core SDK and the path from project open to an operational application workspace.

Production Core is under active development. This document describes verified current behavior and intentionally avoids documenting internal implementation details that are not yet part of the public SDK surface.

---

## Runtime Objective

Production Core is designed so that opening the TouchDesigner project can transition into a usable application runtime without requiring the operator to manually assemble or populate the interface.

At the current milestone, the runtime is responsible for:

- Establishing application lifecycle state.
- Bootstrapping MAIN_UI runtime bindings.
- Populating the application workspace.
- Synchronizing canonical navigation state with rendered UI state.
- Rendering runtime-driven System Map content.
- Maintaining defensive behavior when UI input is invalid.

---

## Cold-Start Flow

```text
PROJECT OPEN
     │
     ▼
AppExtension
Ready / booted=True / mode=dev
     │
     ▼
MainUIRuntimeBinder
bootstrapped=True
     │
     ▼
MAIN_UI
     │
     ├── Header
     ├── Sidebar
     ├── Workspace
     └── Footer
              │
              ▼
       Workspace Pages
              │
              ├── Dashboard
              ├── System Map
              ├── PTZ
              ├── Media
              ├── Modules
              ├── Devices
              ├── Network
              ├── OSC
              ├── Logs
              └── Settings
```

This sequence represents the current verified application startup path at a conceptual level.

---

## Application Lifecycle

### AppExtension

`AppExtension` participates in the application lifecycle and exposes the verified runtime state:

```text
Ready
booted=True
mode=dev
```

The autonomous cold-start milestone establishes that Production Core can move from project open into its application runtime without requiring manual UI population.

### MainUIRuntimeBinder

`MainUIRuntimeBinder` connects the application runtime to MAIN_UI.

Current verified state:

```text
bootstrapped=True
```

At the current milestone, the binder participates in bringing the application shell into an operational state and connecting runtime behavior to the interface.

---

## MAIN_UI Composition

MAIN_UI is organized around four primary interface regions:

```text
┌─────────────────────────────────────────────┐
│                   HEADER                    │
├──────────────┬──────────────────────────────┤
│              │                              │
│   SIDEBAR    │          WORKSPACE           │
│              │                              │
├──────────────┴──────────────────────────────┤
│                   FOOTER                    │
└─────────────────────────────────────────────┘
```

### Header

Provides the upper application region and participates in the integrated runtime shell.

### Sidebar

Provides navigation across the ten canonical workspace pages and reflects active-page state visually.

### Workspace

Hosts the currently selected application page and runtime-driven content such as the System Map.

### Footer

Provides the lower application region and is integrated into the runtime shell.

---

## Workspace Architecture

The current application workspace exposes ten canonical pages:

| Index | Page |
| ---: | --- |
| 0 | Dashboard |
| 1 | System Map |
| 2 | PTZ |
| 3 | Media |
| 4 | Modules |
| 5 | Devices |
| 6 | Network |
| 7 | OSC |
| 8 | Logs |
| 9 | Settings |

Navigation is synchronized with canonical application state so the selected page, rendered workspace, and sidebar highlight remain aligned.

---

## Navigation Data Flow

A key runtime contract exists between canonical navigation data and the visible sidebar.

Conceptually:

```text
navigation_items
      │
      │ canonical source includes header
      ▼
   select1
      │
      │ header removed
      ▼
     in1
      │
      ▼
  List COMP
      │
      │ visible rows are zero-based
      ▼
sidebar callback
      │
      ▼
canonical page ID
      │
      ▼
application state / workspace
```

The rendered List coordinate space is therefore not identical to the original source-table coordinate space.

Once the header has been removed, visible row indices already map directly to the page ordering used by the sidebar.

---

## Sprint 16.3A — Coordinate-Space Contract

Sprint 16.3A corrected an off-by-one navigation defect caused by applying a source-table header offset to a row that had already entered the transformed, zero-based List coordinate space.

The previous behavior could produce mappings such as:

```text
PTZ click
   │
   ▼
visible row 2
   │
   ▼
header offset applied again
   │
   ▼
row 1
   │
   ▼
System Map   ✗
```

The corrected behavior is:

```text
PTZ click
   │
   ▼
visible row 2
   │
   ▼
direct visible-row mapping
   │
   ▼
page ID: ptz   ✓
```

### Verified Contract

```python
cb._ListRowToVisibleRow(..., 2) == 2
sb.GetPageIdForVisibleRow(2) == 'ptz'
cb._ListRowToVisibleRow(..., -1) is None
```

All checks passed.

Operator testing across all ten sidebar pages subsequently verified correct page selection and active-page behavior.

### Architectural Lesson

> A transformed UI dataset must define its own coordinate space. Mapping between canonical source data and rendered UI data should be explicit, testable, and applied exactly once.

This principle reduces hidden assumptions between data preparation, UI rendering, callbacks, and application state.

---

## Canonical Navigation and State

Production Core treats navigation as application state rather than purely visual UI behavior.

The intended relationship is:

```text
USER INPUT
    │
    ▼
SIDEBAR
    │
    ▼
PAGE ID
    │
    ▼
CANONICAL STATE
    │
    ├──────────────► ACTIVE SIDEBAR HIGHLIGHT
    │
    └──────────────► WORKSPACE PAGE
```

This keeps the rendered interface aligned with the application's current navigation truth.

---

## System Map Runtime Integration

The System Map is integrated into the actual MAIN_UI workspace.

Current verified behavior includes:

- System Map nodes render inside the application workspace.
- Node widgets currently use placeholder labels; device-specific metadata and labeling are not yet implemented.
- The operator can navigate away from and back to System Map through the sidebar.
- System Map navigation participates in the same canonical page/state synchronization as the other workspace pages.

This milestone moves the System Map from isolated development infrastructure into visible application behavior while keeping device-specific metadata and labeling as future integration work.

---

## Defensive Runtime Behavior

Navigation behavior includes defensive handling for invalid rows.

Verified behavior includes:

```python
cb._ListRowToVisibleRow(..., -1) is None
```

Invalid input should not be coerced into a valid page selection. Returning no valid row preserves the navigation contract and prevents unintended state changes.

---

## Architectural Boundaries

This public document describes runtime responsibilities and verified behavior rather than the complete internal implementation.

The public architecture currently emphasizes:

- Application lifecycle.
- Runtime binding.
- UI composition.
- Workspace navigation.
- Canonical state synchronization.
- Runtime System Map integration.
- Defensive UI behavior.

Internal TouchDesigner networks, implementation source, development tooling, deployment configuration, and unreleased integrations remain outside the current public repository scope.

---

## Current Runtime Status

At this milestone, Production Core has progressed from a collection of architectural subsystems into an application capable of booting into an integrated operational interface.

The current runtime foundation provides the base for continued work on:

- Media server integration.
- OSC and external control surfaces.
- Device discovery.
- Video switcher integration.
- Production network monitoring.
- System Map interaction and visualization.
- Extensible production modules.

As these systems mature, this document will evolve alongside the runtime architecture.
