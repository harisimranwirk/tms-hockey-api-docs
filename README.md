# TMS Hockey API Documentation

**Base URL:** `https://api.tms.hockey`

---

## Authentication

The API supports multiple authentication methods for different use cases.

### Web Session Authentication

Used by web applications (tms.hockey, livescores.hockey)

{% tabs %}
{% tab title="Request" %}
```http
POST /login
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

Used by mobile applications

{% tabs %}
{% tab title="Request" %}
```http
POST /auth/mobile
Content-Type: application/json

{
  "clientId": "tms-hockey-mobile",
  "clientSecret": "your-client-secret"
}
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
Retrieves all seasons with their associated competition details, registration status, and metadata from the v_seasons_with_competition view.
{% endswagger-description %}

{% swagger-response status="200: OK" description="Successful response with actual database fields" %}
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
    "name_latin": "2024 Indoor League",
    "name_local": "2024 Indoor League",
    "organization_id": "org123",
    "status": "active",
    "year": 2024,
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
| `id` | integer | YES | Internal ID |
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
| `name_latin` | character varying | YES | Season name (Latin) |
| `name_local` | character varying | YES | Season name (Local) |
| `name_short_latin` | character varying | YES | Short name (Latin) |
| `name_short_local` | character varying | YES | Short name (Local) |
| `organization_id` | character varying | YES | Organization identifier |
| `promotion_relegation_rules` | jsonb | YES | Promotion/relegation rules |
| `representation` | character varying | YES | Team representation type |
| `roster_configuration` | jsonb | YES | Roster configuration |
| `season_type` | character varying | YES | Season type |
| `standard` | character varying | YES | Competition standard |
| `standing_configuration_id` | uuid | YES | Standing configuration ID |
| `status` | character varying | YES | Season status |
| `video_production` | character varying | YES | Video production level |
| `year` | integer | YES | Season year |
| `links` | jsonb | YES | Related links |
| `meta` | jsonb | YES | Metadata |
| `image_url` | text | YES | Season image URL |
| `total_matches` | bigint | YES | Total matches in season |
| `matches_completed` | bigint | YES | Completed matches |

### Get Teams in Season

{% swagger method="get" path="/seasons/{seasonId}/teams" baseUrl="https://api.tms.hockey" summary="Get all teams in a season" %}
{% swagger-description %}
Retrieves all teams participating in a specific season from the v_season_teams_with_entities view.
{% endswagger-description %}

{% swagger-parameter in="path" name="seasonId" type="uuid" required="true" %}
Season identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Teams in the season with actual database fields" %}
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
    "team_name_latin": "Lightning Bolts",
    "team_name_local": "Lightning Bolts",
    "team_code_latin": "LB",
    "team_code_local": "LB",
    "team_representing": "City Name",
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
| `roster_status` | text | YES | Roster status |
| `season_team_status` | text | YES | Team status in season |
| `season_team_added` | timestamp with time zone | YES | When team was added |
| `season_team_updated` | timestamp with time zone | YES | Last team update |
| `team_name_latin` | character varying | YES | Team name (Latin) |
| `team_name_local` | character varying | YES | Team name (Local) |
| `team_code_latin` | character varying | YES | Team code (Latin) |
| `team_code_local` | character varying | YES | Team code (Local) |
| `team_representing` | character varying | YES | What team represents |
| `logo_url` | text | YES | Team logo URL |
| `final_standing` | integer | YES | Final season standing |
| `final_points` | numeric | YES | Final points earned |
| `final_prize_money` | numeric | YES | Prize money earned |

### Get Team Roster

{% swagger method="get" path="/seasons/{seasonId}/teams/{teamId}/roster" baseUrl="https://api.tms.hockey" summary="Get team roster" %}
{% swagger-description %}
Retrieves player roster for a specific team in a season from the v_season_team_roster view.
{% endswagger-description %}

{% swagger-parameter in="path" name="seasonId" type="uuid" required="true" %}
Season identifier
{% endswagger-parameter %}

