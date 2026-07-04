# 🎫 Ticket System

A self-hosted Discord support ticket bot with a full-featured web dashboard. Members open tickets inside Discord; staff manage, claim, and close them — all activity is logged, rated, and reviewable through a real-time admin panel.

---
<h2 align="center">📸 SAMPLES</h2>

<p align="center">
  <img src="login.png" width="900" alt="Discord OAuth Login">
</p>

<p align="center">
  <img src="dashboard.png" width="440" alt="Dashboard">
  <img src="guilds.png" width="440" alt="Servers">
</p>

<p align="center">
  <img src="guild-settings.png" width="440" alt="Guild Settings">
  <img src="guild-settings-2.png" width="440" alt="Guild Settings Continued">
</p>

<p align="center">
  <img src="ban.png" width="440" alt="Ban Management">
  <img src="ratings.png" width="440" alt="Ratings">
</p>

<p align="center">
  <img src="bot-setup.png" width="900" alt="Bot Setup Wizard">
</p>

## ✨ Features

### Discord Bot
| Feature | Details |
|---|---|
| **7 ticket categories** | General Support, Bug / Player Report, Ban Appeal, Partnership, Verification, Administrative, Other — each with tailored guidance text and a unique color |
| **Full ticket lifecycle** | Open → Claim → Close (with reason) → Delete |
| **Guided prompts** | Each category shows a tailored guidance embed when a ticket is opened |
| **Verification flow** | Dedicated verify-channel button + ticket-based role granting |
| **Star ratings** | 1–5 star rating request sent to the user after ticket close |
| **Ticket bans** | Ban users from opening future tickets; managed from the dashboard |
| **Log channel** | All events (open, claim, close, delete) posted as structured embeds to a dedicated channel |
| **Per-ticket limits** | Configurable max open tickets per user |
| **Slash-free design** | Everything is driven by buttons and select menus — no slash commands needed |

### Web Dashboard
| Feature | Details |
|---|---|
| **Discord OAuth2 login** | Staff sign in with their Discord account |
| **Guild selector** | One dashboard instance manages multiple servers |
| **Live channel & role pickers** | Combobox dropdowns populated from the live bot — no manual ID copy-pasting |
| **Global stats** | Total tickets, open tickets, closed tickets, avg. rating across all guilds |
| **Per-guild stats** | Ticket counts, ratings, activity breakdowns |
| **Guild configuration** | Set ticket channel, category channel, log channel, verify channel, staff/support/member/verified roles, welcome message, footer, embed color, ticket prefix, and more |
| **Remote panel posting** | Post the ticket panel embed directly to Discord from the dashboard |
| **Ticket browser** | View all tickets with status, category, claim info, and timestamps |
| **Ban management** | Search, add, and remove ticket bans |
| **Ratings browser** | Browse and filter all submitted star ratings |
| **Role-based access** | Only users with "Manage Server" permission (or the configured bot owner) can access guild settings |

---

## 🛠 Tech Stack

| Layer | Technology |
|---|---|
| **Bot & API** | Node.js, Express v5, Discord.js v14 |
| **Frontend** | React 19, Vite, Tailwind CSS v4, Radix UI, Framer Motion, Wouter |
| **Database** | PostgreSQL + Drizzle ORM |
| **Auth** | Discord OAuth2 (`express-session`) |
| **API contract** | OpenAPI 3 spec + auto-generated Zod schemas and React Query hooks |
| **Package manager** | pnpm (monorepo) |

---

## 📋 Requirements

- **Node.js** 20 or later
- **pnpm** 10 or later — install with `npm i -g pnpm`
- **PostgreSQL** database (local, Supabase, Railway, Neon, etc.)
- A **Discord Application** with a bot user

---

## 🤖 Discord Application Setup

