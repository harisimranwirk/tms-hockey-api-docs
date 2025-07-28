# TMS Hockey API Documentation

**Base URL:** `https://api.tms.hockey`

---

## 📋 Table of Contents

- [🔐 Authentication](#-authentication)
- [🏒 Seasons](#-seasons)
- [👥 Players & Personnel](#-players--personnel)  
- [🏟️ Fixtures & Matches](#️-fixtures--matches)
- [🏢 Organizations & Entities](#-organizations--entities)
- [📊 Statistics](#-statistics)
- [🌐 Public Widget Endpoints](#-public-widget-endpoints)
- [❌ Error Responses](#-error-responses)
- [🏒 Hockey Terminology](#-hockey-terminology)

---

## 🔐 Authentication

The API supports multiple authentication methods for different use cases:

### Web Session Authentication
For web applications (tms.hockey, livescores.hockey)

```http
POST /login
POST /login-livescores
```

**Response:**
```json
{
  "ok": true
}
```
*Sets secure session cookie (`session_id`) valid for 4 hours*

### Mobile App Authentication
For mobile applications

```http
POST /auth/mobile
Content-Type: application/json

{
  "clientId": "tms-hockey-mobile",
  "clientSecret": "your-client-secret"
}
```

**Response:**
```json
{
  "accessToken": "hex-token",
  "refreshToken": "hex-token", 
  "expiresAt": 1234567890,
  "tokenType": "Bearer"
}
```

### Direct JWT Authentication
For API testing (Postman, etc.)

```http
Authorization: Bearer <jwt-token>
```

---

## 🏒 Seasons

### Get All Seasons
```http
GET /seasons
```

Get all seasons with competition information.

<details>
<summary><strong>Response Schema (36 fields)</strong></summary>

| Field | Type | Description |
|-------|------|-------------|
| `season_id` | uuid | Unique season identifier |
| `season_name` | string | Display name of the season |
| `season_start_date` | date | Season start date |
| `season_end_date` | date | Season end date |
| `season_status` | string | Current status of the season |
| `competition_id` | uuid | Associated competition ID |
| `competition_name` | string | Competition name |
| `competition_type` | string | Type of competition |
| `competition_gender` | string | Gender category |
| `age_group` | string | Age group category |
| `competition_level` | string | Competition level |
| `entity_group_id` | uuid | Entity group identifier |
| `entity_group_code` | string | Entity group code |
| `entity_group_name` | string | Entity group display name |
| `season_description` | text | Season description |
| `registration_open` | boolean | Registration status |
| `max_teams` | integer | Maximum teams allowed |
| `current_teams` | integer | Current number of teams |
| `is_active` | boolean | Whether season is active |
| `is_current` | boolean | Whether season is current |
| `season_phase` | string | Current season phase |
| *...plus additional metadata fields* |

</details>

### Get Teams in Season
```http
GET /seasons/{seasonId}/teams
```

Get all teams participating in a specific season.

<details>
<summary><strong>Response Schema (19 fields)</strong></summary>

| Field | Type | Description |
|-------|------|-------------|
| `season_id` | uuid | Season identifier |
| `team_entity_id` | uuid | Team entity identifier |
| `team_name` | string | Team name |
| `team_short_name` | string | Team short name |
| `team_code` | string | Team code |
| `team_logo_url` | string | Team logo URL |
| `entity_group_id` | uuid | Entity group ID |
| `entity_group_name` | string | Entity group name |
| `registration_date` | timestamp | Team registration date |
| `team_status` | string | Team status in season |
| `contact_person` | string | Contact person |
| `contact_email` | string | Contact email |
| `home_venue_id` | uuid | Home venue ID |
| `founded_year` | integer | Year team was founded |
| *...plus additional team details* |

</details>

### Get Team Roster
```http
GET /seasons/{seasonId}/teams/{teamId}/roster
```

Get player roster for a specific team in a season.

<details>
<summary><strong>Response Schema (13 fields)</strong></summary>

| Field | Type | Description |
|-------|------|-------------|
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
| *...plus additional player details* |

</details>

### Get Team Staff
```http
GET /seasons/{seasonId}/teams/{teamId}/staff
```

Get staff roster for a specific team in a season.

### Get Season Standings
```http
GET /seasons/{seasonId}/standings
```

Get current standings for a season.

### Get Season Officials
```http
GET /seasons/{seasonId}/officials
```

Get officials assigned to a season.

---

## 👥 Players & Personnel

### Get Player Information
```http
GET /persons/{person_id}
```

Get detailed player statistics and information.

### Get Person Roles
```http
GET /person_roles/{person_id}
```

Get all roles a person has across different seasons/teams.

<details>
<summary><strong>Response Schema (21 fields)</strong></summary>

| Field | Type | Description |
|-------|------|-------------|
| `person_id` | uuid | Person identifier |
| `season_id` | uuid | Season identifier |
| `season_name` | string | Season name |
| `season_role` | string | Role in season (player, staff, etc.) |
| `season_role_subtype` | string | Specific role subtype |
| `fixture_name` | string | Fixture name (if applicable) |
| `match_date` | timestamp | Match date |
| `home_team` | string | Home team name |
| `away_team` | string | Away team name |
| `match_role` | string | Role in specific match |
| `venue_name` | string | Venue name |
| *...plus additional role details* |

</details>

### Get Player Active Seasons
```http
GET /activeseasons/players/{person_id}
```

Get all active seasons for a specific player.

---

## 🏟️ Fixtures & Matches

### Get Fixtures by Group
```http
GET /fixtures/{group}
```

Get fixtures filtered by entity group with security permissions.

<details>
<summary><strong>Response Schema (40 fields)</strong></summary>

| Field | Type | Description |
|-------|------|-------------|
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
| `venue_country` | string | Venue country |
| `season_name` | string | Season name |
| `competition_name` | string | Competition name |
| *...plus additional fixture details* |

</details>

### Get All Fixtures (Admin Only)
```http
GET /fixtures/all
```

### Get Fixtures by Entity Group ID
```http
GET /fixtures/group/{entity_group_id}
```

### Get Fixtures by Entity ID  
```http
GET /fixtures/entity/{entity_id}
```

### Get Fixture Timeline
```http
GET /fixtures/timeline/{fixtureId}
```

Get timeline events for a specific fixture (real-time match data).

<details>
<summary><strong>Response Schema (28 fields)</strong></summary>

| Field | Type | Description |
|-------|------|-------------|
| `event_id` | uuid | Event identifier |
| `fixture_id` | uuid | Fixture identifier |
| `event_time` | timestamp | Event timestamp |
| `clock` | interval | Match clock time |
| `period_id` | integer | Period identifier |
| `event_type` | string | Type of event (goal, card, etc.) |
| `sub_type` | string | Event subtype |
| `entity_id` | uuid | Entity involved in event |
| `person_id` | uuid | Person involved in event |
| `scores` | json | Score state |
| `success` | boolean | Whether event was successful |
| `entity_name` | string | Entity name |
| `person_name` | string | Person's name |
| `clock_display` | string | Clock display format |
| *...plus additional event details* |

</details>

---

## 🏢 Organizations & Entities

### Get All Organizations
```http
GET /organizations
```

<details>
<summary><strong>Response Schema (11 fields)</strong></summary>

| Field | Type | Description |
|-------|------|-------------|
| `organization_id` | uuid | Organization identifier |
| `organization_name` | string | Organization name |
| `organization_short_name` | string | Short name |
| `organization_type` | string | Type of organization |
| `contact_email` | string | Contact email |
| `contact_phone` | string | Contact phone |
| `website` | string | Organization website |
| `address` | text | Physical address |
| `logo_url` | string | Logo URL |
| `status` | string | Organization status |
| `metadata` | json | Additional metadata |

</details>

### Get All Entities
```http
GET /entities
```

Get all entities with their associated groups (50 fields with comprehensive entity data).

---

## 📊 Statistics

### Get Player Season Statistics
```http
GET /seasons/{seasonId}/playerstats
```

Get comprehensive player statistics for a specific season.

<details>
<summary><strong>Response Schema (39 fields)</strong></summary>

| Field | Type | Description |
|-------|------|-------------|
| `person_id` | uuid | Player's unique identifier |
| `season_id` | uuid | Season identifier |
| `team_name` | string | Team name |
| `matches_played` | integer | Total matches played |
| `total_goals_scored` | integer | Total goals scored |
| `total_field_goals_scored` | integer | Total field goals scored |
| `total_penalty_corners_scored` | integer | Total penalty corners scored |
| `total_penalty_strokes_scored` | integer | Total penalty strokes scored |
| `total_assists` | integer | Total assists |
| `total_green_cards` | integer | Total green cards received |
| `total_yellow_cards` | integer | Total yellow cards received |
| `total_red_cards` | integer | Total red cards received |
| `wins` | integer | Team wins |
| `losses` | integer | Team losses |
| `draws` | integer | Team draws |
| `avg_goals_scored` | decimal | Average goals per game |
| `player_name` | string | Player's full name |
| `player_image` | string | Player's image URL |
| *...plus additional performance metrics* |

</details>

### Get Entity Season Statistics
```http
GET /seasons/{seasonId}/entitystats
```

Get comprehensive team/entity statistics for a specific season.

<details>
<summary><strong>Response Schema (61 fields)</strong></summary>

| Field | Type | Description |
|-------|------|-------------|
| `entity_id` | uuid | Entity identifier |
| `season_id` | uuid | Season identifier |
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
| `total_minutes_played` | interval | Total playing time |
| `max_biggest_lead` | integer | Biggest lead achieved |
| *...plus advanced team analytics* |

</details>

### Get Entity Fixture Statistics
```http
GET /seasons/{seasonId}/teams/{entityId}/stats
```

Get detailed fixture-by-fixture statistics for a team (72 fields with comprehensive match data).

### Get Player Match Statistics
```http
GET /fixtures/players/{fixtureId}
```

Get detailed player statistics for a specific match.

<details>
<summary><strong>Response Schema (59 fields)</strong></summary>

| Field | Type | Description |
|-------|------|-------------|
| `person_id` | uuid | Player ID |
| `fixture_id` | uuid | Fixture ID |
| `bib` | string | Player's jersey number |
| `position` | string | Player's position |
| `participated` | boolean | Whether player participated |
| `starter` | boolean | Whether player was a starter |
| `minutes` | interval | Minutes played |
| `goals_scored` | integer | Goals scored |
| `assists` | integer | Assists |
| `shots` | integer | Total shots |
| `shots_on_target` | integer | Shots on target |
| `saves` | integer | Saves (goalkeepers) |
| `passes` | integer | Total passes |
| `tackles` | integer | Tackles made |
| `green_cards` | integer | Green cards received |
| `yellow_cards` | integer | Yellow cards received |
| `red_cards` | integer | Red cards received |
| `plus_minus` | integer | Plus/minus rating |
| `player_name` | string | Player's full name |
| *...plus detailed match performance metrics* |

</details>

---

## 🌐 Public Widget Endpoints

These endpoints are accessible without authentication for widget integration:

### Get Season Officials (Public)
```http
GET /public/seasons/{seasonId}/officials
```
**CORS:** Allows all origins (`*`)

### Get Current Fixture Clocks (Public)
```http
GET /clock/{fixtureId}
```

Get current clock states for a fixture (live match data).

**Response:**
```json
{
  "fixture_id": "uuid",
  "server_time": "2024-01-15T14:30:00Z",
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

---

## ❌ Error Responses

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

## 🏒 Hockey Terminology

**Key Terms Used in Statistics:**

- **Challenges**: Hockey-specific scoring opportunities
- **Field Goals**: Goals scored during regular play (not penalty situations)
- **Penalty Corners**: Set pieces awarded for defensive infractions  
- **Penalty Strokes**: Direct scoring opportunities (similar to penalty kicks)
- **Circle Penetrations**: Entries into the scoring circle
- **Long Corners**: Corner kicks awarded for certain infractions
- **Shootout**: Penalty stroke competition to decide tied matches
- **Bib**: Jersey/uniform number
- **Plus/Minus**: Player rating system (+/- when on field during goals)

---

## 🔧 Technical Notes

1. **UUID fields** are used throughout for primary keys
2. **JSON metadata fields** provide extensibility
3. **Query parameters** supported on most endpoints for filtering/sorting
4. **Timestamps** stored with/without timezone depending on field
5. **Real-time data** available through timeline and clock endpoints
6. **Multi-language support** with Latin/Local naming conventions

---

*Last updated: January 2024*