{% swagger-parameter in="path" name="teamId" type="uuid" required="true" %}
Team entity identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Team roster with actual database fields" %}
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
| `position` | text | YES | Player's position |
| `roster_status` | text | YES | Roster status |
| `roster_added` | timestamp with time zone | YES | When added to roster |
| `roster_updated` | timestamp with time zone | YES | Last roster update |
| `person_name` | text | YES | Player's full name |
| `person_image_url` | text | YES | Player's image URL |
| `person_dob` | date | YES | Player's date of birth |
| `nationality` | text | YES | Player's nationality |
| `organization_id` | text | YES | Organization identifier |

---

## Players & Personnel

### Get Person Roles

{% swagger method="get" path="/person_roles/{person_id}" baseUrl="https://api.tms.hockey" summary="Get person roles" %}
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
| `season_role` | text | YES | Role in season |
| `season_role_subtype` | text | YES | Specific role subtype |
| `fixture_id` | uuid | YES | Fixture identifier |
| `fixture_name` | text | YES | Fixture name |
| `match_date` | timestamp without time zone | YES | Match date |
| `home_team` | text | YES | Home team name |
| `away_team` | text | YES | Away team name |
| `home_score` | text | YES | Home team score |
| `away_score` | text | YES | Away team score |
| `home_secondary_score` | text | YES | Home secondary score |
| `away_secondary_score` | text | YES | Away secondary score |
| `match_role` | text | YES | Role in specific match |
| `venue_name` | text | YES | Venue name |
| `source` | text | YES | Data source |
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
| `roster_status` | text | YES | Roster status |
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

## Fixtures & Matches

### Get Fixtures by Group

{% swagger method="get" path="/fixtures/{group}" baseUrl="https://api.tms.hockey" summary="Get fixtures by group" %}
{% swagger-description %}
Retrieves fixtures filtered by entity group from the fixture_details_view_v2 view.
{% endswagger-description %}

{% swagger-parameter in="path" name="group" type="string" required="true" %}
Entity group code
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Fixtures with actual database fields" %}
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
    "away_team_entity_id": "uuid",
    "away_team_name": "Thunder Hawks",
    "away_team_group_code": "SENIOR_M",
    "home_score": "3",
    "home_secondary_score": "0",
    "away_score": "2",
    "away_secondary_score": "1",
    "season_name": "2024 Indoor League",
    "competition_name": "National Championship",
    "start_time_local": "2024-05-15T19:00:00",
    "start_time_utc": "2024-05-15T23:00:00",
    "venue_name": "Central Arena",
    "venue_country": "Canada",
    "venue_timezone": "America/Toronto",
    "home_team_flag_url": "https://example.com/flag1.png",
    "away_team_flag_url": "https://example.com/flag2.png",
    "home_team_code": "LB",
    "away_team_code": "TH",
    "season_age_group": "Senior",
    "season_discipline": "Hockey",
    "season_gender": "M",
    "last_score_synced": "2024-05-15T21:30:00",
    "organization_id": "org123",
    "fixture_name": "Semi-Final",
    "home_result_status": "win",
    "away_result_status": "loss"
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Actual Database Fields (40 total):**

| Field | Type | Nullable | Description |
| --- | --- | --- | --- |
| `competition_id` | uuid | YES | Competition identifier |
| `season_id` | uuid | YES | Season identifier |
| `fixture_id` | uuid | YES | Fixture identifier |
| `fixture_number` | integer | YES | Fixture number in season |
| `status` | character varying | YES | Fixture status |
| `season_logo_url` | text | YES | Season logo URL |
| `season_start_date` | date | YES | Season start date |
| `season_end_date` | date | YES | Season end date |
| `season_status` | character varying | YES | Season status |
| `home_team_entity_group_id` | character varying | YES | Home team group ID |
| `home_team_entity_id` | uuid | YES | Home team entity ID |
| `home_team_name` | character varying | YES | Home team name |
| `home_team_group_code` | character varying | YES | Home team group code |
| `away_team_entity_group_id` | character varying | YES | Away team group ID |
| `away_team_entity_id` | uuid | YES | Away team entity ID |
| `away_team_name` | character varying | YES | Away team name |
| `away_team_group_code` | character varying | YES | Away team group code |
| `home_score` | character varying | YES | Home team score |
| `home_secondary_score` | character varying | YES | Home secondary score |
| `away_score` | character varying | YES | Away team score |
| `away_secondary_score` | character varying | YES | Away secondary score |
| `season_name` | character varying | YES | Season name |
| `competition_name` | character varying | YES | Competition name |
| `start_time_local` | timestamp without time zone | YES | Local start time |
| `start_time_utc` | timestamp without time zone | YES | UTC start time |
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
| `last_score_synced` | timestamp without time zone | YES | Last score sync |
| `organization_id` | character varying | YES | Organization ID |
| `fixture_name` | character varying | YES | Fixture name |
| `home_result_status` | character varying | YES | Home result status |
| `away_result_status` | character varying | YES | Away result status |

