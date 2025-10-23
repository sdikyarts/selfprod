## Data dictionary (v1)
### Conventions
- Primary keys are marked **(pk)**; foreign keys are marked **(fk)**.
- All dates are ISO-8601 (`YYYY-MM-DD`). Booleans are `true/false`.
- Roles vocabulary is restricted to: `lyrics`, `composition`, `arrangement_producer`.

### Table: groups
| Field | Type | Notes |
|---|---|---|
| group_id (pk) | string | Stable identifier |
| wikipedia_name | string | Original name from Wikipedia |
| canon_name | string | Canonical display name |
| country | string | Country/region |
| debut_date | date | Official debut |
| disband_date | date? | Null if active |
| is_active_flag | boolean | Derived from dates |

### Table: members
| Field | Type | Notes |
|---|---|---|
| member_id (pk) | string | Stable identifier |
| stage_name | string | Canonical stage name |
| legal_name | string? | Optional |
| aliases | string/array | Comma-separated or JSON array |
| birth_date | date? | Optional |

### Table: group_membership
| Field | Type | Notes |
|---|---|---|
| group_id (fk→groups.group_id) | string | |
| member_id (fk→members.member_id) | string | |
| start_date | date | Join date |
| end_date | date? | Departure date |
| is_current_flag | boolean | Derived: end_date null or in future |

### Table: agencies
| Field | Type | Notes |
|---|---|---|
| agency_id (pk) | string | |
| name | string | Imprint or label name |
| parent_company | string? | Parent company (e.g., HYBE) |
| country | string | |

### Table: group_agency_tenure
| Field | Type | Notes |
|---|---|---|
| group_id (fk) | string | |
| agency_id (fk) | string | |
| start_date | date | |
| end_date | date? | |
| market_region | string | e.g., KR, JP |

### Table: releases
| Field | Type | Notes |
|---|---|---|
| release_id (pk) | string | |
| group_id (fk) | string | |
| title | string | |
| release_type | enum | album, ep, single, ost, other |
| release_date | date | |
| market | enum | KR, JP, CN, GLOBAL |
| catalog_ids | string? | e.g., UPCs |

### Table: tracks
| Field | Type | Notes |
|---|---|---|
| track_id (pk) | string | |
| release_id (fk) | string | |
| base_song_id | string | Shared id across language versions |
| title | string | |
| is_title_track | boolean | |
| version_type | enum | original, lang_ver, remix, instrumental, rerecord |
| language | enum | ko, ja, zh, en, other |
| duration | integer? | Seconds |

### Table: credits
| Field | Type | Notes |
|---|---|---|
| credit_id (pk) | string | |
| track_id (fk) | string | |
| person_name | string | As credited |
| role | enum | lyrics, composition, arrangement_producer |
| credited_as | string | Raw credit string |
| source_url | string | Link to source |
| source_name | enum | Wikipedia, Spotify, Genius, Other |
| is_group_member_flag | boolean | True if person is a member of the group |
| member_id (fk) | string? | If matched to members |

### Table: subunits
| Field | Type | Notes |
|---|---|---|
| subunit_id (pk) | string | |
| parent_group_id (fk) | string | |
| name | string | |
| include_flag | boolean | True only for NCT subunits |

### Table: aliases
| Field | Type | Notes |
|---|---|---|
| entity_type | enum | group, member, agency |
| entity_id | string | |
| alias | string | |
| locale | string? | e.g., ko, ja |

### Table: track_labels (derived)
| Field | Type | Notes |
|---|---|---|
| track_id (fk) | string | |
| has_member_lyrics | boolean | |
| has_member_composition | boolean | |
| has_member_arrprod | boolean | |
| member_role_count | integer | 0..3 |
| is_partially_self_produced | boolean | True if count in {1,2} |
| is_fully_self_produced | boolean | True if count == 3 |
| is_self_produced_any | boolean | True if count >= 1 |

### Table: group_aggregates (derived)
| Field | Type | Notes |
|---|---|---|
| group_id (fk) | string | |
| scope_variant | enum | all_time, current |
| count_tracks_total | integer | Denominator (unique base songs) |
| count_not | integer | |
| count_partially | integer | |
| count_fully | integer | |
| count_any | integer | partial + full |
| rate_not | float | count_not / total |
| rate_partially | float | count_partially / total |
| rate_fully | float | count_fully / total |
| rate_any | float | count_any / total |
| coverage_unknown_share | float | Share of tracks with unknown roles |

### Constraints and validation
- Role values must be in the **eligible set** only.
- `base_song_id` groups language versions; ensure one id per song concept.
- Denominators for rates exclude **unknown-role** tracks per policy.
- Foreign keys must resolve; composite uniqueness enforced where appropriate.