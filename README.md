<<<<<<< HEAD
# Care Alert Dashboard - Task 1

This folder contains a complete local Home Assistant dashboard package for Task 1 of the technical assessment.

Repository: https://github.com/uttaran18/task1-care-alert-dashboard

The dashboard models a care-home alert triage workflow using mock local data:

```text
NEW -> ACKNOWLEDGED -> ESCALATED -> RESOLVED
```

The `care_resolve_selected_alert` script also allows resolving any selected active alert. This is intentional for false-positive handling and is documented in `docs/architecture.md`.

## Contents

```text
configuration.yaml
dashboards/care-alert-dashboard.yaml
docs/architecture.md
docs/dependencies.md
packages/care_alerts.yaml
themes/care_command_dark.yaml
screenshots/          — Dashboard state captures (see submission)
```

## Prerequisites

- Home Assistant with YAML configuration access.
- HACS installed.
- HACS frontend components listed in `docs/dependencies.md`.
- A modern browser or tablet viewport for dashboard review.

Tested versions should be recorded in `docs/dependencies.md` after installation.

## Installation

From a clean Home Assistant configuration directory:

1. Copy `configuration.yaml` into the Home Assistant config directory, or merge its `homeassistant`, `frontend`, and `lovelace` sections into an existing configuration.
2. Copy these folders into the same config directory:
   - `packages`
   - `dashboards`
   - `themes`
3. Install the HACS dependencies listed in `docs/dependencies.md`.
4. Restart Home Assistant.
5. Select the `care_command_dark` theme from the user profile theme selector.
6. Open the `Care Alerts` dashboard from the sidebar.
7. Run `script.care_reset_mock_alerts` once to seed demo statuses and relative timestamps.

## Expected Workflow

1. Open the `Care Alerts` dashboard.
2. Select one alert card.
3. Tap `Acknowledge` to move a `NEW` alert to `ACKNOWLEDGED`.
4. Tap `Escalate`, choose a staff member, and confirm. The confirmation closes the popup after recording the escalation.
5. Tap `Resolve` to remove the alert from the active list.

When no alert is selected, the action buttons are visually disabled and suppress pointer interaction.

## Entities Created

Core selection helpers:

- `input_select.care_selected_alert`
- `input_select.care_escalation_recipient`

Alert status helpers:

- `input_select.care_alert_001_status`
- `input_select.care_alert_002_status`
- `input_select.care_alert_003_status`
- `input_select.care_alert_004_status`
- `input_select.care_alert_005_status`
- `input_select.care_alert_006_status`

Derived sensors:

- `sensor.care_active_alert_count`
- `sensor.care_critical_alert_count`
- `sensor.care_selected_alert_status`
- `sensor.care_alert_001` through `sensor.care_alert_006`

Lifecycle scripts:

- `script.care_select_alert`
- `script.care_clear_selected_alert`
- `script.care_acknowledge_selected_alert`
- `script.care_escalate_selected_alert`
- `script.care_resolve_selected_alert`
- `script.care_reset_mock_alerts`

The reset script is intentionally not shown as a staff-facing dashboard control. It exists for demo setup and screenshot capture.

## How To Use In Home Assistant

The YAML dashboard is registered under:

```yaml
lovelace:
  dashboards:
    care-alerts:
      mode: yaml
      title: Care Alerts
      icon: mdi:shield-alert
      show_in_sidebar: true
      filename: dashboards/care-alert-dashboard.yaml
```

## Assumptions

- Alert data is mocked locally because no company system or production data is available.
- Six alerts are enough to demonstrate priority, status, selection, escalation, and resolution behavior.
- Direct resolution is allowed for false positives; production deployments should require a reason code and audit event.
- Browser Mod is used for the escalation modal because it is a common Home Assistant approach for dashboard popups.
- The action bar is sticky on small screens so mobile users do not need to scroll to the bottom of a long alert list before acting.

## AI Disclosure

AI assistance was used to draft and structure this assessment implementation.
