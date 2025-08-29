Here's the complete refactored README file with all the new endpoints added while preserving all existing documentation:

# TMS Hockey API Documentation - Complete Field Reference

**Base URL:** `https://api.tms.hockey`

**API Type:** PostgREST API - Supports advanced querying, filtering, and data selection

---

## PostgREST Query Capabilities

This API is built on PostgREST, which automatically provides powerful querying capabilities for all GET endpoints. You can use these operators to filter, sort, and shape your data requests.

### Basic Query Structure

```
GET /endpoint?parameter=operator.value
```

### Filtering Operators

| Operator | Description | Example |
| --- | --- | --- |
| `eq` | Equals | `?team_name=eq.Lightning Bolts` |
| `neq` | Not equals | `?status=neq.completed` |
| `gt` | Greater than | `?goals_scored=gt.5` |
| `gte` | Greater than or equal | `?standing_position=gte.1` |
| `lt` | Less than | `?age_group=lt.21` |
| `lte` | Less than or equal | `?matches_played=lte.10` |
| `like` | Pattern matching (case sensitive) | `?player_name=like.*Smith*` |
| `ilike` | Pattern matching (case insensitive) | `?team_name=ilike.*lightning*` |
| `in` | In list | `?status=in.(active,completed)` |
| `is` | Is (for null values) | `?penalty_points=is.null` |
| `not` | Negation | `?team_code=not.eq.TBD` |

### Sorting

```
?order=column_name.asc
?order=column_name.desc
?order=column1.asc,column2.desc
```

**Examples:**
- `?order=standing_position.asc` - Sort by position ascending
- `?order=goals_scored.desc,assists.desc` - Sort by goals (desc), then assists (desc)

### Field Selection

```
?select=field1,field2,field3
```

**Examples:**
- `?select=team_name,team_code,logo_url` - Only return specific fields
- `?select=*` - Return all fields (default)

### Limiting Results

```
?limit=number
?offset=number
```

**Examples:**
- `?limit=10` - Return first 10 results
- `?limit=10&offset=20` - Return results 21-30 (pagination)

### Combining Operators

```
?field1=eq.value&field2=gte.number&order=field3.desc&limit=5
```

**Example:**
```
/seasons/123/teams?roster_status=eq.confirmed&final_standing=lte.3&order=final_points.desc&limit=5
```
*Returns top 5 teams with confirmed rosters that finished in top 3 positions*

### Complex Filtering

#### OR Conditions
```
?or=(field1.eq.value1,field2.eq.value2)
```

#### AND Conditions  
```
?field1=eq.value1&field2=eq.value2
```

#### Nested Conditions
```
?and=(field1.gte.10,or(field2.eq.active,field2.eq.completed))
```

### Practical Examples

#### Get Teams by Status
```
GET /seasons/123/teams?season_team_status=eq.active
```

#### Search Players by Name
```
GET /seasons/123/teams/456/roster?person_name=ilike.*john*
```

#### Get Top Scorers
```
GET /seasons/123/playerstats?order=total_goals_scored.desc&limit=10
```

#### Filter by Multiple Criteria
```
GET /seasons/123/standings?standing_position=lte.5&standing_points=gte.20&order=standing_position.asc
```

#### Get Recent Matches
```
GET /fixtures/group/senior-men?fixture_date=gte.2024-01-01&order=fixture_date.desc
```

#### Complex Team Search
```
GET /entities?and=(age_group.eq.Senior,gender.eq.M,entity_status.eq.active)&order=entity_name_latin.asc
```

### Response Format

All PostgREST responses return JSON arrays:

```json
[
  {
    "field1": "value1",
    "field2": "value2"
  },
  {
    "field1": "value3",
    "field2": "value4"
  }
]
```

### Error Handling

PostgREST returns specific error messages for invalid queries:

```json
{
  "code": "PGRST116",
  "details": "Results contain 0 rows, expected 1 row",
  "hint": "The query may have failed to filter to exactly one row",
  "message": "JSON object requested, multiple (or no) rows returned"
}
```

### Performance Tips

1. **Use field selection** to reduce payload size: `?select=id,name,status`
2. **Add limits** for large datasets: `?limit=100`
3. **Index your filters** - commonly filtered fields should have database indexes
4. **Combine filters** efficiently rather than making multiple requests

### PostgREST Resources

