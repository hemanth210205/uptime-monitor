# Uptime-monitor 🚀

[![Node.js](https://img.shields.io/badge/Node.js-v18+-68a063?style=flat-square&logo=node.js)](https://nodejs.org/)
[![Express](https://img.shields.io/badge/Express-5.x-black?style=flat-square&logo=express)](https://expressjs.com/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-14+-4169e1?style=flat-square&logo=postgresql)](https://www.postgresql.org/)
[![License: ISC](https://img.shields.io/badge/License-ISC-blue.svg?style=flat-square)](https://opensource.org/licenses/ISC)
[![Maintained](https://img.shields.io/badge/Maintained%3F-yes-brightgreen.svg?style=flat-square)]()

**Uptime-monitor** is a production-ready, full-stack website and URL uptime monitoring platform. It continuously evaluates the availability, response time, and HTTP status codes of target endpoints. When an incident or service interruption is detected, PingPilot logs the root cause, initiates an incident record, and dispatches real-time email alerts — automatically notifying you again once service is restored with the full downtime duration.

> Package identifier: `uptime-monitor`

---

## ✨ Features

- 🔐 **Google OAuth 2.0 Authentication** — Frictionless and secure user sign-in via Passport.js with persistent cookie-based session management.
- 📡 **Automated Cron-Driven Health Checks** — High-accuracy background scheduler (`node-cron`) checking endpoint uptime at custom intervals (30s, 60s, 300s, etc.).
- 📧 **Instant Email Alerts** — Automated outage and recovery notifications sent straight to your inbox via Nodemailer (Gmail / SMTP integration).
- 📊 **Public Status Pages** — Shareable, branded status pages showcasing 30-day uptime percentages, daily latency charts, and recent incident resolution logs.
- 📈 **Performance & Latency Visualizations** — Visual metrics and uptime trend charts powered by Chart.js.
- ⏸️ **Monitor Management** — Effortlessly register, pause, resume, or delete monitored URLs from an intuitive dashboard.
- 🗄️ **Relational PostgreSQL Storage** — Complete schema migrations for users, monitors, check logs, and incidents with cascading deletes and index optimizations.

---

## 🛠️ Tech Stack

| Layer | Technology | Purpose |
| :--- | :--- | :--- |
| **Runtime** | [Node.js](https://nodejs.org/) (v18+) | Server-side JavaScript runtime |
| **Framework** | [Express 5](https://expressjs.com/) | REST routing and HTTP application server |
| **Templating (UI)**| [EJS](https://ejs.co/) | Server-rendered dynamic HTML templates |
| **Database** | [PostgreSQL](https://www.postgresql.org/) (`pg`) | Relational database for metrics and monitors |
| **Authentication** | [Passport.js](https://www.passportjs.org/) + Google OAuth | Google single sign-on strategy |
| **Session Store** | [express-session](https://github.com/expressjs/session) | Cookie-based session storage |
| **Job Scheduler** | [node-cron](https://github.com/node-cron/node-cron) | Background ping intervals |
| **HTTP Client** | [Axios](https://axios-http.com/) | Low-latency ping execution |
| **Alert Engine** | [Nodemailer](https://nodemailer.com/) | SMTP email notifications |
| **Styling & Charts** | Modern CSS + [Chart.js](https://www.chartjs.org/) | Responsive dashboard & interactive graphs |

---

## 📁 Project Structure

The project follows a clean separation of concerns, isolating the API and background workers (`backend/`) from presentation templates (`frontend/`):

```
uptime-monitor/
├── backend/
│   ├── config/              # Database pool, Passport OAuth, and schema migrations
│   │   ├── db.js
│   │   ├── migrate.js
│   │   ├── passport.js
│   │   └── schema.sql
│   ├── controllers/         # Controller abstractions (.gitkeep)
│   ├── jobs/                # Background cron worker & ping engine
│   │   └── monitorScheduler.js
│   ├── middleware/          # Route guards and authentication checks
│   │   └── auth.js
│   ├── models/              # Database data access layer
│   │   ├── checkModel.js    # Ping telemetry & 30-day percentage calculations
│   │   ├── incidentModel.js # Incident opening, resolution, and duration formatting
│   │   └── monitorModel.js  # Monitor CRUD and status toggle queries
│   ├── routes/              # Express endpoint routers
│   │   ├── auth.js          # Google login, callback, and logout handlers
│   │   └── monitors.js      # Dashboard, monitor actions, and public status routes
│   ├── utils/               # Mailer alerts and formatting helpers
│   │   └── mailer.js
│   └── server.js            # Express server initialization & middleware stack
├── frontend/
│   ├── public/              # Static frontend assets (styles, scripts, images)
│   └── views/               # EJS server-rendered templates
│       ├── dashboard.ejs    # Authenticated user management dashboard
│       ├── landing.ejs      # Public landing page
│       └── status.ejs       # Public monitor status page with live graphs
├── .env.example             # Clean template of required environment variables
├── .gitignore               # Strict exclusion of secrets, OS files, and dependencies
├── LICENSE                  # ISC License
├── package.json             # Scripts, dependencies, and project metadata
├── README.md                # Project documentation
└── server.js                # Root forwarder entry point
```

---

## 🚀 Getting Started

### Prerequisites

Ensure you have the following installed and available:
- **Node.js** (v18.x or later) & **npm**
- A running **PostgreSQL** database instance
- A **Google Cloud Console** OAuth 2.0 Client ID and Secret ([Google Cloud Console](https://console.cloud.google.com/apis/credentials))
- An **SMTP-capable email account** (such as Gmail with an App Password)

### 1. Clone the repository

```bash
git clone https://github.com/hemanth210205/uptime-monitor.git
cd uptime-monitor
```

### 2. Install dependencies

```bash
npm install
```

### 3. Configure environment variables

Copy the example environment file:

```bash
cp .env.example .env
```

Open `.env` and fill in your actual values:

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

> ⚠️ **Security Warning**: The `.env` file contains sensitive credentials and is strictly excluded in `.gitignore`. Never commit it to GitHub.

### 4. Run database migrations

Initialize the required database schema (`users`, `monitors`, `checks`, `incidents`):

```bash
npm run migrate
```

### 5. Launch the application

```bash
# Development (with nodemon hot-reload)
npm run dev

# Production
npm start
```

Visit [http://localhost:3000](http://localhost:3000) in your browser.

---

## 🗺️ Application Routes

| Method | Endpoint | Access | Description |
| :--- | :--- | :--- | :--- |
| `GET` | `/` | Public | Landing page or redirects authenticated users to dashboard |
| `GET` | `/auth/google` | Public | Initiates Google OAuth authentication flow |
| `GET` | `/auth/google/callback` | Public | Google OAuth redirect callback endpoint |
| `GET` | `/auth/logout` | Authenticated | Clears user session and logs out |
| `GET` | `/api/monitors/dashboard` | Authenticated | Main user dashboard showing active monitors and metrics |
| `POST` | `/api/monitors` | Authenticated | Create a new monitor with custom check interval |
| `POST` | `/api/monitors/:id/pause` | Authenticated | Temporarily pause uptime checks for a monitor |
| `POST` | `/api/monitors/:id/resume` | Authenticated | Resume uptime checks for a paused monitor |
| `POST` | `/api/monitors/:id/delete` | Authenticated | Delete a monitor and its historical records |
| `GET` | `/api/monitors/:id/status-page` | Public | Public shareable status page with latency & uptime charts |
| `GET` | `/api/monitors/:id/stats` | Public | JSON endpoint returning 30-day uptime percentage and incidents |

---

## 🔄 How It Works

1. **Authentication:** Users log in securely with their Google account via Passport.js. Sessions are persisted in browser cookies.
2. **Ping Engine:** An asynchronous worker (`backend/jobs/monitorScheduler.js`) runs periodically. It selects all active monitors due for a check and executes an HTTP `GET` request using Axios.
3. **Telemetry Logging:** Latency in milliseconds, HTTP response codes, and errors (timeouts, DNS failures, 5xx errors) are recorded into PostgreSQL.
4. **Alerts & Incidents:**
   - If a monitor fails or returns non-2xx/3xx codes, an incident record is opened and an outage email is dispatched.
   - Once recovered, the open incident is closed, downtime duration is computed (e.g. `12m 45s`), and a recovery email is sent.
5. **Real-time Analytics:** Visual status pages compute 30-day uptime percentage and plot daily latency trends via Chart.js.

---

## 👤 Author

**Hemanth**
- GitHub: [@hemanth210205](https://github.com/hemanth210205)
- Email: [hemanth210205@gmail.com](mailto:hemanth210205@gmail.com)

---

## 📄 License

This project is licensed under the **ISC License**.

```
ISC License

Copyright (c) 2026 Hemanth

Permission to use, copy, modify, and/or distribute this software for any
purpose with or without fee is hereby granted, provided that the above
copyright notice and this permission notice appear in all copies.

THE SOFTWARE IS PROVIDED "AS IS" AND THE AUTHOR DISCLAIMS ALL WARRANTIES WITH
REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED WARRANTIES OF MERCHANTABILITY
AND FITNESS. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR ANY SPECIAL, DIRECT,
INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES WHATSOEVER RESULTING FROM
LOSS OF USE, DATA OR PROFITS, WHETHER IN AN ACTION OF CONTRACT, NEGLIGENCE OR
OTHER TORTIOUS ACTION, ARISING OUT OF OR IN CONNECTION WITH THE USE OR
PERFORMANCE OF THIS SOFTWARE.
```

You are free to use, fork, modify, integrate, and deploy this project for both personal and commercial use.
