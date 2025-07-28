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
Retrieves all seasons with their associated competition details, registration status, and metadata.
{% endswagger-description %}

{% swagger-response status="200: OK" description="Successful response" %}
```json
[
  {
    "season_id": "uuid",
    "season_name": "2024 Indoor League",
    "season_start_date": "2024-01-15",
    "season_end_date": "2024-06-30",
    "season_status": "active",
    "competition_name": "National Indoor Championship",
    "entity_group_name": "Senior Men",
    "is_active": true,
    "current_teams": 12,
    "max_teams": 16
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Response Fields (36 total):**

| Field | Type | Description |
| --- | --- | --- |
| `season_id` | uuid | Unique season identifier |
| `season_name` | string | Display name of the season |
| `season_start_date` | date | Season start date |
| `season_end_date` | date | Season end date |
| `season_status` | string | Current status (active, completed, upcoming) |
| `competition_id` | uuid | Associated competition ID |
| `competition_name` | string | Competition name |
| `competition_type` | string | Type of competition |
| `age_group` | string | Age group category |
| `entity_group_id` | uuid | Entity group identifier |
| `entity_group_code` | string | Entity group code |
| `registration_open` | boolean | Registration status |
| `max_teams` | integer | Maximum teams allowed |
| `current_teams` | integer | Current number of teams |
| `is_active` | boolean | Whether season is active |
| `is_current` | boolean | Whether season is current |

### Get Teams in Season

{% swagger method="get" path="/seasons/{seasonId}/teams" baseUrl="https://api.tms.hockey" summary="Get all teams in a season" %}
{% swagger-description %}
Retrieves all teams participating in a specific season with their registration details.
{% endswagger-description %}

{% swagger-parameter in="path" name="seasonId" type="uuid" required="true" %}
Season identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Teams in the season" %}
```json
[
  {
    "team_entity_id": "uuid",
    "team_name": "Lightning Bolts",
    "team_code": "LB",
    "team_logo_url": "https://example.com/logo.png",
    "entity_group_name": "Senior Men",
    "registration_date": "2024-01-10T10:00:00",
    "team_status": "active"
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Response Fields (19 total):**

| Field | Type | Description |
| --- | --- | --- |
| `season_id` | uuid | Season identifier |
| `team_entity_id` | uuid | Team entity identifier |
| `team_name` | string | Team name |
| `team_short_name` | string | Team short name |
| `team_code` | string | Team code |
| `team_logo_url` | string | Team logo URL |
| `entity_group_id` | uuid | Entity group ID |
| `registration_date` | timestamp | Team registration date |
| `team_status` | string | Team status in season |
| `contact_person` | string | Contact person |
| `contact_email` | string | Contact email |
| `home_venue_id` | uuid | Home venue ID |
| `founded_year` | integer | Year team was founded |

### Get Team Roster

{% swagger method="get" path="/seasons/{seasonId}/teams/{teamId}/roster" baseUrl="https://api.tms.hockey" summary="Get team roster" %}
{% swagger-description %}
Retrieves player roster for a specific team in a season.
{% endswagger-description %}

{% swagger-parameter in="path" name="seasonId" type="uuid" required="true" %}
Season identifier
{% endswagger-parameter %}

{% swagger-parameter in="path" name="teamId" type="uuid" required="true" %}
Team entity identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Team roster" %}
```json
[
  {
    "person_id": "uuid",
    "first_name": "John",
    "last_name": "Smith",
    "jersey_number": 15,
    "position": "Forward",
    "is_captain": false,
    "player_status": "active"
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Response Fields (13 total):**

| Field | Type | Description |
| --- | --- | --- |
| `person_id` | uuid | Player person identifier |
| `first_name` | string | Player's first name |
| `last_name` | string | Player's last name |
| `jersey_number` | integer | Player's jersey number |
| `position` | string | Player's position |
| `player_status` | string | Player status |
| `birth_date` | date | Player's birth date |
| `is_captain` | boolean | Whether player is captain |
| `is_alternate_captain` | boolean | Whether player is alternate captain |
| `height` | integer | Player height (cm) |

### Get Season Standings

{% swagger method="get" path="/seasons/{seasonId}/standings" baseUrl="https://api.tms.hockey" summary="Get season standings" %}
{% swagger-description %}
Retrieves current standings for a season ordered by position.
{% endswagger-description %}

{% swagger-parameter in="path" name="seasonId" type="uuid" required="true" %}
Season identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Season standings" %}
```json
[
  {
    "team_entity_id": "uuid",
    "team_name": "Lightning Bolts",
    "standing_position": 1,
    "games_played": 10,
    "wins": 8,
    "losses": 1,
    "draws": 1,
    "points": 25,
    "goals_for": 32,
    "goals_against": 15,
    "goal_difference": 17
  }
]
```
{% endswagger-response %}
{% endswagger %}

### Get Season Officials

{% swagger method="get" path="/seasons/{seasonId}/officials" baseUrl="https://api.tms.hockey" summary="Get season officials" %}
{% swagger-description %}
Retrieves officials assigned to a season with their certification details.
{% endswagger-description %}

{% swagger-parameter in="path" name="seasonId" type="uuid" required="true" %}
Season identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Season officials" %}
```json
[
  {
    "person_id": "uuid",
    "first_name": "Jane",
    "last_name": "Doe",
    "official_type": "Referee",
    "certification_level": "Level 3",
    "certification_expiry": "2024-12-31",
    "status": "active"
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Response Fields (33 total):**

| Field | Type | Description |
| --- | --- | --- |
| `person_id` | uuid | Official's person identifier |
| `first_name` | string | Official's first name |
| `last_name` | string | Official's last name |
| `official_type` | string | Type of official |
| `certification_level` | string | Certification level |
| `certification_expiry` | date | Certification expiry date |
| `status` | string | Official status |
| `contact_email` | string | Contact email |
| `experience_years` | integer | Years of experience |
| `games_officiated` | integer | Total games officiated |

---

## Players & Personnel

### Get Player Information

{% swagger method="get" path="/persons/{person_id}" baseUrl="https://api.tms.hockey" summary="Get player statistics" %}
{% swagger-description %}
Retrieves detailed player statistics and information across all seasons.
{% endswagger-description %}

{% swagger-parameter in="path" name="person_id" type="uuid" required="true" %}
Player's person identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Player information and statistics" %}
```json
{
  "person_id": "uuid",
  "first_name": "John",
  "last_name": "Smith",
  "total_seasons": 3,
  "total_goals": 45,
  "total_assists": 32,
  "career_statistics": {...}
}
```
{% endswagger-response %}
{% endswagger %}

### Get Person Roles

{% swagger method="get" path="/person_roles/{person_id}" baseUrl="https://api.tms.hockey" summary="Get person roles" %}
{% swagger-description %}
Retrieves all roles a person has across different seasons and matches.
{% endswagger-description %}

{% swagger-parameter in="path" name="person_id" type="uuid" required="true" %}
Person identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Person roles across seasons" %}
```json
[
  {
    "person_id": "uuid",
    "season_name": "2024 Indoor League",
    "season_role": "player",
    "team_name": "Lightning Bolts",
    "match_role": "starter",
    "fixture_name": "Semi-Final"
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Response Fields (21 total):**

| Field | Type | Description |
| --- | --- | --- |
| `person_id` | uuid | Person identifier |
| `season_id` | uuid | Season identifier |
| `season_name` | string | Season name |
| `season_role` | string | Role in season (player, staff, etc.) |
| `season_role_subtype` | string | Specific role subtype |
| `fixture_name` | string | Fixture name (if applicable) |
| `match_date` | timestamp | Match date |
| `match_role` | string | Role in specific match |
| `venue_name` | string | Venue name |

### Get Player Active Seasons

{% swagger method="get" path="/activeseasons/players/{person_id}" baseUrl="https://api.tms.hockey" summary="Get player active seasons" %}
{% swagger-description %}
Retrieves all active seasons for a specific player with team and roster details.
{% endswagger-description %}

{% swagger-parameter in="path" name="person_id" type="uuid" required="true" %}
Player identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Player's active seasons" %}
```json
[
  {
    "person_id": "uuid",
    "season_name": "2024 Indoor League",
    "team_name": "Lightning Bolts",
    "team_code": "LB",
    "position": "Forward",
    "bib": "15",
    "season_status": "active"
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Response Fields (17 total):**

| Field | Type | Description |
| --- | --- | --- |
| `person_id` | uuid | Player identifier |
| `season_id` | uuid | Season identifier |
| `entity_id` | uuid | Team entity identifier |
| `bib` | string | Player's jersey number |
| `position` | string | Player's position |
| `roster_status` | string | Player's roster status |
| `team_name` | string | Team name |
| `team_code` | string | Team code |
| `season_name` | string | Season name |
| `discipline` | string | Sport discipline |
| `gender` | string | Gender category |
| `age_group` | string | Age group |

---

## Fixtures & Matches

### Get Fixtures by Group

{% swagger method="get" path="/fixtures/{group}" baseUrl="https://api.tms.hockey" summary="Get fixtures by group" %}
{% swagger-description %}
Retrieves fixtures filtered by entity group with security permissions applied.
{% endswagger-description %}

{% swagger-parameter in="path" name="group" type="string" required="true" %}
Entity group code
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Fixtures for the group" %}
```json
[
  {
    "fixture_id": "uuid",
    "fixture_name": "Semi-Final",
    "home_team_name": "Lightning Bolts",
    "away_team_name": "Thunder Hawks",
    "home_score": "3",
    "away_score": "2",
    "start_time_local": "2024-03-15T19:00:00",
    "venue_name": "Central Arena",
    "status": "completed"
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Response Fields (40 total):**

| Field | Type | Description |
| --- | --- | --- |
| `fixture_id` | uuid | Fixture identifier |
| `fixture_number` | integer | Fixture number in season |
| `status` | string | Fixture status |
| `home_team_entity_id` | uuid | Home team entity ID |
| `home_team_name` | string | Home team name |
| `away_team_entity_id` | uuid | Away team entity ID |
| `away_team_name` | string | Away team name |
| `home_score` | string | Home team score |
| `away_score` | string | Away team score |
| `start_time_local` | timestamp | Match start time (local) |
| `start_time_utc` | timestamp | Match start time (UTC) |
| `venue_name` | string | Venue name |
| `season_name` | string | Season name |
| `competition_name` | string | Competition name |

### Get Fixture Timeline

{% swagger method="get" path="/fixtures/timeline/{fixtureId}" baseUrl="https://api.tms.hockey" summary="Get fixture timeline" %}
{% swagger-description %}
Retrieves real-time timeline events for a specific fixture including goals, cards, and other match events.
{% endswagger-description %}

{% swagger-parameter in="path" name="fixtureId" type="uuid" required="true" %}
Fixture identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Match timeline events" %}
```json
[
  {
    "event_id": "uuid",
    "event_type": "goal",
    "sub_type": "field_goal",
    "event_time": "2024-03-15T19:15:30Z",
    "clock_display": "15:30",
    "entity_name": "Lightning Bolts",
    "person_name": "John Smith",
    "success": true,
    "scores": {
      "home": 1,
      "away": 0
    }
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Response Fields (28 total):**

| Field | Type | Description |
| --- | --- | --- |
| `event_id` | uuid | Event identifier |
| `fixture_id` | uuid | Fixture identifier |
| `event_time` | timestamp | Event timestamp |
| `clock` | interval | Match clock time |
| `period_id` | integer | Period identifier |
| `event_type` | string | Type of event (goal, card, etc.) |
| `sub_type` | string | Event subtype |
| `entity_name` | string | Entity involved |
| `person_name` | string | Person involved |
| `success` | boolean | Whether event was successful |
| `scores` | json | Score state after event |
| `clock_display` | string | Human-readable clock time |

---

## Statistics

### Get Player Season Statistics

{% swagger method="get" path="/seasons/{seasonId}/playerstats" baseUrl="https://api.tms.hockey" summary="Get player season statistics" %}
{% swagger-description %}
Retrieves comprehensive player statistics for a specific season including goals, assists, cards, and performance metrics.
{% endswagger-description %}

{% swagger-parameter in="path" name="seasonId" type="uuid" required="true" %}
Season identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Player statistics for the season" %}
```json
[
  {
    "person_id": "uuid",
    "player_name": "John Smith",
    "team_name": "Lightning Bolts",
    "matches_played": 12,
    "total_goals_scored": 8,
    "total_assists": 5,
    "total_field_goals_scored": 6,
    "total_penalty_corners_scored": 2,
    "avg_goals_scored": 0.67,
    "total_yellow_cards": 1,
    "total_red_cards": 0
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Response Fields (39 total):**

| Field | Type | Description |
| --- | --- | --- |
| `person_id` | uuid | Player's unique identifier |
| `player_name` | string | Player's full name |
| `team_name` | string | Team name |
| `matches_played` | integer | Total matches played |
| `total_goals_scored` | integer | Total goals scored |
| `total_field_goals_scored` | integer | Field goals scored |
| `total_penalty_corners_scored` | integer | Penalty corners scored |
| `total_penalty_strokes_scored` | integer | Penalty strokes scored |
| `total_assists` | integer | Total assists |
| `total_green_cards` | integer | Green cards received |
| `total_yellow_cards` | integer | Yellow cards received |
| `total_red_cards` | integer | Red cards received |
| `avg_goals_scored` | decimal | Average goals per game |
| `wins` | integer | Team wins when player participated |
| `losses` | integer | Team losses when player participated |
| `draws` | integer | Team draws when player participated |

### Get Entity Season Statistics

{% swagger method="get" path="/seasons/{seasonId}/entitystats" baseUrl="https://api.tms.hockey" summary="Get entity season statistics" %}
{% swagger-description %}
Retrieves comprehensive team/entity statistics for a specific season including offensive, defensive, and possession metrics.
{% endswagger-description %}

{% swagger-parameter in="path" name="seasonId" type="uuid" required="true" %}
Season identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Entity statistics for the season" %}
```json
[
  {
    "entity_id": "uuid",
    "team_name": "Lightning Bolts",
    "matches_played": 12,
    "total_goals_scored": 28,
    "total_goals_conceded": 15,
    "total_shots": 156,
    "total_shots_on_target": 89,
    "avg_possession": 58.5,
    "wins": 8,
    "losses": 2,
    "draws": 2
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Response Fields (61 total):**

| Field | Type | Description |
| --- | --- | --- |
| `entity_id` | uuid | Entity identifier |
| `team_name` | string | Team name |
| `matches_played` | integer | Total matches played |
| `total_goals_scored` | decimal | Total goals scored |
| `total_goals_conceded` | decimal | Total goals conceded |
| `total_shots` | decimal | Total shots |
| `total_shots_on_target` | decimal | Total shots on target |
| `total_saves` | decimal | Total saves |
| `total_passes` | decimal | Total passes |
| `avg_possession` | decimal | Average possession percentage |
| `avg_shot_accuracy` | decimal | Average shot accuracy |
| `wins` | integer | Total wins |
| `losses` | integer | Total losses |
| `draws` | integer | Total draws |
| `max_biggest_lead` | integer | Biggest lead achieved |

### Get Player Match Statistics

{% swagger method="get" path="/fixtures/players/{fixtureId}" baseUrl="https://api.tms.hockey" summary="Get player match statistics" %}
{% swagger-description %}
Retrieves detailed individual player statistics for a specific match.
{% endswagger-description %}

{% swagger-parameter in="path" name="fixtureId" type="uuid" required="true" %}
Fixture identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Player statistics for the match" %}
```json
[
  {
    "person_id": "uuid",
    "player_name": "John Smith",
    "team_name": "Lightning Bolts",
    "bib": "15",
    "position": "Forward",
    "participated": true,
    "starter": true,
    "minutes": "58:30",
    "goals_scored": 2,
    "assists": 1,
    "shots": 5,
    "shots_on_target": 3,
    "yellow_cards": 0
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Response Fields (59 total):**

| Field | Type | Description |
| --- | --- | --- |
| `person_id` | uuid | Player ID |
| `player_name` | string | Player's full name |
| `team_name` | string | Team name |
| `bib` | string | Jersey number |
| `position` | string | Player's position |
| `participated` | boolean | Whether player participated |
| `starter` | boolean | Whether player was a starter |
| `minutes` | interval | Minutes played |
| `goals_scored` | integer | Goals scored in match |
| `assists` | integer | Assists in match |
| `shots` | integer | Total shots |
| `shots_on_target` | integer | Shots on target |
| `passes` | integer | Total passes |
| `tackles` | integer | Tackles made |
| `saves` | integer | Saves (goalkeepers) |
| `plus_minus` | integer | Plus/minus rating |

---

## Public Widget Endpoints

These endpoints are accessible without authentication for widget integration.

### Get Current Fixture Clocks

{% swagger method="get" path="/clock/{fixtureId}" baseUrl="https://api.tms.hockey" summary="Get fixture clocks (Public)" %}
{% swagger-description %}
Public endpoint for getting current clock states for a fixture. Perfect for live score widgets.
{% endswagger-description %}

{% swagger-parameter in="path" name="fixtureId" type="uuid" required="true" %}
Fixture identifier
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Current clock states" %}
```json
{
  "fixture_id": "uuid",
  "server_time": "2024-03-15T19:30:00Z",
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

## Organizations & Entities

### Get All Organizations

{% swagger method="get" path="/organizations" baseUrl="https://api.tms.hockey" summary="Get all organizations" %}
{% swagger-description %}
Retrieves all organizations in the system with their contact details and metadata.
{% endswagger-description %}

{% swagger-response status="200: OK" description="List of organizations" %}
```json
[
  {
    "organization_id": "uuid",
    "organization_name": "National Hockey Federation",
    "organization_short_name": "NHF",
    "organization_type": "federation",
    "contact_email": "info@nhf.com",
    "website": "https://nhf.com",
    "status": "active"
  }
]
```
{% endswagger-response %}
{% endswagger %}

### Get All Entities

{% swagger method="get" path="/entities" baseUrl="https://api.tms.hockey" summary="Get all entities" %}
{% swagger-description %}
Retrieves all entities (teams, clubs) with their associated groups and detailed information.
{% endswagger-description %}

{% swagger-response status="200: OK" description="List of entities with groups" %}
```json
[
  {
    "entity_id": "uuid",
    "entity_name_en": "Lightning Bolts",
    "entity_code_latin": "LB",
    "entity_type": "team",
    "entity_status": "active",
    "entity_group_name": "Senior Men",
    "founded_year": 2015,
    "entity_logo_url": "https://example.com/logo.png"
  }
]
```
{% endswagger-response %}
{% endswagger %}

**Response Fields:** 50 total fields with comprehensive entity and group information

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
