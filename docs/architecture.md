# Architecture Explanation

## Scope

This submission models a care-home alert triage dashboard using local mock data inside Home Assistant. It focuses on a tight operational workflow:

```text
select alert -> acknowledge -> escalate -> resolve
```

The dashboard is intentionally scoped to six representative alerts and four staff recipients. The goal is to demonstrate state modelling, clear Lovelace composition, and safe operator interaction rather than a full production care platform.

## State Model

Each alert has a stable mock identifier:

- `alert_001`
- `alert_002`
- `alert_003`
- `alert_004`
- `alert_005`
- `alert_006`

Mutable state is stored in Home Assistant helpers:

- `input_select.care_selected_alert` stores the currently selected alert ID.
- `input_select.care_alert_NNN_status` stores each alert lifecycle state.
- `input_text.care_alert_NNN_escalated_to` records the staff recipient selected during escalation.
- `input_datetime.care_alert_NNN_timestamp` stores the alert timestamp used for relative-time display.

Static mock metadata, such as resident name, room number, alert type, and priority, is exposed through template sensors. This separates stable demo data from mutable workflow state.

Derived counters, including active-alert count and critical-alert count, are calculated from alert sensor state and attributes rather than hardcoded status helper IDs. This keeps the dashboard extensible if additional mock alerts are added.

The mock alert types mirror the brief's scenario categories:

- Fall Detection
- Call Button
- Environmental Threshold
- Wandering Alert

## Lifecycle

The primary lifecycle is:

```text
NEW -> ACKNOWLEDGED -> ESCALATED -> RESOLVED
```

Scripts enforce this flow for acknowledge and escalate actions:

- An alert can only be acknowledged from `NEW`.
- An alert can only be escalated from `NEW` or `ACKNOWLEDGED`, and only when a staff recipient is selected.
- A resolved alert is removed from the active list.

The resolve script allows resolving any selected active alert. This is a deliberate shortcut for false-positive handling, where a staff member may determine that a newly raised event does not require escalation. In production this would usually require a reason code and audit entry.

## Dashboard Composition

The Lovelace dashboard is split into two sections:

1. Operational alert list and action bar.
2. Selected-alert detail.

The alert list uses `button-card` for selectable cards because it gives deterministic control over selected styling, active/resolved visibility, and tap behavior. Summary and modal content use Mushroom cards for readable, restrained display.

The action bar uses the brief's intended color semantics:

- Acknowledge: blue
- Escalate: orange
- Resolve: green

Each alert card shows a relative timestamp for glanceability and an absolute timestamp for auditability.

The action bar remains beneath the alert list in the Lovelace structure, as required by the brief. It is also styled as a sticky control surface so the three actions remain reachable on mobile after scrolling through a longer alert list.

## Escalation Flow

The escalation action opens a `browser_mod` popup listing available staff members. Selecting a staff member opens a confirmation popup. The final confirmation uses a frontend `browser_mod.sequence` to call the escalation script and then close the popup from the active browser context. This is more reliable on mobile than attempting to close the popup from a backend script.

The escalation script records the recipient in the selected alert's escalation field and moves the alert to `ESCALATED`.

Cancelling the modal does not call the escalation script, so no state changes occur.

## Disabled State Handling

Action buttons calculate disabled state from:

- whether `input_select.care_selected_alert` is `none`
- the selected alert status

Disabled buttons are styled with reduced opacity and suppressed pointer events, matching the assessment requirement that taps/clicks should not trigger navigation or feedback when no action is valid.

## Production Considerations

In a production system, the mock helpers would be replaced by event entities sourced from a real alerting backend. Additional production requirements would include:

- immutable audit logging
- reason codes for direct resolution
- multi-user conflict handling
- alert assignment and reassignment history
- stronger stale-state detection
- backend validation of lifecycle transitions
- integration tests for concurrent state updates

The current implementation keeps those concerns visible in the design while staying within the assessment's expected 8-12 hour scope.
