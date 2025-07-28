# TMS Hockey API Documentation - Complete

**Base URL:** `https://api.tms.hockey`

---

## Authentication

The API supports multiple authentication methods for different use cases.

### Web Session Authentication

{% tabs %}
{% tab title="Web Login" %}
```http
POST /login
```
{% endtab %}

{% tab title="Livescores Login" %}
```http
POST /login-livescores
```
{% endtab %}

{% tab title="Response" %}
```json
{
  "ok": true
}
```
{% endtab %}
{% endtabs %}

Sets secure session cookie (`session_id`) valid for 4 hours.

### Mobile App Authentication

{% tabs %}
{% tab title="Mobile Login" %}
```http
POST /auth/mobile
Content-Type: application/json

{
  "clientId": "tms-hockey-mobile",
  "clientSecret": "your-client-secret"
}
```
{% endtab %}

{% tab title="Token Refresh" %}
```http
POST /auth/refresh
Content-Type: application/json

{
  "refreshToken": "hex-token"
}
```
{% endtab %}

{% tab title="Logout" %}
```http
POST /auth/logout
Authorization: Bearer <access-token>
```
{% endtab %}

{% tab title="Response" %}
```json
{
  "accessToken": "hex-token",
  "refreshToken": "hex-token", 
  "expiresAt": 1234567890,
  "tokenType": "Bearer"
}
```
{% endtab %}
{% endtabs %}

### Direct JWT Authentication

For API testing (Postman, etc.)

```http
Authorization: Bearer <jwt-token>
```

---

## Seasons

### Get All Seasons

{% swagger method="get" path="/seasons" baseUrl="https://api.tms.hockey" summary="Get all seasons with competition information" %}
{% swagger-description %}
Retrieves all seasons with their associated competition details from the v_seasons_with_competition view.
{% endswagger-description %}

