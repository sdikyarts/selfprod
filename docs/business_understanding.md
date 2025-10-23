# K-pop boy groups’ self-production: measurement and rankings

## Problem statement
We aim to measure self-production at the track level for K-pop boy groups by detecting member participation in **lyrics**, **composition**, and **arrangement/producer** roles. A track is **self-contributed** for a role if at least one member is officially credited in that role. Tracks are partitioned into three **mutually exclusive** categories:
- **Not self-produced:** No member is credited in any of the three roles (0 roles).
- **Partially self-produced:** Members are credited in **exactly one or two** of the three roles (1–2 roles).
- **Fully self-produced:** Members are credited in **all three** roles (3 roles).

### Overall distribution vs. self-produced (any role)
- **Overall distribution:** The **full** category breakdown across **not / partial / full**. This is what “overall” refers to in this project (i.e., **both combined plus the ones that are not**).
- **Self-produced (any role):** An aggregate convenience view equal to **partial + full** (tracks with ≥1 member role).

## Objectives and KPIs
- Rank groups by **counts** and **rates** for each category in the **overall distribution**: **not**, **partially**, and **fully** self-produced.
- Provide a compact **self-produced (any role)** view (counts and rates), where **any = partial + full**.
- Publish **role-specific leaderboards** (lyrics, composition, arrangement/producer).
- Provide secondary cuts by **agency** (parent and imprint), **market** (KR/JP), and **era**.

## Success criteria
- Reproducible tables with clear definitions, thresholds, and a complete **overall distribution** per group.
- Transparent per-row sources (Wikipedia/Spotify/Genius links).
- A documented edge-case policy, frozen at v1 and linked here.

## Scope and rules (v1)
- **Groups:** K-pop boy groups only.
- **Subunits:** Exclude all subunits **except NCT’s official subunits**; keep NCT parent and each subunit separate.
- **Discography size:** For **rate** leaderboards, require **≥15 unique original tracks** (post-dedup). Publish a secondary table for **8–14** with a warning.
- **Versions:** Consolidate language versions into a **base song** for rate calculations; count remixes only if credits materially change; instrumentals ineligible unless credits differ.
- **Membership timing:** Use **release-date membership**. Publish two metrics: **current-only** and **historical-inclusive** (credits count up to departure).
- **Agencies:** Record **imprint** and **parent** at release date; aggregate at both levels.
- **Eligible roles:** Only Wikipedia’s three roles: **lyrics**, **composition**, **arrangement/producer**.

## Assumptions and constraints
- Credits come from official sources and may conflict; **Wikipedia takes precedence** for the three roles.
- Some releases lack full credit lines; mark **unknown** and **exclude from denominators** where it would bias rates.
- Manual collection is necessary; log **source URLs per credit**.

## Stakeholders and responsibilities
- **Owner:** [name].
- **Reviewer:** [name].
- **Consumers:** [names or “public readers”].
- **Sign-off cadence:** Freeze **v1** now; next review after **pilot EDA**.

## Deliverables
- Clean datasets (CSV/Parquet or SQLite), Colab notebook, and **ranked tables** that include:
  - **Overall distribution** per group: counts and rates for **not / partial / full** (summing to 100%).
  - **Self-produced (any role)** counts and rates (**partial + full**).
  - **Role-wise** counts and rates.
  - Appendices documenting rules and decisions.

## Risks and mitigations
- **Small-N volatility:** Threshold + optional **Wilson score** for rate ordering.
- **Ambiguous names:** **Alias table** and manual confirmations.
- **Policy drift:** Changes only via **PR** with a versioned change log.

## Timeline (high-level)
- **Week 0:** Finalize v1 business understanding + edge-case policy; set up repo structure.
- **Week 1:** Assemble schema, import groups and members, pilot 2–3 groups of credits; run EDA.
- **Week 2:** Full labeling and first leaderboards; iterate on anomalies.

## Edge-case policy: where it lives and how to write it
### Policy file
Create `docs/edge_case_policy.md` in the repository. This file is the versioned source of truth for all edge-case rules.

### Core rules
- **Membership timing:** Credits count only if the person is a member on release date (**current-only**). For **historical-inclusive**, count up to departure.
- **Versions:** Consolidate KR/JP/CN/EN versions under one **base song id** for **rate**; remixes count only with **new arrangement/producer** credits; instrumentals **ineligible** unless credits differ.
- **Subunits:** Only **NCT’s** official subunits are in scope; others excluded by default.
- **Agencies:** Store **imprint** and **parent** at release date; handle mergers **prospectively** (do not rewrite history).
- **Featuring artists from other groups:** Do **not** attribute to the host group.
- **Ambiguous roles:** Only **lyrics**, **composition**, **arrangement/producer**.
- **Data conflicts:** Prefer **Wikipedia** for role mapping; attach source URLs; flag disputes.
- **Category taxonomy:** Explicitly define **not self-produced** (0 roles), **partially self-produced** (1–2 roles), **fully self-produced** (3 roles), and the **self-produced (any role)** aggregate (**partial + full**). Confirm that the **overall distribution** is the full partition (**not / partial / full**) and thus sums to 100%.

### Rule format
Each rule should include:
- **Definition** (short, testable sentence).
- **Examples** (one positive, one negative).
- **Counter-examples** (ambiguous or near-miss cases).
- **Unit test idea** (for example, “A track with KR and JP versions yields one base song in rate denominators”).