# Evently — Event Discovery & Tracking

A full-stack event discovery platform built for the Vibe Coding brief. Users can explore local events, view an event calendar, RSVP, share invite links, track invite clicks, and ask an event assistant to find experiences.

## Features
- Event feed with Ticketmaster Discovery API integration and local demo events when no API key is configured.
- Search and category filtering, responsive event cards, date/time and venue details.
- Monthly calendar with event-date markers.
- RSVP dashboard and duplicate RSVP validation.
- Per-RSVP invite links; backend stores and increments click counts and exposes a friends-attending count per event.
- Context-aware chat assistant that returns event suggestions.
- MongoDB persistence when `MONGODB_URI` is configured; in-memory demo mode otherwise.

## Requirements
Node.js 18+ and npm. MongoDB Atlas URI and Ticketmaster API key are optional for local demo mode; configure them for persistent RSVP data and live Ticketmaster listings.

## Run locally
1. Copy `server/.env.example` to `server/.env` and fill in credentials if available.
2. From the repository root run:
   ```bash
   npm run install:all
   npm run dev
   ```
3. Open `http://localhost:5173`. API health check: `http://localhost:5000/api/health`.

For a deployed frontend, set `VITE_API_URL` to the deployed backend's `/api` base URL. Set `CLIENT_URL` on the backend to the frontend origin.

## API
| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/api/health` | API/database status |
| GET | `/api/events?city=Pune&keyword=music&category=Music` | Event feed and filters |
| GET | `/api/rsvps` | RSVP dashboard data |
| POST | `/api/rsvps` | Create RSVP and invite code (`eventId`, `eventName`, `userName`, optional `userEmail`) |
| GET | `/api/rsvps/stats/:eventId` | Total invite-link clicks for an event |
| POST | `/api/rsvps/invite/:code/click` | Track invite-link click |
| POST | `/api/chat` | Event assistant (`message`) |

## Architecture
The React client owns presentation and interaction. Express exposes REST endpoints and handles validation, event mapping, RSVP uniqueness, invite generation and click calculations. Mongoose models persist RSVPs and invites. Ticketmaster credentials remain on the server and are never exposed to the browser.

## Notes for the demo / viva
- Without credentials, the server intentionally returns clearly defined sample events and keeps RSVP/invite data in memory so the UI can be demonstrated immediately. Restarting the server clears demo data.
- With MongoDB configured, RSVP and invite records persist. The demo profile is a lightweight name-based profile, not production authentication.
- “Friends Attending” is implemented as invite-link click count, matching the assessment brief's specified tracking requirement; a click is not proof that the visitor completed an RSVP.
- The chat assistant uses event-related keyword matching and live event lookup where configured; it is not represented as a generative LLM integration.