1. Go to [discord.com/developers/applications](https://discord.com/developers/applications) and create a new application.
2. Under **Bot**, enable the following **Privileged Gateway Intents**:
   - `SERVER MEMBERS INTENT`
   - `MESSAGE CONTENT INTENT`
3. Under **OAuth2 → General**, add a redirect URL:
   ```
   http://localhost:5000/api/auth/callback   ← for local development
   https://your-domain.com/api/auth/callback ← for production
   ```
4. Copy your **Client ID**, **Client Secret**, and **Bot Token** — you'll need them for environment variables.
5. Invite the bot to your server using this URL (replace `CLIENT_ID`):
   ```
   https://discord.com/oauth2/authorize?client_id=CLIENT_ID&scope=bot&permissions=8
   ```
   Minimum required bot permissions:
   - Manage Channels · Manage Roles · View Channels · Send Messages
   - Embed Links · Attach Files · Read Message History
   - Add Reactions · Use External Emojis

---

## ⚙️ Environment Variables

Create a `.env` file in the project root:

```env
# PostgreSQL connection string
DATABASE_URL=postgresql://user:password@host:5432/dbname

# Discord application credentials
DISCORD_BOT_TOKEN=your_bot_token
DISCORD_CLIENT_ID=your_client_id
DISCORD_CLIENT_SECRET=your_client_secret

# OAuth2 callback URL (must match what you set in the Discord Developer Portal)
DISCORD_REDIRECT_URI=http://localhost:5000/api/auth/callback

# Random secret for signing session cookies — use a long random string
SESSION_SECRET=change_me_to_a_random_string

# Discord User ID of the master admin (bypasses guild permission checks)
BOT_OWNER_ID=your_discord_user_id

# Optional — log verbosity: trace | debug | info | warn | error (default: info)
LOG_LEVEL=info
```
---

# 🛒 Purchase & Live Demo

Interested in purchasing the source code or trying it before buying?

I maintain a dedicated demo Discord server where you can experience the complete ticket workflow, including:

- Creating tickets
- Category selection
- Verification system
- Staff claiming and closing tickets
- Ticket ratings
- Live dashboard synchronization
- Guild configuration
- Ticket management from the web dashboard

If you'd like to test the bot or purchase the source code, send me a direct message on Discord.

> **Discord:** `Notcyy_`
> 
>  **Discord server:** https://discord.gg/HmRj6JsJg

### Accepted Payment Methods

- PayPal
- GCash

> Demo access is provided for evaluation purposes only and may be limited to genuine buyers.

---

## 📁 Project Structure

```
.
├── artifacts/
│   ├── api-server/          # Express API + Discord.js bot (single process)
│   │   └── src/
│   │       ├── lib/         # bot logic, auth, embeds, logger
│   │       └── routes/      # guilds, tickets, bans, ratings, auth
│   └── dashboard/           # React + Vite frontend
│       └── src/
│           ├── components/  # UI components (comboboxes, cards, tables)
│           └── pages/       # guilds, tickets, bans, ratings, settings
├── lib/
│   ├── db/                  # Drizzle ORM schema + migrations
│   ├── api-spec/            # OpenAPI 3 spec (source of truth for the API)
│   ├── api-zod/             # Auto-generated Zod request/response schemas
│   └── api-client-react/    # Auto-generated React Query hooks
├── render.yaml              # Render deployment config
└── pnpm-workspace.yaml      # Monorepo workspace definition
```

---

## 🗄 Database Tables

| Table | Purpose |
|---|---|
| `guilds` | Per-guild configuration (channels, roles, settings) |
| `tickets` | All tickets with status, category, claim, and close info |
| `ticket_ratings` | Star ratings submitted by users after close |
| `ticket_bans` | Users banned from opening tickets |
| `ticket_logs` | Audit log of all ticket events |

Schema changes are managed with Drizzle. To regenerate after editing `lib/db/src/schema/`:
```bash
pnpm --filter @workspace/db run push
```

---

## 🔧 Customization

### Ticket categories
Edit `artifacts/api-server/src/lib/bot-embeds.ts` — the `CATEGORIES` array. Each entry has a `value`, `label`, `description`, `emoji`, `guidance` message, and `color`. The select menu is built from this array automatically.

### Welcome message & embed color
Configurable per-guild from the dashboard without touching code.

### Ticket prefix and max tickets per user
Also configurable per-guild from the dashboard.

### Auto-close
Toggle and set the inactivity window (hours) per guild from the dashboard.

### Transcript and rating toggles
Both can be disabled per guild from the dashboard.

---

## 🔑 Authentication & Access Control

- Users log in via **Discord OAuth2**.
- A user can manage a guild's settings if they have the **Manage Server** permission in that guild.
- The Discord user ID set as `BOT_OWNER_ID` has full access to all guilds regardless of permissions — useful for the deployer / developer.
- Sessions are stored server-side and signed with `SESSION_SECRET`.

---

## 🔄 API Regeneration (for developers modifying the API)

The API contract lives in `lib/api-spec/openapi.yaml`. After editing it, regenerate the client libraries:

```bash
pnpm --filter @workspace/api-spec run codegen
```

This regenerates Zod schemas (`lib/api-zod`) and React Query hooks (`lib/api-client-react`). The client library is TypeScript-source-only and requires no separate build step.

---

## 📜 License

This source code is sold under a **commercial single-use license**. By purchasing you are granted the right to deploy and modify the code for your own use. You may **not** resell, redistribute, re-license, or offer the source code (or any derivative) as a product to others.

> **Note for buyers:** The root `package.json` currently contains `"license": "MIT"`. Update this field to reflect your own license terms before distributing or publishing any fork of this project.
