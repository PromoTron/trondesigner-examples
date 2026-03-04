# Docker Compose Deployment

## Files
- `docker-compose.yml`: Compose definition for the app
- `.env.example`: Example environment configuration

## Usage
1. Create env file:
   - `cp .env.example .env`
2. Set a real `TD_API_KEY` in `.env`
3. Start:
   - `docker compose up -d --build`
4. Stop:
   - `docker compose down`

The app will be available on `http://localhost:${PORT}` (default `3000`).