- **Official Documentation**: [postgrest.org](https://postgrest.org)
- **API Reference**: [PostgREST API](https://postgrest.org/en/stable/api.html)
- **Query Examples**: [PostgREST Tutorials](https://postgrest.org/en/stable/tutorials/)

---

## Authentication

### Web Session Authentication

**Endpoints:**
- `POST /login` - Web application login
- `POST /login-livescores` - Livescores application login

**Response:** `{"ok": true}` - Sets secure session cookie (`session_id`) valid for 4 hours.

#### Livescores-Specific Login
`POST /login-livescores`

**Description:** Dedicated login endpoint for livescores.hockey domain
**Domain Restriction:** Only accepts requests from livescores.hockey origins
**Response:** Same as `/login` - sets secure session cookie with 4-hour validity

### Mobile App Authentication

**Endpoints:**
- `POST /auth/mobile` - Mobile app login
- `POST /auth/refresh` - Refresh access token  
- `POST /auth/logout` - Mobile logout

**Mobile Login Request:**
```json
{
  "clientId": "tms-hockey-mobile",
  "clientSecret": "your-client-secret"
}
```

**Mobile Response Fields:**
- `accessToken` (string) - Access token for API calls
- `refreshToken` (string) - Token for refreshing access
- `expiresAt` (number) - Expiration timestamp
- `tokenType` (string) - Always "Bearer"

**Token Storage:** Redis-based with automatic expiration
- Access Token: 24-hour TTL
- Refresh Token: 30-day TTL

### Direct JWT Authentication

**Header:** `Authorization: Bearer <jwt-token>`

---

## Seasons

### Get All Seasons
`GET /seasons`

**Database View:** `v_seasons_with_competition`

**Response Fields (36 total):**

| Field | Type | Nullable | Description |
| --- | --- | --- | --- |
| `id` | integer | YES | Internal sequence ID |
| `season_id` | uuid | YES | Unique season identifier |
| `added` | timestamp without time zone | YES | When season was added to system |
| `age_group` | character varying | YES | Age group category (Senior, U21, etc.) |
| `competition_id` | uuid | YES | Associated competition identifier |
| `competition_name` | character varying | YES | Full competition name |
| `discipline` | character varying | YES | Sport discipline (Hockey, etc.) |
| `duration_full` | integer | YES | Full match duration in minutes |
| `start_date` | date | YES | Season start date |
| `end_date` | date | YES | Season end date |
| `event_type` | character varying | YES | Type of event (league, tournament, etc.) |
| `gender` | character varying | YES | Gender category (M, F, Mixed) |
| `grade` | character varying | YES | Competition grade/level |
| `include_in_statistics` | boolean | YES | Whether season is included in statistics |
| `live_data_available` | boolean | YES | Whether live data is available |
| `live_video_available` | boolean | YES | Whether live video is available |
| `lock_standings` | boolean | YES | Whether standings are locked from changes |
| `name_latin` | character varying | YES | Season name in Latin script |
| `name_local` | character varying | YES | Season name in local script |
| `name_short_latin` | character varying | YES | Short season name (Latin script) |
| `name_short_local` | character varying | YES | Short season name (local script) |
| `organization_id` | character varying | YES | Organizing body identifier |
| `promotion_relegation_rules` | jsonb | YES | Promotion/relegation rules as JSON |
| `representation` | character varying | YES | Team representation type |
| `roster_configuration` | jsonb | YES | Roster configuration rules as JSON |
| `season_type` | character varying | YES | Type of season (regular, playoff, etc.) |
| `standard` | character varying | YES | Competition standard level |
| `standing_configuration_id` | uuid | YES | Configuration for standings calculation |
| `status` | character varying | YES | Current season status |
| `video_production` | character varying | YES | Level of video production |
| `year` | integer | YES | Calendar year of season |
| `links` | jsonb | YES | Related links as JSON |
| `meta` | jsonb | YES | Additional metadata as JSON |
| `image_url` | text | YES | Season image/logo URL |
| `total_matches` | bigint | YES | Total number of matches in season |
| `matches_completed` | bigint | YES | Number of completed matches |

### Get Teams in Season
`GET /seasons/{seasonId}/teams`

**Database View:** `v_season_teams_with_entities`

**Response Fields (19 total):**

| Field | Type | Nullable | Description |
| --- | --- | --- | --- |
| `season_id` | uuid | YES | Season identifier |
| `team_entity_id` | uuid | YES | Team entity identifier |
| `team_organization_id` | text | YES | Team's organization identifier |
| `team_external_id` | text | YES | External system team identifier |
| `conference_id` | uuid | YES | Conference identifier (if applicable) |
| `division_id` | uuid | YES | Division identifier (if applicable) |
| `roster_status` | text | YES | Status of team roster (confirmed, pending, etc.) |
| `season_team_status` | text | YES | Team's status in this season |
| `season_team_added` | timestamp with time zone | YES | When team was added to season |
| `season_team_updated` | timestamp with time zone | YES | Last update to team in season |
| `team_name_latin` | character varying | YES | Team name in Latin script |
| `team_name_local` | character varying | YES | Team name in local script |
| `team_code_latin` | character varying | YES | Team code in Latin script |
| `team_code_local` | character varying | YES | Team code in local script |
| `team_representing` | character varying | YES | What/where the team represents |
| `logo_url` | text | YES | Team logo URL |
| `final_standing` | integer | YES | Final season standing position |
| `final_points` | numeric | YES | Final points earned in season |
| `final_prize_money` | numeric | YES | Prize money earned |

### Get Team Roster
`GET /seasons/{seasonId}/teams/{teamId}/roster`

**Database View:** `v_season_team_roster`

**Response Fields (13 total):**

| Field | Type | Nullable | Description |
| --- | --- | --- | --- |
| `season_id` | uuid | YES | Season identifier |
| `team_entity_id` | uuid | YES | Team entity identifier |
| `person_id` | uuid | YES | Player person identifier |
| `bib` | text | YES | Player's bib/jersey number |
| `position` | text | YES | Player's playing position |
| `roster_status` | text | YES | Player's roster status (active, inactive, etc.) |
| `roster_added` | timestamp with time zone | YES | When player was added to roster |
| `roster_updated` | timestamp with time zone | YES | Last roster update timestamp |
| `person_name` | text | YES | Player's full name |
| `person_image_url` | text | YES | Player's profile image URL |
| `person_dob` | date | YES | Player's date of birth |
| `nationality` | text | YES | Player's nationality code |
| `organization_id` | text | YES | Organization identifier |

### Get Team Staff
`GET /seasons/{seasonId}/teams/{teamId}/staff`

**Database View:** `v_season_team_staff`

**Response Fields (14 total):**

| Field | Type | Nullable | Description |
| --- | --- | --- | --- |
| `season_id` | uuid | YES | Season identifier |
| `team_entity_id` | uuid | YES | Team entity identifier |
| `person_id` | uuid | YES | Staff person identifier |
| `role` | text | YES | Staff role/position title |
| `role_id` | uuid | YES | Role identifier |
| `date_from` | timestamp without time zone | YES | Role start date |
| `date_to` | timestamp without time zone | YES | Role end date |
| `role_status` | text | YES | Role status (active, inactive, etc.) |
| `role_added` | timestamp without time zone | YES | When role was added |
| `role_updated` | timestamp without time zone | YES | Last role update |
| `person_name` | text | YES | Staff member's full name |
| `person_image_url` | text | YES | Staff member's image URL |
| `person_dob` | date | YES | Staff member's date of birth |
| `organization_id` | text | YES | Organization identifier |

### Get Season Standings
`GET /seasons/{seasonId}/standings`

**Database View:** `v_season_standings_with_entities`

**Response Fields (36 total):**

| Field | Type | Nullable | Description |
| --- | --- | --- | --- |
| `standing_id` | uuid | YES | Standing record identifier |
| `season_id` | uuid | YES | Season identifier |
| `team_entity_id` | uuid | YES | Team entity identifier |
| `fixture_type` | text | YES | Type of fixtures included in standing |
| `pool_code` | text | YES | Pool/group code |
| `standing_position` | integer | YES | Current standing position |
| `played` | integer | YES | Number of games played |
| `wins` | integer | YES | Number of wins |
| `draws` | integer | YES | Number of draws |
| `losses` | integer | YES | Number of losses |
| `scored_for` | integer | YES | Total goals/points scored |
| `scored_against` | integer | YES | Total goals/points conceded |
| `points_difference` | integer | YES | Goal/point difference |
| `result_string` | text | YES | Recent results string (WWLDW, etc.) |
| `streak` | integer | YES | Current win/loss streak |
| `win_percentage` | numeric | YES | Win percentage |
| `byes` | integer | YES | Number of bye games |
| `bonus_standing_points` | integer | YES | Bonus points earned |
| `bye_standing_points` | integer | YES | Points awarded for byes |
| `penalty_standing_points` | integer | YES | Penalty points deducted |
| `standing_points` | integer | YES | Total standing points |
| `latest` | boolean | YES | Whether this is the latest standing |
| `live` | boolean | YES | Whether standing is live/current |
| `standing_added` | timestamp with time zone | YES | When standing was added |
| `standing_updated` | timestamp with time zone | YES | Last standing update |
| `team_name` | character varying | YES | Team name |
| `team_code` | character varying | YES | Team code |
| `logo_url` | text | YES | Team logo URL |
| `pool_name_latin` | text | YES | Pool name (Latin script) |
| `pool_name_local` | text | YES | Pool name (local script) |
| `pool_abbreviation_latin` | text | YES | Pool abbreviation (Latin script) |
| `pool_abbreviation_local` | text | YES | Pool abbreviation (local script) |
| `pool_stage_code` | text | YES | Pool stage code |
| `pool_order` | integer | YES | Pool display order |
| `pool_external_id` | text | YES | External pool identifier |
| `pool_organization_id` | text | YES | Pool organization identifier |

### Get Season Officials
`GET /seasons/{seasonId}/officials`

**Database View:** `v_season_officials`

**Response Fields (33 total):**

*Key fields include:*
- `season_id` (uuid) - Season identifier
- `person_id` (uuid) - Official's person identifier
- `role` (text) - Official role (Referee, Umpire, etc.)
- `role_id` (uuid) - Role identifier
- `date_from` (timestamp) - Role start date
- `date_to` (timestamp) - Role end date
- `role_status` (text) - Role status
- `person_name` (text) - Official's full name
- `person_image_url` (text) - Official's image URL
- `person_dob` (date) - Official's date of birth
- `nationality` (text) - Official's nationality
- `organization_id` (text) - Organization identifier

*Plus additional fields for contact information, certifications, and assignment details.*

---

## Players & Personnel

### Get Player Information
`GET /persons/{person_id}`

**Data Source:** RPC function `get_player_stats_by_person_id`

*Note: This endpoint uses a stored procedure. Response schema varies based on function implementation.*

### Get Player Aggregate Statistics
`GET /persons/{person_id}/aggregate-stats`

**Data Source:** RPC function `get_player_aggregate_stats`

**Description:** Returns total career statistics for a player across all seasons

**Query Parameters:**
- `p_person_id` (uuid) - Player's unique identifier

*Note: Response schema varies based on function implementation*

### Get Person Roles
`GET /person_roles/{person_id}`

**Database View:** `person_roles_combined`

**Response Fields (21 total):**

| Field | Type | Nullable | Description |
| --- | --- | --- | --- |
| `person_id` | uuid | YES | Person identifier |
| `season_id` | uuid | YES | Season identifier |
| `season_name` | character varying | YES | Season name |
| `season_start` | date | YES | Season start date |
| `season_end` | date | YES | Season end date |
| `season_role` | text | YES | Role in season (player, staff, official) |
| `season_role_subtype` | text | YES | Specific role subtype |
| `fixture_id` | uuid | YES | Fixture identifier (if applicable) |
| `fixture_name` | text | YES | Fixture name |
| `match_date` | timestamp without time zone | YES | Match date and time |
| `home_team` | text | YES | Home team name |
| `away_team` | text | YES | Away team name |
| `home_score` | text | YES | Home team score |
| `away_score` | text | YES | Away team score |
| `home_secondary_score` | text | YES | Home team secondary score |
| `away_secondary_score` | text | YES | Away team secondary score |
| `match_role` | text | YES | Role in specific match |
| `venue_name` | text | YES | Venue name |
| `source` | text | YES | Data source (roster, lineup, etc.) |
| `entity_id` | uuid | YES | Associated entity identifier |
| `organization_id` | text | YES | Organization identifier |

### Get Player Active Seasons
`GET /activeseasons/players/{person_id}`

**Database View:** `player_active_seasons`

**Response Fields (17 total):**

| Field | Type | Nullable | Description |
| --- | --- | --- | --- |
| `person_id` | uuid | YES | Player identifier |
| `season_id` | uuid | YES | Season identifier |
| `entity_id` | uuid | YES | Team entity identifier |
| `bib` | text | YES | Player's jersey number |
| `position` | text | YES | Player's position |
| `roster_status` | text | YES | Player's roster status |
| `team_name` | character varying | YES | Team name |
| `team_code` | character varying | YES | Team code |
| `season_name` | character varying | YES | Season name |
| `start_date` | date | YES | Season start date |
| `end_date` | date | YES | Season end date |
| `season_status` | character varying | YES | Season status |
| `organization_id` | character varying | YES | Organization identifier |
| `year` | integer | YES | Season year |
| `discipline` | character varying | YES | Sport discipline |
| `gender` | character varying | YES | Gender category |
| `age_group` | character varying | YES | Age group |

---

## Statistics

### Get Player Season Statistics
`GET /seasons/{seasonId}/playerstats`

**Database View:** `player_season_statistics_frontend`

**Response Fields (39 total):**

| Field | Type | Nullable | Description |
| --- | --- | --- | --- |
| `person_id` | uuid | YES | Player identifier |
| `season_id` | uuid | YES | Season identifier |
| `season_name` | character varying | YES | Season name |
| `season_start` | date | YES | Season start date |
| `season_end` | date | YES | Season end date |
| `team_entity_id` | uuid | YES | Team entity identifier |
| `team_code` | character varying | YES | Team code |
| `team_name` | character varying | YES | Team name |
| `team_logo_url` | text | YES | Team logo URL |
| `organization_id` | character varying | YES | Organization identifier |
| `season_standing` | integer | YES | Team's final season standing |
| `matches_played` | bigint | YES | Total matches played |
| `total_challenges_scored` | bigint | YES | Total challenges scored |
| `total_field_goals_scored` | bigint | YES | Total field goals scored |
| `total_goals_scored` | bigint | YES | Total goals scored |
| `total_penalty_corners_scored` | bigint | YES | Total penalty corners scored |
| `total_penalty_strokes_scored` | bigint | YES | Total penalty strokes scored |
| `total_green_cards` | bigint | YES | Total green cards received |
| `total_yellow_cards` | bigint | YES | Total yellow cards received |
| `total_red_cards` | bigint | YES | Total red cards received |
| `wins` | bigint | YES | Team wins (when player participated) |
| `losses` | bigint | YES | Team losses (when player participated) |
| `draws` | bigint | YES | Team draws (when player participated) |
| `shootout_wins` | bigint | YES | Shootout wins |
| `shootout_losses` | bigint | YES | Shootout losses |
| `avg_challenges_scored` | numeric | YES | Average challenges scored per game |
| `avg_field_goals_scored` | numeric | YES | Average field goals scored per game |
| `avg_goals_scored` | numeric | YES | Average goals scored per game |
| `avg_penalty_corners_scored` | numeric | YES | Average penalty corners scored per game |
| `avg_penalty_strokes_scored` | numeric | YES | Average penalty strokes scored per game |
| `avg_green_cards` | numeric | YES | Average green cards per game |
| `avg_yellow_cards` | numeric | YES | Average yellow cards per game |
| `avg_red_cards` | numeric | YES | Average red cards per game |
| `total_goals_against` | bigint | YES | Total goals conceded (team total) |
| `avg_goals_against` | numeric | YES | Average goals conceded per game |
| `first_match_date` | timestamp without time zone | YES | Date of first match |
| `last_match_date` | timestamp without time zone | YES | Date of last match |
| `player_name` | text | YES | Player's full name |
| `player_image` | text | YES | Player's image URL |

### Get Entity Season Statistics
`GET /seasons/{seasonId}/entitystats`

**Database View:** `entity_season_statistics_frontend`

**Response Fields (61 total):**

| Field | Type | Nullable | Description |
| --- | --- | --- | --- |
| `entity_id` | uuid | YES | Entity identifier |
| `season_id` | uuid | YES | Season identifier |
| `season_name_latin` | character varying | YES | Season name (Latin script) |
| `season_name_local` | character varying | YES | Season name (local script) |
| `season_name` | character varying | YES | Season name (primary) |
| `season_start` | date | YES | Season start date |
| `season_end` | date | YES | Season end date |
| `team_code_latin` | character varying | YES | Team code (Latin script) |
| `team_code_local` | character varying | YES | Team code (local script) |
| `team_code` | character varying | YES | Team code (primary) |
| `team_name_latin` | character varying | YES | Team name (Latin script) |
| `team_name_local` | character varying | YES | Team name (local script) |
| `team_name` | character varying | YES | Team name (primary) |
| `team_logo_url` | text | YES | Team logo URL |
| `organization_id` | text | YES | Organization identifier |
| `matches_played` | bigint | YES | Total matches played |
| `total_goals_scored` | numeric | YES | Total goals scored |
| `total_goals_conceded` | numeric | YES | Total goals conceded |
| `total_field_goals_scored` | numeric | YES | Total field goals scored |
| `total_penalty_corners_scored` | numeric | YES | Total penalty corners scored |
| `total_penalty_strokes_scored` | numeric | YES | Total penalty strokes scored |
| `total_challenges_scored` | numeric | YES | Total challenges scored |
| `total_goals_shootout` | numeric | YES | Total shootout goals scored |
| `total_goals_conceded_shootout` | numeric | YES | Total shootout goals conceded |
| `total_penalty_strokes_shootout_scored` | numeric | YES | Shootout penalty strokes scored |
| `total_penalty_strokes_shootout_missed` | numeric | YES | Shootout penalty strokes missed |
| `total_saves` | numeric | YES | Total saves made |
| `total_saves_shootout` | numeric | YES | Total shootout saves |
| `total_blocks` | numeric | YES | Total blocks made |
| `total_tackles` | numeric | YES | Total tackles made |
| `total_interceptions` | numeric | YES | Total interceptions |
| `total_shots` | numeric | YES | Total shots taken |
| `total_shots_on_target` | numeric | YES | Total shots on target |
| `total_assists` | numeric | YES | Total assists |
| `total_passes` | numeric | YES | Total passes made |
| `total_bad_passes` | numeric | YES | Total bad passes |
| `avg_possession` | numeric | YES | Average possession percentage |
| `avg_shot_accuracy` | numeric | YES | Average shot accuracy percentage |
| `total_green_cards` | numeric | YES | Total green cards |
| `total_yellow_cards` | numeric | YES | Total yellow cards |
| `total_red_cards` | numeric | YES | Total red cards |
| `total_penalty_corners_earned` | numeric | YES | Total penalty corners earned |
| `total_penalty_strokes_earned` | numeric | YES | Total penalty strokes earned |
| `total_circle_penetrations` | numeric | YES | Total circle penetrations |
| `total_long_corners_earned` | numeric | YES | Total long corners earned |
| `total_challenges_earned` | numeric | YES | Total challenges earned |
| `avg_goals_scored` | numeric | YES | Average goals scored per game |
| `avg_goals_conceded` | numeric | YES | Average goals conceded per game |
| `avg_shots` | numeric | YES | Average shots per game |
| `avg_shots_on_target` | numeric | YES | Average shots on target per game |
| `avg_saves` | numeric | YES | Average saves per game |
| `wins` | bigint | YES | Total wins |
| `losses` | bigint | YES | Total losses |
| `draws` | bigint | YES | Total draws |
| `total_minutes_played` | interval | YES | Total playing time |
| `total_time_in_possession` | interval | YES | Total possession time |
| `avg_time_in_lead` | numeric | YES | Average time spent in lead |
| `max_biggest_lead` | integer | YES | Biggest lead achieved |
| `max_biggest_scoring_run` | integer | YES | Biggest scoring run |
| `first_match_date` | timestamp without time zone | YES | First match date |
| `last_match_date` | timestamp without time zone | YES | Last match date |

### Get Entity Fixture Statistics
`GET /seasons/{seasonId}/teams/{entityId}/stats`

**Database View:** `entity_fixture_statistics_frontend`

**Response Fields (72 total):**

*Key field categories:*

**Basic Information:**
- `entity_id` (uuid) - Entity identifier
- `fixture_id` (uuid) - Fixture identifier
- `period_id` (integer) - Period identifier
- `team_code` (character varying) - Team code
- `team_name` (character varying) - Team name
- `team_logo_url` (text) - Team logo URL
- `fixture_name` (character varying) - Fixture name
- `fixture_date` (timestamp) - Fixture date
- `fixture_status` (character varying) - Fixture status
- `season_id` (uuid) - Season identifier
- `season_name` (character varying) - Season name
- `venue_id` (uuid) - Venue identifier
- `organization_id` (text) - Organization identifier

**Scoring Statistics:**
- `goals_scored` (integer) - Goals scored in match
- `goals_conceded` (integer) - Goals conceded in match
- `field_goals_scored` (integer) - Field goals scored
- `penalty_corners_scored` (integer) - Penalty corners scored
- `penalty_strokes_scored` (integer) - Penalty strokes scored
- `challenges_scored` (integer) - Challenges scored
- `goals_shoot_out` (integer) - Shootout goals

**Performance Statistics:**
- `shots` (integer) - Total shots
- `shots_on_target` (integer) - Shots on target
- `shots_missed` (integer) - Shots missed
- `shot_accuracy` (numeric) - Shot accuracy percentage
- `saves` (integer) - Saves made
- `assists` (integer) - Assists
- `passes` (integer) - Total passes
- `bad_passes` (integer) - Bad passes
- `possession` (numeric) - Possession percentage

**Defensive Statistics:**
- `tackles` (integer) - Tackles made
- `blocks` (integer) - Blocks made
- `interceptions` (integer) - Interceptions

**Disciplinary:**
- `green_cards` (integer) - Green cards received
- `yellow_cards` (integer) - Yellow cards received
- `red_cards` (integer) - Red cards received

**Match Context:**
- `opponent_id` (uuid) - Opponent entity ID
- `opponent_name` (character varying) - Opponent name
- `opponent_code` (character varying) - Opponent code
- `home_away` (text) - Home/Away indicator
- `home_score` (character varying) - Home team score
- `away_score` (character varying) - Away team score
- `team_score` (integer) - This team's score
- `opponent_score` (integer) - Opponent's score

### Get Entity Statistics by ID
`GET /fixturestats/{entity_id}`

**Database View:** `entity_season_statistics_frontend`

*Uses the same 61 fields as the entity season statistics endpoint above.*

### Get Player Match Statistics
`GET /fixtures/players/{fixtureId}`

**Database View:** `player_fixture_statistics_matchcenter`

**Response Fields (59 total):**

*Key field categories:*

**Player Identity:**
- `player_fixture_statistics_id` (integer) - Statistics record ID
- `person_id` (uuid) - Player identifier
- `entity_id` (uuid) - Team entity identifier
- `fixture_id` (uuid) - Fixture identifier
- `bib` (text) - Player's jersey number
- `position` (text) - Player's position
- `team_name` (character varying) - Team name
- `team_code` (character varying) - Team code
- `player_name` (text) - Player's full name
- `player_photo` (text) - Player's photo URL

**Participation:**
- `participated` (boolean) - Whether player participated
- `starter` (boolean) - Whether player was a starter
- `is_player` (boolean) - Whether record is for a player
- `is_team_official` (boolean) - Whether record is for team official
- `minutes` (interval) - Minutes played
- `did_not_play_reason` (text) - Reason if player didn't play

**Scoring:**
- `goals_scored` (integer) - Goals scored
- `assists` (integer) - Assists
- `field_goals_scored` (integer) - Field goals scored
- `penalty_corners_scored` (integer) - Penalty corners scored
- `penalty_strokes_scored` (integer) - Penalty strokes scored
- `challenges_scored` (integer) - Challenges scored
- `goals_shootout` (integer) - Shootout goals

**Shooting:**
- `shots` (integer) - Total shots
- `shots_on_target` (integer) - Shots on target
- `shots_missed` (integer) - Shots missed
- `shot_accuracy` (numeric) - Shot accuracy percentage

**Passing:**
- `passes` (integer) - Total passes
- `bad_passes` (integer) - Bad passes
- `pass_accuracy` (numeric) - Pass accuracy percentage

**Defense:**
- `tackles` (integer) - Tackles made
- `blocks` (integer) - Blocks made
- `interceptions` (integer) - Interceptions
- `saves` (integer) - Saves (for goalkeepers)
- `saves_shootout` (integer) - Shootout saves

**Discipline:**
- `green_cards` (integer) - Green cards received
- `yellow_cards` (integer) - Yellow cards received
- `red_cards` (integer) - Red cards received

**Performance Rating:**
- `plus` (integer) - Plus rating
- `minus` (integer) - Minus rating
- `plus_minus` (integer) - Plus/minus rating

*Plus additional fields for penalty actions, circle penetrations, and match context.*

---

## Fixtures & Matches

### Get Fixtures by Group
`GET /fixtures/{group}`

**Data Source:** RPC function `get_fixtures_secure`

*Note: This endpoint uses a stored procedure with security filtering. Response schema varies based on user permissions.*

### Get All Fixtures (Admin Only)
`GET /fixtures/all`

**Data Source:** RPC function `get_fixtures_secure`

*Note: Admin-only access. Uses same RPC function as fixtures by group.*

### Get Fixtures by Entity Group ID
`GET /fixtures/group/{entity_group_id}`

**Database View:** `fixture_details_view_v2`

**Response Fields (40 total):**

| Field | Type | Nullable | Description |
| --- | --- | --- | --- |
| `competition_id` | uuid | YES | Competition identifier |
| `season_id` | uuid | YES | Season identifier |
| `fixture_id` | uuid | YES | Fixture identifier |
| `fixture_number` | integer | YES | Fixture number in season |
| `status` | character varying | YES | Fixture status (scheduled, live, completed, etc.) |
| `season_logo_url` | text | YES | Season logo URL |
| `season_start_date` | date | YES | Season start date |
| `season_end_date` | date | YES | Season end date |
| `season_status` | character varying | YES | Season status |
| `home_team_entity_group_id` | character varying | YES | Home team entity group identifier |
| `home_team_entity_id` | uuid | YES | Home team entity identifier |
| `home_team_name` | character varying | YES | Home team name |
| `home_team_group_code` | character varying | YES | Home team group code |
| `away_team_entity_group_id` | character varying | YES | Away team entity group identifier |
| `away_team_entity_id` | uuid | YES | Away team entity identifier |
| `away_team_name` | character varying | YES | Away team name |
| `away_team_group_code` | character varying | YES | Away team group code |
| `home_score` | character varying | YES | Home team score |
| `home_secondary_score` | character varying | YES | Home team secondary score |
| `away_score` | character varying | YES | Away team score |
| `away_secondary_score` | character varying | YES | Away team secondary score |
| `season_name` | character varying | YES | Season name |
| `competition_name` | character varying | YES | Competition name |
| `start_time_local` | timestamp without time zone | YES | Match start time (local timezone) |
| `start_time_utc` | timestamp without time zone | YES | Match start time (UTC) |
| `venue_name` | character varying | YES | Venue name |
| `venue_country` | character varying | YES | Venue country |
| `venue_timezone` | character varying | YES | Venue timezone |
| `home_team_flag_url` | text | YES | Home team flag URL |
| `away_team_flag_url` | text | YES | Away team flag URL |
| `home_team_code` | character varying | YES | Home team code |
| `away_team_code` | character varying | YES | Away team code |
| `season_age_group` | character varying | YES | Season age group |
| `season_discipline` | character varying | YES | Season discipline |
| `season_gender` | character varying | YES | Season gender |
| `last_score_synced` | timestamp without time zone | YES | Last score synchronization time |
| `organization_id` | character varying | YES | Organization identifier |
| `fixture_name` | character varying | YES | Fixture name/description |
| `home_result_status` | character varying | YES | Home team result status (win/loss/draw) |
| `away_result_status` | character varying | YES | Away team result status (win/loss/draw) |

### Get Fixtures by Entity ID
`GET /fixtures/entity/{entity_id}`

**Database View:** `fixture_details_view_v2`

*Uses the same 40 fields as fixtures by entity group ID above.*

### Get Fixture Timeline
`GET /fixtures/timeline/{fixtureId}`

**Database View:** `fixture_timeline`

**Description:** Returns timeline events for a specific fixture (REST endpoint, not WebSocket)

**Default Sort:** `period_id.asc,event_time.asc,sequence_number.asc`

**Response Fields (28 total):**

| Field | Type | Nullable | Description |
| --- | --- | --- | --- |
| `event_id` | uuid | YES | Event identifier |
| `fixture_id` | uuid | YES | Fixture identifier |
| `organization_id` | character varying | YES | Organization identifier |
| `event_time` | timestamp with time zone | YES | Event timestamp |
| `clock` | interval | YES | Match clock time |
| `period_id` | integer | YES | Period identifier |
| `sequence_number` | integer | YES | Event sequence number within period |
| `event_type` | character varying | YES | Type of event (goal, card, substitution, etc.) |
| `sub_type` | character varying | YES | Event subtype |
| `entity_id` | uuid | YES | Entity involved in event |
| `person_id` | uuid | YES | Person involved in event |
| `play_id` | uuid | YES | Play identifier |
| `scores` | jsonb | YES | Score state after event (JSON) |
| `success` | boolean | YES | Whether event was successful |
| `status` | character varying | YES | Event status |
| `official_id` | uuid | YES | Official who recorded event |
| `timestamp` | timestamp with time zone | YES | Event timestamp |
| `entity_name` | character varying | YES | Entity name |
| `entity_code` | character varying | YES | Entity code |
| `person_name` | text | YES | Person's name |
| `entity_image_url` | text | YES | Entity image URL |
| `person_image_url` | text | YES | Person's image URL |
| `discipline` | character varying | YES | Sport discipline |
| `options` | jsonb | YES | Additional event options (JSON) |
| `period_name` | text | YES | Period name |
| `clock_display` | text | YES | Human-readable clock display |
| `match_clock_seconds` | numeric | YES | Match time in seconds |
| `match_clock_display` | text | YES | Match clock display format |

### Get Fixture Event Details by Group
`GET /fixturedetails/{group}`

**Data Source:** RPC function `get_fixture_event_log_secure`

*Note: Uses stored procedure with security filtering.*

### Get All Fixture Event Details (Admin Only)
`GET /fixturedetails/all`

**Data Source:** RPC function `get_fixture_event_log_secure`

*Note: Admin-only access to all fixture event logs.*

---

## Organizations & Entities

### Get All Organizations
`GET /organizations`

**Database View:** `v_organizations`

**Response Fields (11 total):**

| Field | Type | Nullable | Description |
| --- | --- | --- | --- |
| `organization_id` | text | YES | Organization identifier |
| `abbreviation_latin` | text | YES | Organization abbreviation (Latin script) |
| `abbreviation_local` | text | YES | Organization abbreviation (local script) |
| `added` | timestamp without time zone | YES | When organization was added |
| `country_code` | text | YES | ISO country code |
| `default_locale` | text | YES | Default locale/language code |
| `images` | jsonb | YES | Organization images (JSON) |
| `name_latin` | text | YES | Organization name (Latin script) |
| `name_local` | text | YES | Organization name (local script) |
| `region_type` | text | YES | Type of region (national, regional, local) |
| `updated` | timestamp without time zone | YES | Last update timestamp |

### Get All Entities
`GET /entities`

**Database View:** `v_entities_with_groups`

**Response Fields (50 total):**

*Key field categories:*

**Entity Basic Information:**
- `entity_id` (uuid) - Entity identifier
- `entity_added` (timestamp) - When entity was added
- `entity_code_latin` (character varying) - Entity code (Latin script)
- `entity_code_local` (character varying) - Entity code (local script)
- `entity_name_full_latin` (character varying) - Full entity name (Latin)
- `entity_name_full_local` (character varying) - Full entity name (local)
- `entity_status` (character varying) - Entity status
- `entity_updated` (timestamp) - Last entity update

**Entity Classification:**
- `age_group` (character varying) - Age group
- `discipline` (character varying) - Sport discipline
- `gender` (character varying) - Gender category
- `grade` (character varying) - Competition grade
- `standard` (character varying) - Entity standard

**Entity Metadata:**
- `entity_additional_names` (jsonb) - Additional names (JSON)
- `entity_colors` (jsonb) - Entity colors (JSON)
- `entity_images` (jsonb) - Entity images (JSON)
- `entity_social` (jsonb) - Social media (JSON)
- `entity_links` (jsonb) - Entity links (JSON)
- `entity_meta` (jsonb) - Entity metadata (JSON)

**Entity Contact:**
- `website_url` (text) - Website URL
- `instagram_url` (text) - Instagram URL
- `twitter_url` (text) - Twitter URL
- `facebook_url` (text) - Facebook URL

**Entity Group Information:**
- `group_id` (integer) - Group identifier
- `group_code_latin` (character varying) - Group code (Latin)
- `group_code_local` (character varying) - Group code (local)
- `group_name_full_latin` (character varying) - Group name (Latin)
- `group_name_full_local` (character varying) - Group name (local)
- `group_status` (character varying) - Group status
- `group_colors` (jsonb) - Group colors (JSON)
- `group_images` (jsonb) - Group images (JSON)
- `group_meta` (jsonb) - Group metadata (JSON)

**Organizational:**
- `organization_id` (character varying) - Organization identifier
- `entity_organization` (jsonb) - Entity organization info (JSON)
- `group_organization` (jsonb) - Group organization info (JSON)
- `representing` (character varying) - What entity represents

*Plus additional fields for addresses, external references, and venue information.*

### Get Entity Players History
`GET /entities/{entity_id}/players`

**Database View:** `v_entity_players_participated`

**Description:** Returns all players who have ever participated in at least one match for the specified entity, including their debut date, last match date, and total matches played.

**Response Fields (10 total):**

| Field | Type | Nullable | Description |
| --- | --- | --- | --- |
| `entity_id` | uuid | YES | Entity identifier |
| `person_id` | uuid | YES | Player's unique identifier |
| `person_name` | text | YES | Player's full name (Latin preferred, falls back to local) |
| `organization_id` | text | YES | Player's organization identifier |
| `image_url` | text | YES | Player's profile image URL |
| `status` | text | YES | Player's current status (ACTIVE, INACTIVE, etc.) |
| `nationality` | text | YES | Player's nationality code (ISO format) |
| `dob` | date | YES | Player's date of birth |
| `debut_date` | timestamp without time zone | YES | Date of player's first match for this entity |
| `last_match_date` | timestamp without time zone | YES | Date of player's most recent match for this entity |
| `total_matches_played` | bigint | YES | Total number of matches played for this entity |

---

## Public Widget Endpoints

### Get All Seasons (Public)
`GET /public/seasons`

**Database View:** `seasons`

**CORS:** Allows all origins (`*`)

**Fixed Filter:** `organization_id=eq.k11s0` (hardcoded for permissions app)

*Returns same fields as protected `/seasons` endpoint*

### Get Season Teams (Public)
`GET /public/seasons/{seasonId}/teams`

**Database View:** `v_season_teams_with_entities`

**CORS:** Allows all origins (`*`)

*Returns same 19 fields as protected endpoint*

### Get Season Officials (Public)
`GET /public/seasons/{seasonId}/officials`

**Database View:** `v_season_officials`

**CORS:** Allows all origins (`*`)

*Uses the same 33 fields as the protected season officials endpoint.*

### Get Current Fixture Clocks (Public)
`GET /clock/{fixtureId}`

**Database View:** `v_fixture_current_clocks`

**CORS:** Allows all origins (`*`)

**Response Fields (15 total):**

*Key fields include:*
- `fixture_id` (uuid) - Fixture identifier
- `clock_type` (character varying) - Type of clock (main, greenCard, etc.)
- `clock_action` (character varying) - Current clock action
- `is_running` (boolean) - Whether clock is running
- `period_id` (uuid) - Current period identifier
- `current_display` (character varying) - Human-readable time display
- `current_seconds` (numeric) - Current time in seconds
- `last_update_time` (timestamp) - Last clock update

**Note:** Validates UUID format before querying database.

---

# Real-Time WebSocket API

The TMS Hockey platform provides real-time updates for match events and clock data through WebSocket connections. The system uses PostgreSQL LISTEN/NOTIFY for instant change detection, Redis pub/sub for message distribution, and WebSocket for client communication.

**WebSocket Base URL:** `wss://ws.tms.hockey:8443`

## WebSocket Endpoints

| Endpoint | Description | Connection URL |
| --- | --- | --- |
| `/timeline` | Real-time match events (goals, cards, substitutions, etc.) | `wss://ws.tms.hockey:8443/timeline?fixture_id={fixtureId}` |
| `/clock` | Real-time clock and score updates | `wss://ws.tms.hockey:8443/clock?fixture_id={fixtureId}` |

### Connection Requirements

- **Protocol:** WebSocket Secure (WSS) over SSL/TLS
- **Port:** 8443
- **SSL Certificate:** Let's Encrypt
- **Authentication:** None required (fixture_id is the only parameter)
- **CORS:** Accepts all origins (web, mobile apps, Postman)
- **Heartbeat:** Ping/pong every 30 seconds
- **Auto-close:** Connections close 5 seconds after fixture ends

---

## Timeline WebSocket Endpoint

Provides real-time updates for all match events including goals, cards, substitutions, and penalty corners. Events are enriched with player/team names and images from the `fixture_timeline_all` view.

### Connection

```javascript
const ws = new WebSocket('wss://ws.tms.hockey:8443/timeline?fixture_id=d7bb15e0-7e6d-11f0-aae8-a5de09f529bd');
```

### Message Flow

1. **Connection Established** → Receive `connected` message
2. **Subscription Confirmed** → Receive `status` message  
3. **Match Events Occur** → Receive `timeline_update` messages
4. **Fixture Ends** → Receive `fixture_ended` message
5. **Connection Closes** → Automatic closure after 5 seconds

### Message Types

| Type | Description | When Sent |
| --- | --- | --- |
| `connected` | Initial connection confirmation | On successful WebSocket connection |
| `status` | Subscription status | After connection established |
| `timeline_update` | Event update (new/update/delete/shootout_refresh) | When match events occur |
| `fixture_ended` | Match completed notification | When fixture status becomes CONFIRMED |
| `connection_closing` | Connection terminating warning | 1 second before connection closes |
| `pong` | Heartbeat response | In response to ping message |

### Timeline Event Fields

All timeline events include the following fields from the `fixture_timeline_all` view:

| Field | Type | Description |
| --- | --- | --- |
| `event_id` | uuid | Unique event identifier |
| `fixture_id` | uuid | Fixture identifier |
| `organization_id` | string | Organization code (e.g., "k11s0", "HKHA") |
| `event_time` | timestamp | When event occurred (UTC) |
| `clock` | string | Period clock time (format: "HH:MM:SS") |
| `period_id` | integer | Period number (1-4: Regular, 10-11: Extra Time, 12: Shootout, 13: Extra Time 4) |
| `sequence_number` | integer | Event order within fixture |
| `event_type` | string | Type of event (see Event Types table) |
| `sub_type` | string | Event subtype (e.g., "awarded", "missed", "scored") |
| `entity_id` | uuid | Team entity identifier |
| `person_id` | uuid | Player identifier (null for team events) |
| `play_id` | uuid | Play sequence identifier |
| `scores` | object | Current score state (entity_id: score mapping) |
| `success` | boolean | Whether event was successful |
| `status` | string | Event status ("added", "updated", "deleted") |
| `official_id` | uuid | Recording official identifier |
| `timestamp` | timestamp | Database timestamp |
| `deleted_at` | timestamp | Soft delete timestamp (null if not deleted) |
| `is_deleted` | boolean | Soft delete flag |
| `entity_name` | string | Team name |
| `entity_code` | string | Team code (e.g., "IND", "CHN") |
| `person_name` | string | Player name |
| `entity_image_url` | string | Team logo URL |
| `person_image_url` | string | Player image URL |
| `discipline` | string | Sport discipline ("OUTDOOR", "INDOOR") |
| `options` | object | Additional event data (varies by event type) |
| `period_name` | string | Human-readable period ("Period 1", "Extra Time 1", "Shoot Out") |
| `clock_display` | string | Formatted clock time |
| `match_clock_seconds` | number | Cumulative match time in seconds |
| `match_clock_display` | string | Cumulative match time (MM:SS format) |

### Event Types

| Event Type | Description | Has Person | Common Subtypes | Options Fields |
| --- | --- | --- | --- | --- |
| `goal` | Goal scored | Yes | null, "field", "penalty" | goalOutcome, assist_by |
| `penaltyCorner` | Penalty corner awarded | No | null | - |
| `penaltyStroke` | Penalty stroke | Yes | "awarded", "missed", "scored" | reason |
| `card` | Disciplinary card | Yes | "green", "yellow", "red" | duration_minutes, offense |
| `substitution` | Player substitution | Yes | null | player_in, player_out |
| `timeout` | Team timeout | No | null | duration |
| `period_start` | Period started | No | null | - |
| `period_end` | Period ended | No | null | - |

### Update Types

| Update Type | Description | Additional Fields |
| --- | --- | --- |
| `new` | New event added | Full event object in `event` field |
| `update` | Existing event modified | Full updated event object in `event` field |
| `delete` | Event soft-deleted | Full event with `is_deleted=true`, includes `play_id` and `event_id` |
| `shootout_refresh` | All shootout events refresh | Array in `events` field, may include `deleted_event`, `updated_event`, or `new_event` |

### Period IDs

| Period ID | Period Name | Description |
| --- | --- | --- |
| 0 | Full Match | Entire match statistics |
| 1-4 | Period 1-4 | Regular periods (20 minutes each by default) |
| 10 | Extra Time 1 | First extra time period |
| 11 | Extra Time 2 | Second extra time period |
| 12 | Shoot Out | Penalty shootout |
| 13 | Extra Time 4 | Fourth extra time period |

---

## Clock WebSocket Endpoint

Provides real-time clock updates, period changes, and score updates during matches.

### Connection

```javascript
const ws = new WebSocket('wss://ws.tms.hockey:8443/clock?fixture_id=d7bb15e0-7e6d-11f0-aae8-a5de09f529bd');
```

### Clock Features

- **Multiple Fixtures:** Can subscribe to additional fixtures using `subscribe` message
- **Unified Lifecycle:** Closes together with timeline when fixture ends
- **Clock Types:** Main clock, green card timers, sin bin timers
- **Period Transitions:** Automatic updates on period changes

### Clock Message Format

```json
{
    "type": "clock_update",
    "data": {
        "fixture_id": "d7bb15e0-7e6d-11f0-aae8-a5de09f529bd",
        "fixture_status": "IN_PROGRESS",
        "clocks": [{
            "clock_type": "main",
            "is_running": true,
            "current_display": "18:42",
            "current_seconds": 1122,
            "period_id": 1,
            "action": "running"
        }],
        "scores": {
            "home_score": 2,
            "away_score": 1
        },
        "fixture_ended": false
    }
}
```

---

## Message Examples

### Connection Messages

#### Initial Connection (Timeline)
```json
{
    "type": "connected",
    "connection_type": "timeline",
    "fixture_id": "d7bb15e0-7e6d-11f0-aae8-a5de09f529bd",
    "connection_id": 1,
    "server_time": "2025-08-29T10:45:00.000Z",
    "message": "Connected to timeline updates"
}
```

#### Subscription Status
```json
{
    "type": "status",
    "message": "Subscribed to timeline updates for fixture d7bb15e0-7e6d-11f0-aae8-a5de09f529bd",
    "fixture_id": "d7bb15e0-7e6d-11f0-aae8-a5de09f529bd",
    "waiting_for_initial_data": true
}
```

### Event Messages

#### Goal Scored
```json
{
    "type": "timeline_update",
    "fixture_id": "d7bb15e0-7e6d-11f0-aae8-a5de09f529bd",
    "server_time": "2025-08-29T10:51:23.030397+00:00",
    "update_type": "new",
    "event": {
        "event_id": "18833b50-84c6-11f0-af6d-855e50fcfdf0",
        "fixture_id": "d7bb15e0-7e6d-11f0-aae8-a5de09f529bd",
        "organization_id": "k11s0",
        "event_time": "2025-08-29T10:51:19.812000+00:00",
        "clock": "00:11:59",
        "period_id": 3,
        "sequence_number": 306,
        "event_type": "goal",
        "sub_type": null,
        "entity_id": "8b89fb15-4727-11ef-98a6-ffae7c73f6b4",
        "person_id": "7ab6f714-4731-11ef-9b8b-c9c6b571658e",
        "play_id": "18833b51-84c6-11f0-af6d-855e50fcfdf0",
        "scores": {
            "802671c5-4727-11ef-b7df-ffae7c73f6b4": 1,
            "8b89fb15-4727-11ef-98a6-ffae7c73f6b4": 3
        },
        "success": true,
        "status": "added",
        "official_id": null,
        "timestamp": "2025-08-29T10:51:15.052000+00:00",
        "deleted_at": null,
        "is_deleted": false,
        "entity_name": "India",
        "entity_code": "IND",
        "person_name": "SINGH Harmanpreet",
        "entity_image_url": "https://images.dc.prod.cloud.atriumsports.com/k11s0/0e644e2a1c3d46ca8d6900d6ebb0d1b4",
        "person_image_url": "https://images.dc.prod.cloud.atriumsports.com/k11s0/afb0e4d0622846428bd64378bd3e3897",
        "discipline": "OUTDOOR",
        "options": {
            "goalOutcome": "PENALTY_CORNER"
        },
        "period_name": "Period 3",
        "clock_display": "00:11:59",
        "match_clock_seconds": 1981,
        "match_clock_display": "33:01"
    }
}
```

#### Soft Delete Event
```json
{
    "type": "timeline_update",
    "fixture_id": "d7bb15e0-7e6d-11f0-aae8-a5de09f529bd",
    "server_time": "2025-08-29T10:59:12.174634+00:00",
    "update_type": "delete",
    "event": {
        "event_id": "ebcb5dd1-84c6-11f0-af6d-855e50fcfdf0",
        "fixture_id": "d7bb15e0-7e6d-11f0-aae8-a5de09f529bd",
        "organization_id": "k11s0",
        "event_time": "2025-08-29T10:57:14.295000+00:00",
        "clock": "00:08:58",
        "period_id": 3,
        "sequence_number": 397,
        "event_type": "goal",
        "sub_type": null,
        "entity_id": "802671c5-4727-11ef-b7df-ffae7c73f6b4",
        "person_id": null,
        "play_id": "ecb7b9a0-84c6-11f0-af6d-855e50fcfdf0",
        "scores": {
            "802671c5-4727-11ef-b7df-ffae7c73f6b4": 3,
            "8b89fb15-4727-11ef-98a6-ffae7c73f6b4": 3
        },
        "success": true,
        "status": "deleted",
        "official_id": null,
        "timestamp": "2025-08-29T10:59:10.947000+00:00",
        "deleted_at": "2025-08-29T10:59:12.158524+00:00",
        "is_deleted": true,
        "entity_name": "China",
        "entity_code": "CHN",
        "person_name": null,
        "entity_image_url": "https://images.dc.prod.cloud.atriumsports.com/k11s0/ced60ff2f48b427bbc73681374e59ca2",
        "person_image_url": null,
        "discipline": "OUTDOOR",
        "options": {
            "goalOutcome": "PENALTY_CORNER"
        },
        "period_name": "Period 3",
        "clock_display": "00:08:58",
        "match_clock_seconds": 2162,
        "match_clock_display": "36:02"
    },
    "play_id": "ecb7b9a0-84c6-11f0-af6d-855e50fcfdf0",
    "event_id": "ebcb5dd1-84c6-11f0-af6d-855e50fcfdf0"
}
```

#### Penalty Stroke Awarded
```json
{
    "type": "timeline_update",
    "fixture_id": "d7bb15e0-7e6d-11f0-aae8-a5de09f529bd",
    "server_time": "2025-08-29T11:03:40.347271+00:00",
    "update_type": "new",
    "event": {
        "event_id": "cc810051-84c7-11f0-af6d-855e50fcfdf0",
        "fixture_id": "d7bb15e0-7e6d-11f0-aae8-a5de09f529bd",
        "organization_id": "k11s0",
        "event_time": "2025-08-29T11:03:31.285000+00:00",
        "clock": "00:06:38",
        "period_id": 3,
        "sequence_number": 449,
        "event_type": "penaltyStroke",
        "sub_type": "awarded",
        "entity_id": "8b89fb15-4727-11ef-98a6-ffae7c73f6b4",
        "person_id": "7ab6f714-4731-11ef-9b8b-c9c6b571658e",
        "play_id": "cc810052-84c7-11f0-af6d-855e50fcfdf0",
        "scores": {
            "802671c5-4727-11ef-b7df-ffae7c73f6b4": 2,
            "8b89fb15-4727-11ef-98a6-ffae7c73f6b4": 3
        },
        "success": null,
        "status": "added",
        "official_id": null,
        "timestamp": "2025-08-29T11:01:23.835000+00:00",
        "deleted_at": null,
        "is_deleted": false,
        "entity_name": "India",
        "entity_code": "IND",
        "person_name": "SINGH Harmanpreet",
        "entity_image_url": "https://images.dc.prod.cloud.atriumsports.com/k11s0/0e644e2a1c3d46ca8d6900d6ebb0d1b4",
        "person_image_url": "https://images.dc.prod.cloud.atriumsports.com/k11s0/afb0e4d0622846428bd64378bd3e3897",
        "discipline": "OUTDOOR",
        "options": {},
        "period_name": "Period 3",
        "clock_display": "00:06:38",
        "match_clock_seconds": 2302,
        "match_clock_display": "38:22"
    }
}
```

#### Fixture Ended
```json
{
    "type": "fixture_ended",
    "fixture_id": "d7bb15e0-7e6d-11f0-aae8-a5de09f529bd",
    "server_time": "2025-08-29T12:00:00.000Z",
    "fixture_ended": true,
    "message": "Fixture has ended - timeline updates stopped"
}
```

---

## Client Implementation

### JavaScript/Browser

```javascript
class TimelineConnection {
    constructor(fixtureId) {
        this.fixtureId = fixtureId;
        this.ws = null;
        this.reconnectAttempts = 0;
        this.maxReconnectAttempts = 5;
        this.reconnectDelay = [1000, 2000, 4000, 8000, 16000];
    }

    connect() {
        const url = `wss://ws.tms.hockey:8443/timeline?fixture_id=${this.fixtureId}`;
        this.ws = new WebSocket(url);

        this.ws.onopen = () => {
            console.log('Connected to timeline');
            this.reconnectAttempts = 0;
            this.startHeartbeat();
        };

        this.ws.onmessage = (event) => {
            const data = JSON.parse(event.data);
            this.handleMessage(data);
        };

        this.ws.onerror = (error) => {
            console.error('WebSocket error:', error);
        };

        this.ws.onclose = (event) => {
            console.log(`Connection closed: ${event.code} - ${event.reason}`);
            this.stopHeartbeat();
            
            if (event.code !== 1000) { // Not normal closure
                this.reconnect();
            }
        };
    }

    handleMessage(data) {
        switch(data.type) {
            case 'connected':
                console.log('Connection confirmed:', data);
                break;
            case 'timeline_update':
                this.processTimelineUpdate(data);
                break;
            case 'fixture_ended':
                console.log('Fixture ended');
                this.close();
                break;
            case 'pong':
                // Heartbeat response received
                break;
        }
    }

    processTimelineUpdate(data) {
        const { update_type, event } = data;
        
        switch(update_type) {
            case 'new':
                console.log(`New ${event.event_type} event:`, event);
                break;
            case 'update':
                console.log(`Updated event ${event.event_id}`);
                break;
            case 'delete':
                console.log(`Deleted event ${event.event_id}`);
                break;
            case 'shootout_refresh':
                console.log(`Shootout refresh with ${data.events?.length} events`);
                break;
        }
    }

    startHeartbeat() {
        this.heartbeatInterval = setInterval(() => {
            if (this.ws.readyState === WebSocket.OPEN) {
                this.ws.send(JSON.stringify({ type: 'ping' }));
            }
        }, 30000); // Every 30 seconds
    }

    stopHeartbeat() {
        if (this.heartbeatInterval) {
            clearInterval(this.heartbeatInterval);
        }
    }

    reconnect() {
        if (this.reconnectAttempts < this.maxReconnectAttempts) {
            const delay = this.reconnectDelay[this.reconnectAttempts] || 16000;
            console.log(`Reconnecting in ${delay}ms (attempt ${this.reconnectAttempts + 1})`);
            
            setTimeout(() => {
                this.reconnectAttempts++;
                this.connect();
            }, delay);
        }
    }

    close() {
        this.stopHeartbeat();
        if (this.ws) {
            this.ws.close(1000, 'Client closing');
        }
    }
}

// Usage
const timeline = new TimelineConnection('d7bb15e0-7e6d-11f0-aae8-a5de09f529bd');
timeline.connect();
```

### Testing with Postman

1. **New Request:** Create new WebSocket Request in Postman
2. **URL:** `wss://ws.tms.hockey:8443/timeline?fixture_id=YOUR_FIXTURE_ID`
3. **Connect:** Click Connect button
4. **Send Ping:** Send `{"type": "ping"}` to test connection
5. **Monitor:** Watch Messages tab for incoming events

### Testing with cURL

```bash
# Test WebSocket handshake
curl -i -N \
  -H "Connection: Upgrade" \
  -H "Upgrade: websocket" \
  -H "Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw==" \
  -H "Sec-WebSocket-Version: 13" \
  https://ws.tms.hockey:8443/timeline?fixture_id=d7bb15e0-7e6d-11f0-aae8-a5de09f529bd
```

---

## Architecture & Data Flow

### System Components

1. **PostgreSQL Database**
   - `fixture_playbyplay_events` table stores raw events
   - `fixture_timeline_all` view enriches with names/images
   - Trigger `timeline_change_trigger` detects changes
   - LISTEN/NOTIFY on `timeline_events` channel

2. **Timeline Publisher (Python)**
   - Listens for PostgreSQL notifications
   - Queries enriched data from view
   - Publishes to Redis channels
   - Handles 800-1000 concurrent users per fixture
   - Caches fixture status (60-second TTL)

3. **WebSocket Server (Node.js)**
   - Subscribes to Redis pub/sub channels
   - Manages WebSocket connections
   - Broadcasts to connected clients
   - Unified lifecycle management for clock/timeline

### Data Flow Sequence

```
Event Occurs → Streaming API → Database Insert → PostgreSQL Trigger
    ↓
NOTIFY timeline_events → Timeline Publisher → Query View
    ↓
Publish to Redis → WebSocket Server → Broadcast to Clients
```

### Redis Channels

- **Timeline:** `hockey:timeline:updates:{fixture_id}`
- **Timeline All:** `hockey:timeline:updates:all`
- **Clock:** `hockey:clock:updates:{fixture_id}`
- **Clock All:** `hockey:clock:updates:all`

---

## Performance & Monitoring

### Capacity Limits

| Metric | Value | Description |
| --- | --- | --- |
| Concurrent connections per fixture | 800-1000 | Maximum WebSocket connections |
| Active fixtures | 20-50 | Simultaneous matches supported |
| Message latency | < 100ms | Database change to client delivery |
| Database queries | 1 per event | Only on event occurrence |
| Cache hit rate | ~70% | Fixture status cache effectiveness |
| Memory usage | < 100MB | Per publisher instance |
| CPU usage | < 5% | Under normal load |

### Monitoring Endpoints

The WebSocket server logs statistics every 60 seconds:
- Active connections per endpoint
- Active fixtures being monitored
- Total messages sent
- Connection/disconnection events

### Service Management

```bash
# Check service status
./service_management.sh status

# View timeline logs
./service_management.sh logs timeline 100

# Follow timeline logs in real-time
./service_management.sh follow timeline

# Test WebSocket connection
./service_management.sh test timeline d7bb15e0-7e6d-11f0-aae8-a5de09f529bd
```

---

## Error Handling

### WebSocket Close Codes

| Code | Description | Client Action |
| --- | --- | --- |
| 1000 | Normal closure | No action needed |
| 1001 | Server going away | Reconnect after delay |
| 1008 | Policy violation (missing fixture_id) | Fix request parameters |
| 1011 | Internal server error | Reconnect with exponential backoff |

### Connection States

| State | Description | Recovery |
| --- | --- | --- |
| CONNECTING | Establishing connection | Wait for open event |
| OPEN | Active and receiving | Process messages normally |
| CLOSING | Server initiated close | Prepare for reconnection |
| CLOSED | Connection terminated | Reconnect if fixture active |

### Best Practices

1. **Always implement reconnection logic** with exponential backoff
2. **Send heartbeat pings** every 30 seconds to detect stale connections
3. **Handle all message types** including unknown types gracefully
4. **Parse scores object** using entity IDs as keys
5. **Check is_deleted flag** to filter soft-deleted events
6. **Use match_clock_seconds** for accurate time tracking
7. **Monitor fixture_ended** to stop reconnection attempts

---

## Error Responses

All endpoints return consistent error responses:

**Format:**
```json
{
  "error": "Error type",
  "detail": "Detailed error message"  
}
```

**HTTP Status Codes:**
- `200` - Success
- `401` - Authentication required
- `403` - Access denied / Admin required
- `404` - Resource not found  
- `500` - Internal server error

---

## Technical Notes

1. **Database Sources**: Views provide exact field names and types as documented
2. **RPC Functions**: Some endpoints use stored procedures with dynamic schemas
3. **Field Types**: 
   - `uuid` - PostgreSQL UUID format
   - `character varying` - Variable-length string
   - `text` - Unlimited length string
   - `jsonb` - Binary JSON data
   - `timestamp with/without time zone` - Date/time stamps
   - `interval` - Time intervals (e.g., match duration)
   - `bigint` - Large integers for statistics
   - `numeric` - Decimal numbers for percentages/rates

4. **Multilingual Support**: Many fields have `_latin` and `_local` variants
5. **Security**: Group-based access control in RPC functions, Redis-based mobile token storage
6. **Query Parameters**: Most endpoints support PostgREST query parameters for filtering, sorting, and field selection
7. **Direct Browser Access Protection**: API endpoints are protected from direct browser access, returning HTML error pages for browser requests

---

## Hockey Terminology

| Term | Description |
| --- | --- |
| **Challenges** | Hockey-specific scoring opportunities |
| **Field Goals** | Goals scored during regular play |
| **Penalty Corners** | Set pieces for defensive infractions |
| **Penalty Strokes** | Direct scoring opportunities |
| **Circle Penetrations** | Entries into scoring circle |
| **Long Corners** | Corner kicks for certain infractions |
| **Shootout** | Penalty stroke competition |
| **Bib** | Jersey/uniform number |
| **Plus/Minus** | Player rating system |

---

*Complete field reference for all TMS Hockey API endpoints with exact database field names, types, and descriptions. Includes real-time WebSocket support for live match updates.*
