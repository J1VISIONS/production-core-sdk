# Changelog

All notable public development milestones for Production Core SDK will be documented in this file.

Production Core is currently under active development. Until a formal public versioning strategy is established, current work is recorded under **Unreleased**.

## [Unreleased]

### Added

- Autonomous Production Core application cold start.
- Application runtime boot state through `AppExtension`, verified as Ready with `booted=True` and `mode=dev`.
- MAIN_UI runtime bootstrap through `MainUIRuntimeBinder`, verified with `bootstrapped=True`.
- Automatic MAIN_UI population on application launch.
- Integrated header, footer, sidebar, and workspace runtime systems.
- Operational workspace page architecture spanning ten primary pages:
  - Dashboard
  - System Map
  - PTZ
  - Media
  - Modules
  - Devices
  - Network
  - OSC
  - Logs
  - Settings
- Active-page visual highlighting in sidebar navigation.
- Canonical navigation/state synchronization.
- System Map runtime integration inside MAIN_UI.
- Runtime System Map node rendering inside the application workspace.
- Runtime data binding for System Map labels.
- Navigation support between System Map and other workspace pages.
- Defensive handling for invalid sidebar rows.

### Changed

- Production Core now transitions from project open into an operational application runtime without requiring manual MAIN_UI population.
- Sidebar navigation now uses a direct mapping between rendered List rows and canonical page IDs.
- Public project documentation now distinguishes operational capabilities from active and upcoming development work.

### Fixed

#### Sidebar row-coordinate mismatch — Sprint 16.3A

Corrected an off-by-one navigation defect caused by a coordinate-space mismatch between canonical navigation source data and rendered List data.

The canonical `navigation_items` table contains a header row. `select1` removes that header before the data reaches `in1` and the List COMP. As a result, visible List rows are already zero-based.

The previous sidebar callback subtracted the original source-table header offset from the already transformed visible row. This caused page selections to resolve one page early—for example, selecting PTZ could resolve to System Map, while selecting Media could resolve to PTZ.

The mapping contract was corrected so visible List rows now map 1:1 to page IDs.

### Verified

The corrected navigation contract was verified with the following checks:

```python
cb._ListRowToVisibleRow(..., 2) == 2
sb.GetPageIdForVisibleRow(2) == 'ptz'
cb._ListRowToVisibleRow(..., -1) is None
```

All checks passed.

Operator testing was then completed across all ten sidebar pages, confirming:

- Correct page selection.
- Correct active-page highlighting.
- Correct System Map navigation behavior.
- Stable canonical navigation/state synchronization.
- Defensive invalid-row behavior.

### Engineering Notes

Sprint 16.3A reinforced a core UI architecture principle for Production Core:

> Canonical source-data coordinates and rendered UI coordinates must have an explicit, testable mapping contract.

The defect was not a failure of page state itself. It resulted from applying a source-data offset after the header had already been removed by the UI data pipeline. Treating each transformation stage as its own coordinate space made the root cause explicit and produced a simpler mapping contract.

---

## Earlier Public Repository Milestones

Before the current runtime milestone update, the public repository established:

- Initial Production Core SDK project overview.
- Documentation asset structure.
- Initial Production Core UI and system architecture visuals.
- README integration for project visuals.

Detailed historical engineering records from earlier internal development stages may be incorporated into future documentation updates.
