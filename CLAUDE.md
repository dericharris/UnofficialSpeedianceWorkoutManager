# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

UnofficialSpeedianceWorkoutManager is a Flask-based desktop web application for creating, managing, and scheduling workouts on Speediance gym devices. It provides a local web interface that communicates directly with Speediance's API.

## Development Commands

```bash
# Install dependencies
pip install -r requirements.txt

# Run the application (serves at http://localhost:5001)
python app.py

# Run end-to-end tests (requires valid credentials in config.json or env vars)
python test_e2e_workouts.py
```

For testing, credentials can be provided via:
- `config.json` file (copy from `config.example.json`)
- Environment variables: `SPEEDIANCE_USER_ID`, `SPEEDIANCE_TOKEN`

## Architecture

### Core Files

- **app.py** - Flask application with all routes, UI logic, and template rendering. Entry point that runs the web server.
- **api_client.py** - `SpeedianceClient` class that handles all Speediance API interactions, credential management, and local caching.

### Templates (`templates/`)

Jinja2 HTML templates with embedded JavaScript and Tailwind CSS (via CDN):
- `layout.html` - Base template with navigation and debug modal
- `index.html` - Dashboard with calendar view and drag-drop scheduling
- `create.html` - Workout builder with exercise selection and ordering
- `library.html` - Exercise browsing interface
- `exercise_detail.html` - Individual exercise details with media
- `settings.html` - Authentication, preferences, and offline mode

### Key Patterns

**API Client**: `SpeedianceClient` manages region-aware API endpoints (Global/EU/CN), caches exercise libraries per device type to JSON files, and stores debug info for troubleshooting.

**Configuration**: Uses `config.json` for user credentials (user_id, token, region, unit, device_type, etc.). Never stores passwords—only session tokens.

**Media Caching**: Exercise images/videos are proxied through `/media_proxy` and cached to `static/media_cache/` for offline use.

**Frontend**: Plain JavaScript with HTML5 drag-drop API. No build tools—all scripts are inline or via CDN.

## API Routes

Main routes in `app.py`:
- `/` - Dashboard/calendar
- `/library` - Exercise browser
- `/create`, `/edit/<code>` - Workout builder
- `/settings` - Auth and preferences
- `/api/calendar`, `/api/schedule`, `/api/exercise/<id>` - JSON endpoints

## Build & Distribution

PyInstaller builds are automated via GitHub Actions (`.github/workflows/build.yml`):
- Triggers on release publish or manual dispatch
- Produces Windows `.exe` and macOS `.app` bundles
- Bundles templates, static assets, and config example

## LLM Integration

`llm_prompt.txt` contains a system prompt with the full exercise library (IDs and metadata) for generating workout JSON that can be imported into the application.
