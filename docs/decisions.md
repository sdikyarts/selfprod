## Decision log
### How to use this file
Record each policy or design decision with the **date**, **decision**, **rationale**, **impact**, and **version**. New entries append to the top.

### Entries
| Date (YYYY-MM-DD) | Decision | Rationale | Impact | Version |
|---|---|---|---|---|
| 2025-10-23 | Define overall distribution = not + partial + full; “self-produced (any role)” = partial + full. | Clarify taxonomy and reporting. | Standardizes leaderboards and denominators. | v1.0 |
| 2025-10-23 | Apply ≥15 unique original base songs threshold for rate leaderboards; secondary table for 8–14. | Stabilize small-N volatility. | Fairer comparisons across catalogs. | v1.0 |
| 2025-10-23 | NCT exception: include NCT subunits; exclude other subunits by default. | Reflect ecosystem reality of NCT units. | Clean parent vs unit comparisons. | v1.0 |
| 2025-10-23 | Consolidate language versions under one base_song_id; count remixes only if new arrangement/producer credits. | Prevent double counting; reflect production changes. | Consistent rate denominators. | v1.0 |
| 2025-10-23 | Membership timing at release date; provide current-only and historical-inclusive metrics. | Avoid post-hoc attribution; allow historical view. | Clear dual-metric reporting. | v1.0 |

### Pending proposals
- Add Wilson score ordering for rates.  
- Introduce confidence bands on rates where denominators are borderline (15–20).  
- Expand agency mapping to additional markets beyond KR/JP (CN, US).

### Version history
- **v1.0 (2025-10-23):** Initial policy, taxonomy, thresholds, and scope recorded.