### Get Fixture Timeline

{% swagger method="get" path="/fixtures/timeline/{fixtureId}" baseUrl="https://api.tms.hockey" summary="Get fixture timeline" %}
{% swagger-description %}
Retrieves real-time timeline events for a specific fixture from the fixture_timeline view.
{% endswagger-description %}

{% swagger-parameter in="path" name="fixtureId" type="uuid" required="true" %}
Fixture identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Match timeline with actual database fields" %}
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
| `scores` | jsonb | YES | Score state (JSON) |
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
| `options` | jsonb | YES | Additional options |
| `period_name` | text | YES | Period name |
| `clock_display` | text | YES | Clock display |
| `match_clock_seconds` | numeric | YES | Match time in seconds |
| `match_clock_display` | text | YES | Match clock display |

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

{% swagger-response status="200: OK" description="Player statistics with actual database fields" %}
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
| `total_goals_against` | bigint | YES | Goals against (team) |
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
    "organization_id": "org123",
    "matches_played": 12,
    "total_goals_scored": 28.5,
    "total_goals_conceded": 18.0,
    "total_field_goals_scored": 24.0,
    "total_penalty_corners_scored": 4.5,
    "total_penalty_strokes_scored": 0.0,
    "total_challenges_scored": 8.0,
    "total_goals_shootout": 2.0,
    "total_goals_conceded_shootout": 1.0,
    "total_penalty_strokes_shootout_scored": 3.0,
    "total_penalty_strokes_shootout_missed": 1.0,
    "total_saves": 89.0,
    "total_saves_shootout": 4.0,
    "total_blocks": 45.0,
    "total_tackles": 156.0,
    "total_interceptions": 78.0,
    "total_shots": 189.0,
    "total_shots_on_target": 98.0,
    "total_assists": 34.0,
    "total_passes": 2456.0,
    "total_bad_passes": 234.0,
    "avg_possession": 58.5,
    "avg_shot_accuracy": 51.9,
    "total_green_cards": 2.0,
    "total_yellow_cards": 8.0,
    "total_red_cards": 1.0,
    "total_penalty_corners_earned": 12.0,
    "total_penalty_strokes_earned": 3.0,
    "total_circle_penetrations": 67.0,
    "total_long_corners_earned": 23.0,
    "total_challenges_earned": 15.0,
    "avg_goals_scored": 2.38,
    "avg_goals_conceded": 1.50,
    "avg_shots": 15.75,
    "avg_shots_on_target": 8.17,
    "avg_saves": 7.42,
    "wins": 8,
    "losses": 2,
    "draws": 2,
    "total_minutes_played": "12:00:00",
    "total_time_in_possession": "06:58:30",
    "avg_time_in_lead": 42.5,
    "max_biggest_lead": 4,
    "max_biggest_scoring_run": 3,
    "first_match_date": "2024-01-20T15:00:00",
    "last_match_date": "2024-05-28T19:00:00"
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Actual Database Fields (61 total):**

