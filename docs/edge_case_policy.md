## Edge-case policy (v1)
### Purpose
This document defines short, testable rules for handling non-standard situations in the K-pop boy groups’ self-production project. Each rule includes: **definition**, **examples**, **counter-examples**, and a **unit test idea**.

### Category taxonomy
**Definition:** Tracks are partitioned into **not self-produced** (0 roles), **partially self-produced** (1–2 roles), and **fully self-produced** (3 roles). **Self-produced (any role)** is the aggregate of **partial + full**. The **overall distribution** is the full partition (**not / partial / full**) and sums to 100%.
**Examples:** A track with member in lyrics only → partially self-produced. A track with members in all three roles → fully self-produced.
**Counter-examples:** A track with a member as vocal director only (not one of the three roles) → not self-produced.
**Unit test idea:** Given three tracks labeled (0, 1, 3) roles, confirm category counts = {not:1, partial:1, full:1} and any-role = 2.

### Membership timing
**Definition:** Count a credit only if the person was a **group member on the release date** (current-only metric). A **historical-inclusive** variant counts credits **up to a member’s departure** date.
**Examples:** A member who left after the release date still counts. A pre-debut trainee credited before official joining does not count.
**Counter-examples:** Counting a former member on tracks released **after** departure.
**Unit test idea:** For a member with tenure 2019-01-01 → 2022-12-31, a track on 2023-02-01 should not count in current-only.

### Versions and duplicates
**Definition:** Consolidate KR/JP/CN/EN language versions to a single **base song id** for rate denominators. **Remixes** count as a new base song only if new **arrangement/producer** credits are present. **Instrumentals** are ineligible unless credits materially differ.
**Examples:** “Title (Korean)” and “Title (Japanese)” → one base song. A remix with a new external producer → new base song.
**Counter-examples:** Counting both KR and JP versions separately in rate denominators.
**Unit test idea:** Given two language versions with identical credits, confirm one base song id is assigned and denominator = 1.

### Subunits
**Definition:** Exclude all subunits **except NCT’s official subunits**; keep NCT parent and each subunit separate.
**Examples:** NCT 127 is included; a random non-NCT subunit is excluded.
**Counter-examples:** Merging NCT subunits back into the parent entity for counting.
**Unit test idea:** Parent NCT and NCT 127 maintain separate tallies without cross-contamination.

### Agencies and labels
**Definition:** Record **imprint** and **parent** at the **release date**; treat mergers **prospectively** (do not rewrite the past).
**Examples:** Big Hit Music under HYBE for 2021 releases; Epic Records Japan under Sony Music Japan.
**Counter-examples:** Retroactively labeling pre-merger releases under the new parent.
**Unit test idea:** A 2016 release remains under the 2016 parent when queried in 2025.

### Featuring and collaborations
**Definition:** Credits from **featuring artists** who are members of other groups do **not** count toward the host group’s self-production.
**Examples:** A featured rapper from another group does not contribute to the host group’s member-role flags.
**Counter-examples:** Counting a feature as if the artist were a member.
**Unit test idea:** Track with a feature from a non-member should not flip any member-role booleans to True.

### Eligible roles only
**Definition:** Only Wikipedia’s three official categories are eligible: **lyrics**, **composition**, **arrangement/producer**.
**Examples:** Counting “arrangement” as eligible.
**Counter-examples:** Counting “vocal director”, “mix engineer”, or “recording engineer” as eligible.
**Unit test idea:** Validate that the set of roles in credits ⊆ {lyrics, composition, arrangement_producer}.

### Name disambiguation and aliases
**Definition:** Use an **aliases** table to unify stage names, romanizations, and producer tags; store **credited_as** for the raw string.
**Examples:** Matching a member’s producer alias to their member_id.
**Counter-examples:** Treating two aliases as different people.
**Unit test idea:** Alias “Producer X” maps to member_id M123 and flips the correct member-role flags.

### Conflicting sources
**Definition:** Prefer **Wikipedia** for mapping to the three roles; attach **source_url** and flag disputes when Spotify/Genius differ.
**Examples:** If Genius lists “producer” but Wikipedia does not, follow Wikipedia for role eligibility; still store both URLs.
**Counter-examples:** Silently overwriting without a dispute flag.
**Unit test idea:** When sources disagree, ensure `dispute_flag = True` and both URLs are logged.

### Release eligibility
**Definition:** Include official discography tracks (albums/EPs/singles/OSTs) released under the group. Exclude solo/mixtape items unless released as group tracks.
**Examples:** Official OST credited to the group is included; member solo mixtape is excluded.
**Counter-examples:** Including survival show performances not in the official discography.
**Unit test idea:** OSTs under the group’s discography pass; solo items fail eligibility.

### Discography size threshold
**Definition:** For **rate** leaderboards, require **≥15 unique original base songs** (post-dedup). Publish a secondary table for **8–14** with a warning.
**Examples:** A group with 12 originals appears in the secondary table only.
**Counter-examples:** Ranking a 5-track group by rate alongside major catalogs.
**Unit test idea:** Deny inclusion in the primary rate leaderboard when unique originals < 15.

### Data gaps and unknowns
**Definition:** If any of the three roles are **unknown** for a track, exclude that track from **rate** denominators (to avoid bias); keep it visible in coverage stats.
**Examples:** Missing arrangement credit → the track is excluded from rate computation but listed in a “coverage” table.
**Counter-examples:** Treating unknown as not self-produced.
**Unit test idea:** A group with 20 tracks, 2 unknown-role tracks should have denominator = 18 for rates.

### Change control
**Definition:** All policy updates occur via **PR** with a versioned change log. No silent edits.
**Unit test idea:** Attempting to change a rule without updating version triggers a CI failure.