{% swagger-response status="200: OK" description="Seasons with actual database fields" %}
```json
[
  {
    "id": 123,
    "season_id": "uuid",
    "added": "2024-01-01T10:00:00",
    "age_group": "Senior",
    "competition_id": "uuid",
    "competition_name": "National Indoor Championship",
    "discipline": "Hockey",
    "duration_full": 60,
    "start_date": "2024-01-15",
    "end_date": "2024-06-30",
    "event_type": "league",
    "gender": "M",
    "grade": "A",
    "include_in_statistics": true,
    "live_data_available": true,
    "live_video_available": false,
    "lock_standings": false,
    "name_latin": "2024 Indoor League",
    "name_local": "2024 Indoor League",
    "name_short_latin": "2024 Indoor",
    "name_short_local": "2024 Indoor",
    "organization_id": "org123",
    "promotion_relegation_rules": {"rule": "top_2_promote"},
    "representation": "club",
    "roster_configuration": {"max_players": 23},
    "season_type": "regular",
    "standard": "international",
    "standing_configuration_id": "uuid",
    "status": "active",
    "video_production": "basic",
    "year": 2024,
    "links": {"website": "https://example.com"},
    "meta": {"additional": "data"},
    "image_url": "https://example.com/season.png",
    "total_matches": 120,
    "matches_completed": 45
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Actual Database Fields (36 total):**

| Field | Type | Nullable | Description |
| --- | --- | --- | --- |
| `id` | integer | YES | Internal sequence ID |
| `season_id` | uuid | YES | Unique season identifier |
| `added` | timestamp without time zone | YES | When season was added |
| `age_group` | character varying | YES | Age group category |
| `competition_id` | uuid | YES | Associated competition ID |
| `competition_name` | character varying | YES | Competition name |
| `discipline` | character varying | YES | Sport discipline |
| `duration_full` | integer | YES | Full match duration in minutes |
| `start_date` | date | YES | Season start date |
| `end_date` | date | YES | Season end date |
| `event_type` | character varying | YES | Type of event |
| `gender` | character varying | YES | Gender category |
| `grade` | character varying | YES | Competition grade |
| `include_in_statistics` | boolean | YES | Whether to include in stats |
| `live_data_available` | boolean | YES | Live data availability |
| `live_video_available` | boolean | YES | Live video availability |
| `lock_standings` | boolean | YES | Whether standings are locked |
| `name_latin` | character varying | YES | Season name (Latin script) |
| `name_local` | character varying | YES | Season name (Local script) |
| `name_short_latin` | character varying | YES | Short name (Latin script) |
| `name_short_local` | character varying | YES | Short name (Local script) |
| `organization_id` | character varying | YES | Organization identifier |
| `promotion_relegation_rules` | jsonb | YES | Promotion/relegation rules JSON |
| `representation` | character varying | YES | Team representation type |
| `roster_configuration` | jsonb | YES | Roster configuration JSON |
| `season_type` | character varying | YES | Season type |
| `standard` | character varying | YES | Competition standard |
| `standing_configuration_id` | uuid | YES | Standing configuration ID |
| `status` | character varying | YES | Season status |
| `video_production` | character varying | YES | Video production level |
| `year` | integer | YES | Season year |
| `links` | jsonb | YES | Related links JSON |
| `meta` | jsonb | YES | Metadata JSON |
| `image_url` | text | YES | Season image URL |
| `total_matches` | bigint | YES | Total matches in season |
| `matches_completed` | bigint | YES | Completed matches count |

### Get Teams in Season

{% swagger method="get" path="/seasons/{seasonId}/teams" baseUrl="https://api.tms.hockey" summary="Get all teams in a season" %}
{% swagger-description %}
Retrieves all teams participating in a specific season from the v_season_teams_with_entities view.
{% endswagger-description %}

{% swagger-parameter in="path" name="seasonId" type="uuid" required="true" %}
Season identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Teams with actual database fields" %}
```json
[
  {
    "season_id": "uuid",
    "team_entity_id": "uuid",
    "team_organization_id": "org123",
    "team_external_id": "ext456",
    "conference_id": "uuid",
    "division_id": "uuid",
    "roster_status": "confirmed",
    "season_team_status": "active",
    "season_team_added": "2024-01-10T10:00:00Z",
    "season_team_updated": "2024-01-15T14:30:00Z",
    "team_name_latin": "Lightning Bolts",
    "team_name_local": "Lightning Bolts",
    "team_code_latin": "LB",
    "team_code_local": "LB",
    "team_representing": "Toronto",
    "logo_url": "https://example.com/logo.png",
    "final_standing": 3,
    "final_points": 42.5,
    "final_prize_money": 5000.00
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Actual Database Fields (19 total):**

| Field | Type | Nullable | Description |
| --- | --- | --- | --- |
| `season_id` | uuid | YES | Season identifier |
| `team_entity_id` | uuid | YES | Team entity identifier |
| `team_organization_id` | text | YES | Team organization ID |
| `team_external_id` | text | YES | External team ID |
| `conference_id` | uuid | YES | Conference identifier |
| `division_id` | uuid | YES | Division identifier |
| `roster_status` | text | YES | Roster confirmation status |
| `season_team_status` | text | YES | Team status in season |
| `season_team_added` | timestamp with time zone | YES | When team was added to season |
| `season_team_updated` | timestamp with time zone | YES | Last team update timestamp |
| `team_name_latin` | character varying | YES | Team name (Latin script) |
| `team_name_local` | character varying | YES | Team name (Local script) |
| `team_code_latin` | character varying | YES | Team code (Latin script) |
| `team_code_local` | character varying | YES | Team code (Local script) |
| `team_representing` | character varying | YES | What/where team represents |
| `logo_url` | text | YES | Team logo URL |
| `final_standing` | integer | YES | Final season standing position |
| `final_points` | numeric | YES | Final points earned in season |
| `final_prize_money` | numeric | YES | Prize money earned |

### Get Team Roster

{% swagger method="get" path="/seasons/{seasonId}/teams/{teamId}/roster" baseUrl="https://api.tms.hockey" summary="Get team player roster" %}
{% swagger-description %}
Retrieves player roster for a specific team in a season from the v_season_team_roster view.
{% endswagger-description %}

{% swagger-parameter in="path" name="seasonId" type="uuid" required="true" %}
Season identifier
{% endswagger-parameter %}

{% swagger-parameter in="path" name="teamId" type="uuid" required="true" %}
Team entity identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Player roster with actual database fields" %}
```json
[
  {
    "season_id": "uuid",
    "team_entity_id": "uuid",
    "person_id": "uuid",
    "bib": "15",
    "position": "Forward",
    "roster_status": "active",
    "roster_added": "2024-01-10T10:00:00Z",
    "roster_updated": "2024-01-15T14:30:00Z",
    "person_name": "John Smith",
    "person_image_url": "https://example.com/player.jpg",
    "person_dob": "1995-03-15",
    "nationality": "CAN",
    "organization_id": "org123"
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Actual Database Fields (13 total):**

| Field | Type | Nullable | Description |
| --- | --- | --- | --- |
| `season_id` | uuid | YES | Season identifier |
| `team_entity_id` | uuid | YES | Team entity identifier |
| `person_id` | uuid | YES | Player person identifier |
| `bib` | text | YES | Player's bib/jersey number |
| `position` | text | YES | Player's playing position |
| `roster_status` | text | YES | Player's roster status |
| `roster_added` | timestamp with time zone | YES | When player was added to roster |
| `roster_updated` | timestamp with time zone | YES | Last roster update timestamp |
| `person_name` | text | YES | Player's full name |
| `person_image_url` | text | YES | Player's profile image URL |
| `person_dob` | date | YES | Player's date of birth |
| `nationality` | text | YES | Player's nationality code |
| `organization_id` | text | YES | Organization identifier |

### Get Team Staff

{% swagger method="get" path="/seasons/{seasonId}/teams/{teamId}/staff" baseUrl="https://api.tms.hockey" summary="Get team staff roster" %}
{% swagger-description %}
Retrieves staff roster for a specific team in a season from the v_season_team_staff view.
{% endswagger-description %}

{% swagger-parameter in="path" name="seasonId" type="uuid" required="true" %}
Season identifier
{% endswagger-parameter %}

{% swagger-parameter in="path" name="teamId" type="uuid" required="true" %}
Team entity identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Staff roster with actual database fields" %}
```json
[
  {
    "season_id": "uuid",
    "team_entity_id": "uuid",
    "person_id": "uuid",
    "role": "Head Coach",
    "role_id": "uuid",
    "date_from": "2024-01-01T00:00:00",
    "date_to": "2024-12-31T23:59:59",
    "role_status": "active",
    "role_added": "2024-01-01T10:00:00",
    "role_updated": "2024-01-15T14:30:00",
    "person_name": "Jane Doe",
    "person_image_url": "https://example.com/coach.jpg",
    "person_dob": "1980-07-22",
    "organization_id": "org123"
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Actual Database Fields (14 total):**

| Field | Type | Nullable | Description |
| --- | --- | --- | --- |
| `season_id` | uuid | YES | Season identifier |
| `team_entity_id` | uuid | YES | Team entity identifier |
| `person_id` | uuid | YES | Staff person identifier |
| `role` | text | YES | Staff role/position title |
| `role_id` | uuid | YES | Role identifier |
| `date_from` | timestamp without time zone | YES | Role start date |
| `date_to` | timestamp without time zone | YES | Role end date |
| `role_status` | text | YES | Role status |
| `role_added` | timestamp without time zone | YES | When role was added |
| `role_updated` | timestamp without time zone | YES | Last role update |
| `person_name` | text | YES | Staff member's full name |
| `person_image_url` | text | YES | Staff member's image URL |
| `person_dob` | date | YES | Staff member's date of birth |
| `organization_id` | text | YES | Organization identifier |

### Get Season Standings

{% swagger method="get" path="/seasons/{seasonId}/standings" baseUrl="https://api.tms.hockey" summary="Get season standings" %}
{% swagger-description %}
Retrieves current standings for a season from the v_season_standings_with_entities view.
{% endswagger-description %}

{% swagger-parameter in="path" name="seasonId" type="uuid" required="true" %}
Season identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Season standings with actual database fields" %}
```json
[
  {
    "standing_id": "uuid",
    "season_id": "uuid",
    "team_entity_id": "uuid",
    "fixture_type": "regular",
    "pool_code": "A",
    "standing_position": 1,
    "played": 10,
    "wins": 8,
    "draws": 1,
    "losses": 1,
    "scored_for": 32,
    "scored_against": 15,
    "points_difference": 17,
    "result_string": "WWWDL",
    "streak": 3,
    "win_percentage": 80.0,
    "byes": 0,
    "bonus_standing_points": 2,
    "bye_standing_points": 0,
    "penalty_standing_points": 0,
    "standing_points": 25,
    "latest": true,
    "live": true,
    "standing_added": "2024-01-15T10:00:00Z",
    "standing_updated": "2024-05-28T21:30:00Z",
    "team_name": "Lightning Bolts",
    "team_code": "LB",
    "logo_url": "https://example.com/logo.png",
    "pool_name_latin": "Pool A",
    "pool_name_local": "Pool A",
    "pool_abbreviation_latin": "A",
    "pool_abbreviation_local": "A",
    "pool_stage_code": "GROUP",
    "pool_order": 1,
    "pool_external_id": "pool_a_123",
    "pool_organization_id": "org123"
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Actual Database Fields (36 total):**

| Field | Type | Nullable | Description |
| --- | --- | --- | --- |
| `standing_id` | uuid | YES | Standing record identifier |
| `season_id` | uuid | YES | Season identifier |
| `team_entity_id` | uuid | YES | Team entity identifier |
| `fixture_type` | text | YES | Type of fixtures included |
| `pool_code` | text | YES | Pool/group code |
| `standing_position` | integer | YES | Current standing position |
| `played` | integer | YES | Games played |
| `wins` | integer | YES | Number of wins |
| `draws` | integer | YES | Number of draws |
| `losses` | integer | YES | Number of losses |
| `scored_for` | integer | YES | Goals/points scored |
| `scored_against` | integer | YES | Goals/points conceded |
| `points_difference` | integer | YES | Goal/point difference |
| `result_string` | text | YES | Recent results string |
| `streak` | integer | YES | Current win/loss streak |
| `win_percentage` | numeric | YES | Win percentage |
| `byes` | integer | YES | Number of byes |
| `bonus_standing_points` | integer | YES | Bonus points earned |
| `bye_standing_points` | integer | YES | Points from byes |
| `penalty_standing_points` | integer | YES | Penalty points deducted |
| `standing_points` | integer | YES | Total standing points |
| `latest` | boolean | YES | Whether this is latest standing |
| `live` | boolean | YES | Whether standing is live |
| `standing_added` | timestamp with time zone | YES | When standing was added |
| `standing_updated` | timestamp with time zone | YES | Last standing update |
| `team_name` | character varying | YES | Team name |
| `team_code` | character varying | YES | Team code |
| `logo_url` | text | YES | Team logo URL |
| `pool_name_latin` | text | YES | Pool name (Latin script) |
| `pool_name_local` | text | YES | Pool name (Local script) |
| `pool_abbreviation_latin` | text | YES | Pool abbreviation (Latin) |
| `pool_abbreviation_local` | text | YES | Pool abbreviation (Local) |
| `pool_stage_code` | text | YES | Pool stage code |
| `pool_order` | integer | YES | Pool display order |
| `pool_external_id` | text | YES | External pool identifier |
| `pool_organization_id` | text | YES | Pool organization ID |

### Get Season Officials

{% swagger method="get" path="/seasons/{seasonId}/officials" baseUrl="https://api.tms.hockey" summary="Get season officials" %}
{% swagger-description %}
Retrieves officials assigned to a season from the v_season_officials view.
{% endswagger-description %}

{% swagger-parameter in="path" name="seasonId" type="uuid" required="true" %}
Season identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Season officials with actual database fields (first 15 of 33 fields shown)" %}
```json
[
  {
    "season_id": "uuid",
    "person_id": "uuid",
    "role": "Referee",
    "role_id": "uuid",
    "date_from": "2024-01-01T00:00:00",
    "date_to": "2024-12-31T23:59:59",
    "role_status": "active",
    "role_added": "2024-01-01T10:00:00",
    "role_updated": "2024-01-15T14:30:00",
    "person_name": "John Official",
    "person_image_url": "https://example.com/official.jpg",
    "person_dob": "1975-05-10",
    "nationality": "USA",
    "organization_id": "org123",
    "additional_organization_id": "regional456"
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Actual Database Fields (33 total):** *First 15 fields shown above, view contains comprehensive official information including certifications, contact details, and assignment history.*

---

## Players & Personnel

### Get Player Information

{% swagger method="get" path="/persons/{person_id}" baseUrl="https://api.tms.hockey" summary="Get player statistics and information" %}
{% swagger-description %}
Retrieves detailed player statistics and information using the get_player_stats_by_person_id RPC function.
{% endswagger-description %}

{% swagger-parameter in="path" name="person_id" type="uuid" required="true" %}
Player's person identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Player information (RPC function - schema varies)" %}
```json
{
  "person_id": "uuid",
  "player_name": "John Smith",
  "career_statistics": {},
  "current_teams": [],
  "recent_matches": []
}
```
{% endswagger-response %}
{% endswagger %}

*Note: This endpoint uses an RPC function. The exact response schema depends on the function implementation.*

### Get Person Roles

{% swagger method="get" path="/person_roles/{person_id}" baseUrl="https://api.tms.hockey" summary="Get person roles across seasons" %}
{% swagger-description %}
Retrieves all roles a person has across different seasons and matches from the person_roles_combined view.
{% endswagger-description %}

{% swagger-parameter in="path" name="person_id" type="uuid" required="true" %}
Person identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Person roles with actual database fields" %}
```json
[
  {
    "person_id": "uuid",
    "season_id": "uuid",
    "season_name": "2024 Indoor League",
    "season_start": "2024-01-15",
    "season_end": "2024-06-30",
    "season_role": "player",
    "season_role_subtype": "forward",
    "fixture_id": "uuid",
    "fixture_name": "Semi-Final",
    "match_date": "2024-05-15T19:00:00",
    "home_team": "Lightning Bolts",
    "away_team": "Thunder Hawks",
    "home_score": "3",
    "away_score": "2",
    "home_secondary_score": "0",
    "away_secondary_score": "1",
    "match_role": "starter",
    "venue_name": "Central Arena",
    "source": "roster",
    "entity_id": "uuid",
    "organization_id": "org123"
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Actual Database Fields (21 total):**

| Field | Type | Nullable | Description |
| --- | --- | --- | --- |
| `person_id` | uuid | YES | Person identifier |
| `season_id` | uuid | YES | Season identifier |
| `season_name` | character varying | YES | Season name |
| `season_start` | date | YES | Season start date |
| `season_end` | date | YES | Season end date |
| `season_role` | text | YES | Role in season (player/staff/official) |
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
| `source` | text | YES | Data source (roster/lineup/etc) |
| `entity_id` | uuid | YES | Associated entity ID |
| `organization_id` | text | YES | Organization identifier |

### Get Player Active Seasons

{% swagger method="get" path="/activeseasons/players/{person_id}" baseUrl="https://api.tms.hockey" summary="Get player active seasons" %}
{% swagger-description %}
Retrieves all active seasons for a specific player from the player_active_seasons view.
{% endswagger-description %}

{% swagger-parameter in="path" name="person_id" type="uuid" required="true" %}
Player identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Player's active seasons with actual database fields" %}
```json
[
  {
    "person_id": "uuid",
    "season_id": "uuid",
    "entity_id": "uuid",
    "bib": "15",
    "position": "Forward",
    "roster_status": "active",
    "team_name": "Lightning Bolts",
    "team_code": "LB",
    "season_name": "2024 Indoor League",
    "start_date": "2024-01-15",
    "end_date": "2024-06-30",
    "season_status": "active",
    "organization_id": "org123",
    "year": 2024,
    "discipline": "Hockey",
    "gender": "M",
    "age_group": "Senior"
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Actual Database Fields (17 total):**

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

{% swagger method="get" path="/seasons/{seasonId}/playerstats" baseUrl="https://api.tms.hockey" summary="Get player season statistics" %}
{% swagger-description %}
Retrieves comprehensive player statistics for a specific season from the player_season_statistics_frontend view.
{% endswagger-description %}

{% swagger-parameter in="path" name="seasonId" type="uuid" required="true" %}
Season identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Player statistics with actual database fields (all 39 fields)" %}
```json
[
  {
    "person_id": "uuid",
    "season_id": "uuid", 
    "season_name": "2024 Indoor League",
    "season_start": "2024-01-15",
    "season_end": "2024-06-30",
    "team_entity_id": "uuid",
    "team_code": "LB",
    "team_name": "Lightning Bolts",
    "team_logo_url": "https://example.com/logo.png",
    "organization_id": "org123",
    "season_standing": 3,
    "matches_played": 12,
    "total_challenges_scored": 5,
    "total_field_goals_scored": 6,
    "total_goals_scored": 8,
    "total_penalty_corners_scored": 2,
    "total_penalty_strokes_scored": 0,
    "total_green_cards": 0,
    "total_yellow_cards": 1,
    "total_red_cards": 0,
    "wins": 8,
    "losses": 2,
    "draws": 2,
    "shootout_wins": 1,
    "shootout_losses": 0,
    "avg_challenges_scored": 0.42,
    "avg_field_goals_scored": 0.50,
    "avg_goals_scored": 0.67,
    "avg_penalty_corners_scored": 0.17,
    "avg_penalty_strokes_scored": 0.00,
    "avg_green_cards": 0.00,
    "avg_yellow_cards": 0.08,
    "avg_red_cards": 0.00,
    "total_goals_against": 18,
    "avg_goals_against": 1.50,
    "first_match_date": "2024-01-20T15:00:00",
    "last_match_date": "2024-05-28T19:00:00",
    "player_name": "John Smith",
    "player_image": "https://example.com/player.jpg"
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Actual Database Fields (39 total):**

| Field | Type | Nullable | Description |
| --- | --- | --- | --- |
| `person_id` | uuid | YES | Player identifier |
| `season_id` | uuid | YES | Season identifier |
| `season_name` | character varying | YES | Season name |
| `season_start` | date | YES | Season start date |
| `season_end` | date | YES | Season end date |
| `team_entity_id` | uuid | YES | Team entity ID |
| `team_code` | character varying | YES | Team code |
| `team_name` | character varying | YES | Team name |
| `team_logo_url` | text | YES | Team logo URL |
| `organization_id` | character varying | YES | Organization ID |
| `season_standing` | integer | YES | Team's season standing |
| `matches_played` | bigint | YES | Matches played |
| `total_challenges_scored` | bigint | YES | Total challenges scored |
| `total_field_goals_scored` | bigint | YES | Total field goals |
| `total_goals_scored` | bigint | YES | Total goals scored |
| `total_penalty_corners_scored` | bigint | YES | Penalty corners scored |
| `total_penalty_strokes_scored` | bigint | YES | Penalty strokes scored |
| `total_green_cards` | bigint | YES | Green cards received |
| `total_yellow_cards` | bigint | YES | Yellow cards received |
| `total_red_cards` | bigint | YES | Red cards received |
| `wins` | bigint | YES | Team wins |
| `losses` | bigint | YES | Team losses |
| `draws` | bigint | YES | Team draws |
| `shootout_wins` | bigint | YES | Shootout wins |
| `shootout_losses` | bigint | YES | Shootout losses |
| `avg_challenges_scored` | numeric | YES | Average challenges per game |
| `avg_field_goals_scored` | numeric | YES | Average field goals per game |
| `avg_goals_scored` | numeric | YES | Average goals per game |
| `avg_penalty_corners_scored` | numeric | YES | Average penalty corners per game |
| `avg_penalty_strokes_scored` | numeric | YES | Average penalty strokes per game |
| `avg_green_cards` | numeric | YES | Average green cards per game |
| `avg_yellow_cards` | numeric | YES | Average yellow cards per game |
| `avg_red_cards` | numeric | YES | Average red cards per game |
| `total_goals_against` | bigint | YES | Goals against (team total) |
| `avg_goals_against` | numeric | YES | Average goals against per game |
| `first_match_date` | timestamp without time zone | YES | First match date |
| `last_match_date` | timestamp without time zone | YES | Last match date |
| `player_name` | text | YES | Player's full name |
| `player_image` | text | YES | Player's image URL |

### Get Entity Season Statistics

{% swagger method="get" path="/seasons/{seasonId}/entitystats" baseUrl="https://api.tms.hockey" summary="Get entity season statistics" %}
{% swagger-description %}
Retrieves comprehensive team/entity statistics for a specific season from the entity_season_statistics_frontend view.
{% endswagger-description %}

{% swagger-parameter in="path" name="seasonId" type="uuid" required="true" %}
Season identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Entity statistics with all 61 actual database fields" %}
```json
[
  {
    "entity_id": "uuid",
    "season_id": "uuid",
    "season_name_latin": "2024 Indoor League",
    "season_name_local": "2024 Indoor League", 
    "season_name": "2024 Indoor League",
    "season_start": "2024-01-15",
    "season_end": "2024-06-30",
    "team_code_latin": "LB",
    "team_code_local": "LB",
    "team_code": "LB",
    "team_name_latin": "Lightning Bolts",
    "team_name_local": "Lightning Bolts",
    "team_name": "Lightning Bolts",
    "team_logo_url": "https://example.com/logo.png",
    "organization_id": "org123"
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Actual Database Fields (61 total):** *Due to the extensive field list, showing first 15 fields above. The view contains comprehensive team statistics including goals, shots, possession, cards, and advanced analytics.*

**Complete Field List:**
- Basic info: `entity_id`, `season_id`, `season_name_latin`, `season_name_local`, `season_name`, `season_start`, `season_end`
- Team identity: `team_code_latin`, `team_code_local`, `team_code`, `team_name_latin`, `team_name_local`, `team_name`, `team_logo_url`
- Match stats: `matches_played`, `wins`, `losses`, `draws`
- Scoring: `total_goals_scored`, `total_goals_conceded`, `total_field_goals_scored`, `total_penalty_corners_scored`, etc.
- Advanced: `avg_possession`, `avg_shot_accuracy`, `total_time_in_possession`, `max_biggest_lead`, etc.

### Get Entity Fixture Statistics

{% swagger method="get" path="/seasons/{seasonId}/teams/{entityId}/stats" baseUrl="https://api.tms.hockey" summary="Get entity fixture statistics" %}
{% swagger-description %}
Retrieves detailed fixture-by-fixture statistics for a team from the entity_fixture_statistics_frontend view.
{% endswagger-description %}

{% swagger-parameter in="path" name="seasonId" type="uuid" required="true" %}
Season identifier
{% endswagger-parameter %}

{% swagger-parameter in="path" name="entityId" type="uuid" required="true" %}
Entity identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Entity fixture statistics with actual database fields (showing first 20 of 72 fields)" %}
```json
[
  {
    "entity_id": "uuid",
    "fixture_id": "uuid",
    "period_id": 1,
    "team_code": "LB",
    "team_name": "Lightning Bolts",
    "team_logo_url": "https://example.com/logo.png",
    "fixture_name": "Semi-Final",
    "fixture_date": "2024-05-15T19:00:00",
    "fixture_status": "completed",
    "season_id": "uuid",
    "season_name": "2024 Indoor League",
    "season_start": "2024-01-15",
    "season_end": "2024-06-30",
    "venue_id": "uuid",
    "organization_id": "org123",
    "period_name": "Full Time",
    "added": "2024-05-15T21:00:00Z",
    "updated": "2024-05-15T21:30:00Z",
    "assists": 3,
    "bad_passes": 12
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Actual Database Fields (72 total):** *Complete match-by-match statistics including all offensive, defensive, and disciplinary actions. Fields include: goals_scored, goals_conceded, shots, saves, passes, tackles, cards, possession, shot_accuracy, and opponent information.*

### Get Entity Statistics by ID

{% swagger method="get" path="/fixturestats/{entity_id}" baseUrl="https://api.tms.hockey" summary="Get entity statistics by ID" %}
{% swagger-description %}
Retrieves season statistics for a specific entity (same data as entity season statistics endpoint).
{% endswagger-description %}

{% swagger-parameter in="path" name="entity_id" type="uuid" required="true" %}
Entity identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Entity statistics (same as /seasons/{seasonId}/entitystats)" %}
```json
[
  {
    "entity_id": "uuid",
    "season_id": "uuid",
    "team_name": "Lightning Bolts",
    "matches_played": 12,
    "total_goals_scored": 28.5,
    "total_goals_conceded": 18.0,
    "wins": 8,
    "losses": 2,
    "draws": 2
  }
]
```
{% endswagger-response %}
{% endswagger %}

*Uses the same `entity_season_statistics_frontend` view as the season entitystats endpoint.*

---

## Fixtures & Matches

### Get Fixtures by Group

{% swagger method="get" path="/fixtures/{group}" baseUrl="https://api.tms.hockey" summary="Get fixtures by group (Secure)" %}
{% swagger-description %}
Retrieves fixtures filtered by entity group with security permissions using the get_fixtures_secure RPC function.
{% endswagger-description %}

{% swagger-parameter in="path" name="group" type="string" required="true" %}
Entity group code
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Secure fixtures (RPC function - schema varies based on permissions)" %}
```json
[
  {
    "fixture_id": "uuid",
    "home_team_name": "Lightning Bolts",
    "away_team_name": "Thunder Hawks",
    "start_time_local": "2024-05-15T19:00:00",
    "status": "completed"
  }
]
```
{% endswagger-response %}
{% endswagger %}

*Note: This endpoint uses an RPC function with security filtering. The exact response schema depends on user permissions.*

### Get All Fixtures (Admin Only)

{% swagger method="get" path="/fixtures/all" baseUrl="https://api.tms.hockey" summary="Get all fixtures (Admin only)" %}
{% swagger-description %}
Retrieves all fixtures (admin access required) using the get_fixtures_secure RPC function.
{% endswagger-description %}

{% swagger-response status="200: OK" description="All fixtures (admin access required)" %}
```json
[
  {
    "fixture_id": "uuid",
    "home_team_name": "Lightning Bolts",
    "away_team_name": "Thunder Hawks",
    "start_time_local": "2024-05-15T19:00:00",
    "status": "completed"
  }
]
```
{% endswagger-response %}
{% endswagger %}

### Get Fixtures by Entity Group ID

{% swagger method="get" path="/fixtures/group/{entity_group_id}" baseUrl="https://api.tms.hockey" summary="Get fixtures by entity group ID" %}
{% swagger-description %}
Retrieves fixtures where the entity group participates (home or away) from the fixture_details_view_v2 view.
{% endswagger-description %}

{% swagger-parameter in="path" name="entity_group_id" type="string" required="true" %}
Entity group identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Fixtures with actual database fields (showing first 15 of 40 fields)" %}
```json
[
  {
    "competition_id": "uuid",
    "season_id": "uuid",
    "fixture_id": "uuid",
    "fixture_number": 45,
    "status": "completed",
    "season_logo_url": "https://example.com/season.png",
    "season_start_date": "2024-01-15",
    "season_end_date": "2024-06-30",
    "season_status": "active",
    "home_team_entity_group_id": "group123",
    "home_team_entity_id": "uuid",
    "home_team_name": "Lightning Bolts",
    "home_team_group_code": "SENIOR_M",
    "away_team_entity_group_id": "group456",
    "away_team_entity_id": "uuid"
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Actual Database Fields (40 total):** *Complete fixture details including team information, scores, venue, timing, and metadata.*

### Get Fixtures by Entity ID

{% swagger method="get" path="/fixtures/entity/{entity_id}" baseUrl="https://api.tms.hockey" summary="Get fixtures by entity ID" %}
{% swagger-description %}
Retrieves fixtures where the specific entity participates (home or away) from the fixture_details_view_v2 view.
{% endswagger-description %}

{% swagger-parameter in="path" name="entity_id" type="uuid" required="true" %}
Entity identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Entity fixtures (same schema as fixtures by group)" %}
```json
[
  {
    "fixture_id": "uuid",
    "home_team_name": "Lightning Bolts",
    "away_team_name": "Thunder Hawks",
    "home_score": "3",
    "away_score": "2",
    "start_time_local": "2024-05-15T19:00:00",
    "venue_name": "Central Arena",
    "status": "completed"
  }
]
```
{% endswagger-response %}
{% endswagger %}

*Uses the same `fixture_details_view_v2` view as the fixtures by group endpoint.*

### Get Fixture Timeline

{% swagger method="get" path="/fixtures/timeline/{fixtureId}" baseUrl="https://api.tms.hockey" summary="Get fixture timeline events" %}
{% swagger-description %}
Retrieves real-time timeline events for a specific fixture from the fixture_timeline view.
{% endswagger-description %}

{% swagger-parameter in="path" name="fixtureId" type="uuid" required="true" %}
Fixture identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Match timeline with actual database fields (all 28 fields)" %}
```json
[
  {
    "event_id": "uuid",
    "fixture_id": "uuid",
    "organization_id": "org123",
    "event_time": "2024-05-15T19:15:30Z",
    "clock": "00:15:30",
    "period_id": 1,
    "sequence_number": 15,
    "event_type": "goal",
    "sub_type": "field_goal",
    "entity_id": "uuid",
    "person_id": "uuid",
    "play_id": "uuid",
    "scores": {
      "home": 1,
      "away": 0
    },
    "success": true,
    "status": "confirmed",
    "official_id": "uuid",
    "timestamp": "2024-05-15T19:15:35Z",
    "entity_name": "Lightning Bolts",
    "entity_code": "LB",
    "person_name": "John Smith",
    "entity_image_url": "https://example.com/team.png",
    "person_image_url": "https://example.com/player.jpg",
    "discipline": "Hockey",
    "options": {
      "assist_player": "uuid"
    },
    "period_name": "First Period",
    "clock_display": "15:30",
    "match_clock_seconds": 930,
    "match_clock_display": "15:30"
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Actual Database Fields (28 total):**

| Field | Type | Nullable | Description |
| --- | --- | --- | --- |
| `event_id` | uuid | YES | Event identifier |
| `fixture_id` | uuid | YES | Fixture identifier |
| `organization_id` | character varying | YES | Organization ID |
| `event_time` | timestamp with time zone | YES | Event timestamp |
| `clock` | interval | YES | Match clock time |
| `period_id` | integer | YES | Period identifier |
| `sequence_number` | integer | YES | Event sequence number |
| `event_type` | character varying | YES | Type of event |
| `sub_type` | character varying | YES | Event subtype |
| `entity_id` | uuid | YES | Entity involved |
| `person_id` | uuid | YES | Person involved |
| `play_id` | uuid | YES | Play identifier |
| `scores` | jsonb | YES | Score state JSON |
| `success` | boolean | YES | Event success status |
| `status` | character varying | YES | Event status |
| `official_id` | uuid | YES | Official who recorded |
| `timestamp` | timestamp with time zone | YES | Event timestamp |
| `entity_name` | character varying | YES | Entity name |
| `entity_code` | character varying | YES | Entity code |
| `person_name` | text | YES | Person name |
| `entity_image_url` | text | YES | Entity image URL |
| `person_image_url` | text | YES | Person image URL |
| `discipline` | character varying | YES | Sport discipline |
| `options` | jsonb | YES | Additional options JSON |
| `period_name` | text | YES | Period name |
| `clock_display` | text | YES | Clock display |
| `match_clock_seconds` | numeric | YES | Match time in seconds |
| `match_clock_display` | text | YES | Match clock display |

### Get Player Match Statistics

{% swagger method="get" path="/fixtures/players/{fixtureId}" baseUrl="https://api.tms.hockey" summary="Get player match statistics" %}
{% swagger-description %}
Retrieves detailed individual player statistics for a specific match from the player_fixture_statistics_matchcenter view.
{% endswagger-description %}

{% swagger-parameter in="path" name="fixtureId" type="uuid" required="true" %}
Fixture identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Player match statistics with actual database fields (showing first 20 of 59 fields)" %}
```json
[
  {
    "player_fixture_statistics_id": 123,
    "added": "2024-05-15T19:00:00",
    "updated": "2024-05-15T21:30:00",
    "bib": "15",
    "did_not_play_reason": null,
    "entity_id": "uuid",
    "fixture_id": "uuid",
    "is_player": true,
    "is_team_official": false,
    "organization_id": "org123",
    "participated": true,
    "person_id": "uuid",
    "position": "Forward",
    "starter": true,
    "assists": 1,
    "bad_passes": 3,
    "blocks": 2,
    "challenges_earned": 1,
    "challenges_scored": 0,
    "circle_penetrations": 4
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Actual Database Fields (59 total):** *Complete individual match statistics including goals, assists, shots, passes, tackles, saves, cards, and performance metrics.*

**Key Field Categories:**
- **Identity**: `player_fixture_statistics_id`, `person_id`, `entity_id`, `fixture_id`, `bib`, `position`
- **Participation**: `participated`, `starter`, `is_player`, `minutes`, `did_not_play_reason`
- **Scoring**: `goals_scored`, `assists`, `field_goals_scored`, `penalty_corners_scored`, `penalty_strokes_scored`
- **Shooting**: `shots`, `shots_on_target`, `shots_missed`, `shot_accuracy`
- **Passing**: `passes`, `bad_passes`, `pass_accuracy`
- **Defense**: `tackles`, `blocks`, `interceptions`, `saves`
- **Discipline**: `green_cards`, `yellow_cards`, `red_cards`
- **Performance**: `plus`, `minus`, `plus_minus`

### Get Fixture Event Details by Group

{% swagger method="get" path="/fixturedetails/{group}" baseUrl="https://api.tms.hockey" summary="Get fixture event log by group" %}
{% swagger-description %}
Retrieves fixture event log filtered by group using the get_fixture_event_log_secure RPC function.
{% endswagger-description %}

{% swagger-parameter in="path" name="group" type="string" required="true" %}
Entity group code
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Fixture event log (RPC function - schema varies)" %}
```json
[
  {
    "event_id": "uuid",
    "fixture_id": "uuid",
    "event_type": "goal",
    "event_time": "2024-05-15T19:15:30Z",
    "entity_name": "Lightning Bolts",
    "person_name": "John Smith"
  }
]
```
{% endswagger-response %}
{% endswagger %}

*Note: This endpoint uses an RPC function with security filtering.*

### Get All Fixture Event Details (Admin Only)

{% swagger method="get" path="/fixturedetails/all" baseUrl="https://api.tms.hockey" summary="Get all fixture event logs (Admin only)" %}
{% swagger-description %}
Retrieves all fixture event logs (admin access required) using the get_fixture_event_log_secure RPC function.
{% endswagger-description %}

{% swagger-response status="200: OK" description="All fixture event logs (admin access required)" %}
```json
[
  {
    "event_id": "uuid",
    "fixture_id": "uuid",
    "event_type": "goal",
    "event_time": "2024-05-15T19:15:30Z",
    "entity_name": "Lightning Bolts",
    "person_name": "John Smith"
  }
]
```
{% endswagger-response %}
{% endswagger %}

---

## Organizations & Entities

### Get All Organizations

{% swagger method="get" path="/organizations" baseUrl="https://api.tms.hockey" summary="Get all organizations" %}
{% swagger-description %}
Retrieves all organizations in the system from the v_organizations view.
{% endswagger-description %}

{% swagger-response status="200: OK" description="Organizations with actual database fields" %}
```json
[
  {
    "organization_id": "org123",
    "abbreviation_latin": "NHF",
    "abbreviation_local": "NHF",
    "added": "2020-01-01T10:00:00",
    "country_code": "USA",
    "default_locale": "en",
    "images": {
      "logo": "https://example.com/logo.png"
    },
    "name_latin": "National Hockey Federation",
    "name_local": "National Hockey Federation",
    "region_type": "national",
    "updated": "2024-01-15T14:30:00"
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Actual Database Fields (11 total):**

| Field | Type | Nullable | Description |
| --- | --- | --- | --- |
| `organization_id` | text | YES | Organization identifier |
| `abbreviation_latin` | text | YES | Abbreviation (Latin script) |
| `abbreviation_local` | text | YES | Abbreviation (Local script) |
| `added` | timestamp without time zone | YES | When organization was added |
| `country_code` | text | YES | Country code |
| `default_locale` | text | YES | Default locale/language |
| `images` | jsonb | YES | Organization images JSON |
| `name_latin` | text | YES | Organization name (Latin script) |
| `name_local` | text | YES | Organization name (Local script) |
| `region_type` | text | YES | Type of region (national/regional/local) |
| `updated` | timestamp without time zone | YES | Last update timestamp |

### Get All Entities

{% swagger method="get" path="/entities" baseUrl="https://api.tms.hockey" summary="Get all entities with groups" %}
{% swagger-description %}
Retrieves all entities (teams/clubs) with their associated groups from the v_entities_with_groups view.
{% endswagger-description %}

{% swagger-response status="200: OK" description="Entities with actual database fields (showing first 20 of 50 fields)" %}
```json
[
  {
    "entity_id": "uuid",
    "entity_added": "2020-01-01T10:00:00",
    "entity_additional_names": {
      "nickname": "The Bolts"
    },
    "age_group": "Senior",
    "entity_code_latin": "LB",
    "entity_code_local": "LB",
    "entity_colors": {
      "primary": "#1E90FF",
      "secondary": "#FFD700"
    },
    "entity_default_venue_id": "venue123",
    "discipline": "Hockey",
    "entity_group_id": "group456",
    "entity_external_id": "ext789",
    "gender": "M",
    "grade": "A",
    "historical_names": {
      "previous": ["Old Team Name"]
    },
    "logo_url": "https://example.com/logo.png",
    "entity_images": {
      "logo": "https://example.com/logo.png",
      "banner": "https://example.com/banner.png"
    },
    "website_url": "https://team.example.com",
    "instagram_url": "https://instagram.com/team",
    "twitter_url": "https://twitter.com/team"
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Actual Database Fields (50 total):**

| Field | Type | Nullable | Description |
| --- | --- | --- | --- |
| `entity_id` | uuid | YES | Entity identifier |
| `entity_added` | timestamp without time zone | YES | When entity was added |
| `entity_additional_names` | jsonb | YES | Additional names JSON |
| `age_group` | character varying | YES | Age group |
| `entity_code_latin` | character varying | YES | Entity code (Latin script) |
| `entity_code_local` | character varying | YES | Entity code (Local script) |
| `entity_colors` | jsonb | YES | Entity colors JSON |
| `entity_default_venue_id` | character varying | YES | Default venue ID |
| `discipline` | character varying | YES | Sport discipline |
| `entity_group_id` | character varying | YES | Entity group ID |
| `entity_external_id` | character varying | YES | External entity ID |
| `gender` | character varying | YES | Gender category |
| `grade` | character varying | YES | Competition grade |
| `historical_names` | jsonb | YES | Historical names JSON |
| `logo_url` | json | YES | Logo URL JSON |
| `entity_images` | jsonb | YES | Entity images JSON |
| `website_url` | text | YES | Website URL |
| `instagram_url` | text | YES | Instagram URL |
| `twitter_url` | text | YES | Twitter URL |
| `facebook_url` | text | YES | Facebook URL |
| `entity_social` | jsonb | YES | Social media JSON |
| `entity_international_reference` | character varying | YES | International reference |
| `entity_name_full_latin` | character varying | YES | Full name (Latin script) |
| `entity_name_full_local` | character varying | YES | Full name (Local script) |
| `entity_organization` | jsonb | YES | Organization JSON |
| `organization_id` | character varying | YES | Organization ID |
| `representing` | character varying | YES | What entity represents |
| `standard` | character varying | YES | Entity standard |
| `entity_status` | character varying | YES | Entity status |
| `entity_updated` | timestamp without time zone | YES | Last entity update |
| `entity_links` | jsonb | YES | Entity links JSON |
| `entity_meta` | jsonb | YES | Entity metadata JSON |
| `group_id` | integer | YES | Group ID |
| `group_added` | timestamp without time zone | YES | When group was added |
| `group_additional_names` | jsonb | YES | Group additional names |
| `group_address` | jsonb | YES | Group address JSON |
| `group_code_latin` | character varying | YES | Group code (Latin) |
| `group_code_local` | character varying | YES | Group code (Local) |
| `group_colors` | jsonb | YES | Group colors JSON |
| `group_default_venue_id` | character varying | YES | Group default venue |
| `group_external_id` | character varying | YES | Group external ID |
| `group_images` | jsonb | YES | Group images JSON |
| `group_international_reference` | character varying | YES | Group international reference |
| `group_name_full_latin` | character varying | YES | Group full name (Latin) |
| `group_name_full_local` | character varying | YES | Group full name (Local) |
| `group_organization` | jsonb | YES | Group organization JSON |
| `group_status` | character varying | YES | Group status |
| `group_updated` | timestamp without time zone | YES | Last group update |
| `group_links` | jsonb | YES | Group links JSON |
| `group_meta` | jsonb | YES | Group metadata JSON |

---

## Public Widget Endpoints

These endpoints are accessible without authentication for widget integration.

### Get Season Officials (Public)

{% swagger method="get" path="/public/seasons/{seasonId}/officials" baseUrl="https://api.tms.hockey" summary="Get season officials (Public)" %}
{% swagger-description %}
Public endpoint for getting season officials. Uses the same v_season_officials view as the protected endpoint.
{% endswagger-description %}

{% swagger-parameter in="path" name="seasonId" type="uuid" required="true" %}
Season identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Season officials (same schema as protected endpoint)" %}
```json
[
  {
    "season_id": "uuid",
    "person_id": "uuid",
    "role": "Referee",
    "person_name": "John Official",
    "role_status": "active"
  }
]
```
{% endswagger-response %}
{% endswagger %}

**CORS:** Allows all origins (`*`) - Perfect for embedding in websites

### Get Current Fixture Clocks (Public)

{% swagger method="get" path="/clock/{fixtureId}" baseUrl="https://api.tms.hockey" summary="Get fixture clocks (Public)" %}
{% swagger-description %}
Public endpoint for getting current clock states for a fixture from the v_fixture_current_clocks view.
{% endswagger-description %}

{% swagger-parameter in="path" name="fixtureId" type="uuid" required="true" %}
Fixture identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Current clock states with actual database fields" %}
```json
{
  "fixture_id": "uuid",
  "server_time": "2024-05-15T19:30:00Z",
  "clocks": [
    {
      "type": "main",
      "action": "running", 
      "is_running": true,
      "current_display": "25:30",
      "current_seconds": 1530,
      "period_id": "uuid",
      "last_update_time": "2024-05-15T19:30:00Z"
    }
  ],
  "has_live_data": true
}
```
{% endswagger-response %}
{% endswagger %}

**CORS:** Allows all origins (`*`) - Perfect for embedding in websites

**Actual Database Fields (15 total from v_fixture_current_clocks):**
- Clock state: `clock_type`, `clock_action`, `is_running`
- Timing: `current_display`, `current_seconds`, `period_id`, `last_update_time`
- Context: `fixture_id`, `organization_id`, `season_id`

---

## Error Responses

All endpoints return consistent error responses:

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

## Hockey Terminology

| Term | Description |
| --- | --- |
| **Challenges** | Hockey-specific scoring opportunities |
| **Field Goals** | Goals scored during regular play (not penalty situations) |
| **Penalty Corners** | Set pieces awarded for defensive infractions |
| **Penalty Strokes** | Direct scoring opportunities (similar to penalty kicks) |
| **Circle Penetrations** | Entries into the scoring circle |
| **Long Corners** | Corner kicks awarded for certain infractions |
| **Shootout** | Penalty stroke competition to decide tied matches |
| **Bib** | Jersey/uniform number |
| **Plus/Minus** | Player rating system (+/- when on field during goals) |

---

## Technical Notes

1. **Database Views**: Most endpoints use PostgreSQL views with exact field names as documented
2. **RPC Functions**: Some endpoints use stored procedures (`get_fixtures_secure`, `get_fixture_event_log_secure`, `get_player_stats_by_person_id`) with dynamic schemas
3. **UUID Fields**: Primary keys use UUID format throughout
4. **JSON Fields**: Extensive use of JSONB for metadata, configurations, and flexible data
5. **Timestamps**: Mix of `timestamp with time zone` and `timestamp without time zone` depending on field
6. **Multilingual Support**: Many fields have `_latin` and `_local` variants for different scripts
7. **Security**: Group-based access control implemented in RPC functions
8. **Real-time Data**: Timeline and clock endpoints provide live match data

---

## Query Parameters

Most `GET` endpoints support additional query parameters for:
- **Filtering**: Use PostgREST query syntax (e.g., `?season_id=eq.uuid`)
- **Sorting**: Use `order` parameter (e.g., `?order=standing_position.asc`)
- **Limiting**: Use `limit` parameter (e.g., `?limit=10`)
- **Field Selection**: Use `select` parameter (e.g., `?select=team_name,team_code`)

---

*Complete API documentation with all 30 endpoints and exact database field names from your TMS Hockey system.*