| Field | Type | Nullable | Description |
| --- | --- | --- | --- |
| `entity_id` | uuid | YES | Entity identifier |
| `season_id` | uuid | YES | Season identifier |
| `season_name_latin` | character varying | YES | Season name (Latin) |
| `season_name_local` | character varying | YES | Season name (Local) |
| `season_name` | character varying | YES | Season name |
| `season_start` | date | YES | Season start date |
| `season_end` | date | YES | Season end date |
| `team_code_latin` | character varying | YES | Team code (Latin) |
| `team_code_local` | character varying | YES | Team code (Local) |
| `team_code` | character varying | YES | Team code |
| `team_name_latin` | character varying | YES | Team name (Latin) |
| `team_name_local` | character varying | YES | Team name (Local) |
| `team_name` | character varying | YES | Team name |
| `team_logo_url` | text | YES | Team logo URL |
| `organization_id` | text | YES | Organization ID |
| `matches_played` | bigint | YES | Total matches played |
| `total_goals_scored` | numeric | YES | Total goals scored |
| `total_goals_conceded` | numeric | YES | Total goals conceded |
| `total_field_goals_scored` | numeric | YES | Total field goals |
| `total_penalty_corners_scored` | numeric | YES | Penalty corners scored |
| `total_penalty_strokes_scored` | numeric | YES | Penalty strokes scored |
| `total_challenges_scored` | numeric | YES | Challenges scored |
| `total_goals_shootout` | numeric | YES | Shootout goals scored |
| `total_goals_conceded_shootout` | numeric | YES | Shootout goals conceded |
| `total_penalty_strokes_shootout_scored` | numeric | YES | Shootout penalty strokes scored |
| `total_penalty_strokes_shootout_missed` | numeric | YES | Shootout penalty strokes missed |
| `total_saves` | numeric | YES | Total saves |
| `total_saves_shootout` | numeric | YES | Shootout saves |
| `total_blocks` | numeric | YES | Total blocks |
| `total_tackles` | numeric | YES | Total tackles |
| `total_interceptions` | numeric | YES | Total interceptions |
| `total_shots` | numeric | YES | Total shots |
| `total_shots_on_target` | numeric | YES | Shots on target |
| `total_assists` | numeric | YES | Total assists |
| `total_passes` | numeric | YES | Total passes |
| `total_bad_passes` | numeric | YES | Total bad passes |
| `avg_possession` | numeric | YES | Average possession % |
| `avg_shot_accuracy` | numeric | YES | Average shot accuracy % |
| `total_green_cards` | numeric | YES | Total green cards |
| `total_yellow_cards` | numeric | YES | Total yellow cards |
| `total_red_cards` | numeric | YES | Total red cards |
| `total_penalty_corners_earned` | numeric | YES | Penalty corners earned |
| `total_penalty_strokes_earned` | numeric | YES | Penalty strokes earned |
| `total_circle_penetrations` | numeric | YES | Circle penetrations |
| `total_long_corners_earned` | numeric | YES | Long corners earned |
| `total_challenges_earned` | numeric | YES | Challenges earned |
| `avg_goals_scored` | numeric | YES | Average goals per game |
| `avg_goals_conceded` | numeric | YES | Average goals conceded per game |
| `avg_shots` | numeric | YES | Average shots per game |
| `avg_shots_on_target` | numeric | YES | Average shots on target per game |
| `avg_saves` | numeric | YES | Average saves per game |
| `wins` | bigint | YES | Total wins |
| `losses` | bigint | YES | Total losses |
| `draws` | bigint | YES | Total draws |
| `total_minutes_played` | interval | YES | Total playing time |
| `total_time_in_possession` | interval | YES | Total possession time |
| `avg_time_in_lead` | numeric | YES | Average time in lead |
| `max_biggest_lead` | integer | YES | Biggest lead achieved |
| `max_biggest_scoring_run` | integer | YES | Biggest scoring run |
| `first_match_date` | timestamp without time zone | YES | First match date |
| `last_match_date` | timestamp without time zone | YES | Last match date |

---

## Public Widget Endpoints

### Get Current Fixture Clocks

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
      "period_id": "uuid"
    }
  ],
  "has_live_data": true
}
```
{% endswagger-response %}
{% endswagger %}

**CORS:** Allows all origins (`*`) - Perfect for embedding in websites

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
| **Field Goals** | Goals scored during regular play |
| **Penalty Corners** | Set pieces awarded for defensive infractions |
| **Penalty Strokes** | Direct scoring opportunities |
| **Circle Penetrations** | Entries into the scoring circle |
| **Long Corners** | Corner kicks awarded for certain infractions |
| **Shootout** | Penalty stroke competition to decide matches |
| **Bib** | Jersey/uniform number |
