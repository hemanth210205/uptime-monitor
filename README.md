# PingPilot

**PingPilot** is a Node.js-based website and URL uptime monitoring platform. It periodically checks the availability and health of monitored endpoints, maintains response time history, generates public status pages, and sends email alerts whenever an outage or recovery occurs.

> Package identifier: `uptime-monitor`

---

## ✨ Features

- 🔐 **Google OAuth Login** — Secure authentication via Passport.js and Google OAuth 2.0
- 📡 **Automated Uptime Checks** — Background cron-scheduled ping engine running customizable check intervals
- 📧 **Instant Email Alerts** — Immediate downtime and recovery notifications powered by Nodemailer
- 📊 **Status Pages & Analytics** — 30-day uptime percentage calculations, incident history tracking, and response-time visualizations
- 🖥️ **Server-Rendered UI** — Clean, responsive dashboard built with EJS templates and modern styling
- 🗄️ **PostgreSQL Storage** — Robust relational storage with automatic migrations for users, monitors, checks, and incidents

---

## 🛠️ Tech Stack

| Layer            | Technology                          |
|-------------------|--------------------------------------|
| Runtime           | Node.js                             |
| Web Framework     | Express 5                           |
| Templating (UI)   | EJS                                 |
| Database          | PostgreSQL (`pg`)                   |
| Authentication    | Passport.js + Google OAuth 2.0      |
| Session Store     | Express-Session                     |
| Scheduler         | Node-Cron                           |
| HTTP Client       | Axios                               |
| Email Alerts      | Nodemailer                          |
| Config & Secrets  | Dotenv                              |
| Dev Tooling       | Nodemon                             |

---

## 📁 Project Structure

```
uptime-monitor/
├── backend/
│   ├── config/              # PostgreSQL pool, Passport OAuth strategy, and DB migrations
│   │   ├── db.js
│   │   ├── migrate.js
│   │   ├── passport.js
│   │   └── schema.sql
│   ├── controllers/         # Future controller abstractions
│   ├── jobs/                # Background cron worker & ping engine
│   │   └── monitorScheduler.js
│   ├── middleware/          # Route guards and authentication middleware
│   │   └── auth.js
│   ├── models/              # Database data access layer (checks, incidents, monitors)
│   │   ├── checkModel.js
│   │   ├── incidentModel.js
│   │   └── monitorModel.js
│   ├── routes/              # Express endpoint routers (auth, monitors)
│   │   ├── auth.js
│   │   └── monitors.js
│   ├── utils/               # Mailer and helper utilities
│   │   └── mailer.js
│   └── server.js            # Main backend server implementation
├── frontend/
│   ├── public/              # Static frontend assets (styles, scripts, images)
│   └── views/               # EJS server-rendered templates
│       ├── dashboard.ejs    # Authenticated user dashboard
│       ├── landing.ejs      # Public landing page
│       └── status.ejs       # Public monitor status page
├── .env.example             # Template for required environment variables
├── .gitignore               # Ignored files, dependencies, logs, and secrets
├── package.json             # Scripts, dependencies, and metadata
├── README.md                # Documentation
└── server.js                # Root forwarder entry point
```

---

## 🚀 Getting Started

### Prerequisites

- [Node.js](https://nodejs.org/) (v18+ recommended)
- A running [PostgreSQL](https://www.postgresql.org/) database
- A [Google Cloud Console](https://console.cloud.google.com/apis/credentials) OAuth 2.0 Client ID & Secret
- An SMTP-compatible email service (e.g. Gmail App Password)

### 1. Clone the repository

```bash
git clone <your-github-repo-url>
cd uptime-monitor
```

### 2. Install dependencies

```bash
npm install
```

### 3. Configure environment variables

Copy the sample environment file to create your local `.env`:

```bash
cp .env.example .env
```

Fill in your actual environment values:

```env
# Server Configuration
PORT=3000
SESSION_SECRET=your_super_secret_session_key

# PostgreSQL Database Connection URL
DATABASE_URL=postgresql://postgres:password@localhost:5432/uptime_monitor

# Google OAuth 2.0 Credentials
GOOGLE_CLIENT_ID=your_google_client_id.apps.googleusercontent.com
GOOGLE_CLIENT_SECRET=your_google_client_secret
GOOGLE_CALLBACK_URL=http://localhost:3000/auth/google/callback

# Email Alerts (Nodemailer via Gmail SMTP)
SMTP_USER=your_email@gmail.com
SMTP_PASS=your_gmail_app_password
ALERT_TO=alerts_recipient@gmail.com
```

> ⚠️ Never commit your `.env` file to version control. It is already excluded in `.gitignore`.

### 4. Run database migrations

Prepare your PostgreSQL tables (`users`, `monitors`, `checks`, `incidents`):

```bash
npm run migrate
```

### 5. Start the application

```bash
# Development (with nodemon auto-restart)
npm run dev

# Production
npm start
```

Open [http://localhost:3000](http://localhost:3000) in your browser.

---

## 🔄 How It Works

1. **Authentication**: Users sign in with their Google account via Passport.js. User sessions are persisted securely in cookies.
2. **Scheduling**: A background `node-cron` scheduler periodically selects active monitors and checks their HTTP response using `axios`.
3. **Telemetry**: Each ping records status code, latency, and error message to PostgreSQL.
4. **Alerts**: If a monitor goes down, an incident is opened and an immediate alert email is dispatched via Nodemailer. Once recovered, a resolution email with total downtime duration is sent.
5. **Status Pages**: Users and external visitors can view public status pages displaying 30-day uptime percentage, uptime charts, response time trends, and incident histories.

---

## 📄 License

ISC
