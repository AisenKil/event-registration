# School Event Registration

Stack: React + Vite + TypeScript (frontend), Express + cors (backend), MySQL, nginx (load balancer / static host).

## Run locally
1. Create an empty MySQL database: `CREATE DATABASE event_registration;`
2. Backend: `cd backend && npm install && npm start` (env vars in `.env.example`; tables and sample data are created on start)
3. Frontend: `cd frontend && npm install && npm run dev` (proxies /api to port 3000)

Default admin: `admin@school.edu` / `admin123` (override with ADMIN_EMAIL / ADMIN_PASSWORD).

## CI/CD hooks
- Backend: `npm ci` then `npm test` (node built-in test runner, no DB needed)
- Frontend: `npm ci` then `npm run build` (type-checks, outputs `frontend/dist`)
- `nginx/nginx.conf`: round-robin across two backend containers, serves `frontend/dist`. Rename `backend1`/`backend2` to match your containers.

## Rules implemented
- One registration per user per event; approving beyond max participants auto-rejects.
- Notifications: user on approve/reject; admins when an event reaches max participants.

Jenkins CI automatic build test
