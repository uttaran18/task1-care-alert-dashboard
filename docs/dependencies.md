# Dependencies

The dashboard uses standard Home Assistant helpers plus four HACS frontend components.

| Component | Purpose | Install Method |
| --- | --- | --- |
| Mushroom Cards | Header, summary, and modal content cards | HACS frontend repository |
| Button Card | Selectable alert cards and action buttons | HACS frontend repository |
| Card Mod | Focused card styling and dark-mode refinements | HACS frontend repository |
| Browser Mod | Escalation popup/modal handling | HACS integration |

Recommended pinned versions should be filled in after installation in the target Home Assistant environment:

| Component | Version Tested |
| --- | --- |
| Home Assistant | v2026.5.4 |
| HACS | v2.0.5 |
| Mushroom Cards | v5.1.1 |
| Button Card | v7.0.1 |
| Card Mod | v4.2.1 |
| Browser Mod | v2.13.5 |

The implementation intentionally avoids a bespoke JavaScript custom card. This keeps the submission reproducible from common Home Assistant dashboard tooling and reduces custom maintenance surface.

