How to run. the app


The commands used

# Build all project images
docker compose build

# Start all services in detached mode
docker compose up -d

# List this project's running containers
docker compose ps

# Follow only backend logs (press Ctrl+C to stop following)
docker compose logs -f server

# Open a shell in the backend container
docker compose exec server sh

# Run a SQL query inside the PostgreSQL container
docker compose exec db psql -U postgres -d taskboard -c "SELECT * FROM tasks;"

# Restart only the backend
docker compose restart server

# Rebuild and recreate only the frontend
docker compose build client
docker compose up -d --no-deps client

# Stop and remove containers while preserving database records
docker compose down

# List Docker images
docker image ls

# Show dangling (untagged) images
docker image ls -f dangling=true

# Review Docker disk usage and reclaimable space
docker system df -v


Docker Challenge Troubleshooting & Resolution Summary

Here’s a summary of the errors encountered during setup and how each was resolved:

1. Compose Configuration Error

Issue: Compose reported services.server.environment.db must be a boolean, null, number or string.

Root Cause: depends_on was incorrectly indented under environment, where Compose expected key-value pairs.

Fix: Adjusted indentation so depends_on became a sibling to environment in docker-compose.yml.

2. Missing .env File

Issue: Compose failed because it couldn’t find server/.env.

Fix: Created server/.env (or pointed env_file to the root .env) containing the required database host (db) and matching credentials.

3. Database Port Conflict

Issue: Docker failed to publish host port 5432 (Error response from daemon: ports are not available).

Fix: Changed the host port mapping to 5433:5432. The app and containers continue to communicate internally over default port 5432.

4. Backend/API Unavailable

Issue: The web interface displayed: "Could not reach the API. Check that the backend and database are running."

Root Cause: The server Dockerfile executed npm start, which expected a production build file (dist/index.js) that wasn't being compiled.

Fix: Updated the command to npm run dev for local development.

5. tsx: not found Error

Issue: The server container failed during startup with sh: 1: tsx: not found (exit code 127).

Root Cause: A typo in the docker-compose.yml volume mount path (/app/node_modules. instead of /app/node_modules).

Fix: Corrected the volume path string and rebuilt/recreated the backend container.

Final Verification & Results

API & Database Health Check

After recreating the backend container, the logs confirmed API listening on 3000. Running the health check verified full connectivity between the server and PostgreSQL:

$ curl http://localhost:3000/health
{"status":"ok","database":"connected"}


Application Working State

With all services running cleanly, tasks can now be created, completed, and persisted successfully in the application UI