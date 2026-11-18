# cmed-backend

Express API for CampusMed, a campus healthcare web app. Patients and providers can register and log in, manage profiles, request and confirm appointments, record consultations and follow-ups, and receive notifications. A cron job creates appointment and medication reminder notifications.

The React client is in [cmed-frontend](https://github.com/clintunn/cmed-frontend).

## Tech stack

- Node.js, Express
- MongoDB with Mongoose
- express-session with connect-mongo
- bcrypt and validator for user credentials
- node-cron for reminder scheduling
- axios (used by the chat message route)

## Features

- Auth for `patient` and `provider` roles (`POST /api/auth`, `POST /api/auth/login`), plus patient and provider login/logout routes. Passwords are hashed with bcrypt.
- Patient profiles, search, medical history, last consultation, and appointment requests
- Provider profiles, pending appointments, and appointment confirmation
- Appointments, consultations, and follow-up consultations
- Notifications (list, unread count, mark read, delete)
- Chat sessions and messages. Sending a message uses `OPENAI_API_KEY`; the upstream request URL in `routes/chatRoute.js` is currently empty and needs to be set before that path will work.
- A one-minute scheduler that creates notifications for due medication reminders and confirmed appointments

Route modules also exist for medications, reminders, and interaction history, but they are not mounted in `server.js`. `Socket/socket.js` is empty, so Socket.IO is not wired up.

The server listens on port **5001** and allows CORS from `http://localhost:3000`, `http://localhost:3001`, and `http://192.168.55.196:3000`.

## Run locally

```bash
npm install
cp .env.example .env
# set MONGO_URI to your MongoDB connection string
# set OPENAI_API_KEY if you want to use the chat message route
npm run dev    # nodemon
# or
npm start      # node server.js
```

`.env.example` lists `MONGO_URI`. `connection.js` and the session store in `server.js` currently use a hardcoded MongoDB URL instead of that variable — point both at `process.env.MONGO_URI` (and use a session `secret` from the environment) before running against your own database.

## API (mounted in `server.js`)

| Prefix | Purpose |
| --- | --- |
| `/api/auth` | Register and login |
| `/api/patients` | Patient CRUD, profile, search, history, appointment requests |
| `/api/providers` | Provider CRUD, profile, pending/confirm appointment |
| `/api/appointments` | Appointments |
| `/api/consultations` | Consultations and history |
| `/api/followUpConsultation` | Follow-up consultations |
| `/api/chat` | Chat sessions and messages |
| `/api/notifications` | Notifications |

## Project structure

```
server.js                 # App entry, session, CORS, route mounting
connection.js             # MongoDB connection
authMiddleware.js
Models/                   # User, Patient, HealthcareProvider, Appointment, Consultation, and related schemas
routes/
Services/                 # notificationService, schedulerService
```

Built by Clinton Nkamigbo · https://clinton-nkamigbo.vercel.app
