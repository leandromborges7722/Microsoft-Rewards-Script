# Microsoft Rewards Script

[![Validate](https://github.com/TheNetsky/Microsoft-Rewards-Script/actions/workflows/validate.yml/badge.svg?branch=v5)](https://github.com/TheNetsky/Microsoft-Rewards-Script/actions/workflows/validate.yml)
![Version](https://img.shields.io/github/package-json/v/TheNetsky/Microsoft-Rewards-Script/v5)
[![Docker image (GHCR)](https://img.shields.io/badge/Docker-GHCR%20v5-2496ED?logo=docker&logoColor=white)](https://github.com/TheNetsky/Microsoft-Rewards-Script/pkgs/container/microsoft-rewards-script)
![Node.js](https://img.shields.io/badge/node-%3E%3D24-green)
[![License](https://img.shields.io/badge/license-GPL--3.0-blue)](LICENSE)
[![Donate](https://img.shields.io/badge/Donate-Ko--fi-FF5E5B?logo=ko-fi&logoColor=white)](https://ko-fi.com/thenetsky)

Microsoft Rewards automation with API and browser modes, saved sessions,
multiple accounts, localised searches and an optional Control API.

## Features

- Daily Set, promotions, punchcards and bonus-point claims.
- Desktop/mobile searches, Explore on Bing, Daily Check-In and Read to Earn.
- Optional Visual Search and background Edge browsing.
- Streak progress, API-based streak protection and verified point reporting.
- Scheduled runs, account selection, saved sessions and Discord/Telegram/ntfy notifications.
- Control API with live events, persistent history, punchcard progress and activity balance observations.

Activities depend on the account and region. This branch contains v5; earlier
versions remain available on [v3](https://github.com/TheNetsky/Microsoft-Rewards-Script/tree/v3)
and [v4](https://github.com/TheNetsky/Microsoft-Rewards-Script/tree/v4).

## Documentation

- [Setup and usage](docs/usage.md) — local, Nix and Docker installation, accounts, settings, commands and troubleshooting.
- [Control API](docs/control-api.md) — authentication, endpoints, run control, scheduling and dashboard data.

## Setup (Ubuntu / Linux Mint)

1.Install prerequisites and Node.js 24+

```sh
sudo apt update
sudo apt install -y ca-certificates curl gnupg git
curl -fsSL https://deb.nodesource.com/setup_24.x -o /tmp/nodesource_setup.sh
sudo -E bash /tmp/nodesource_setup.sh
sudo apt install -y nodejs
```

Verify the installation:
```sh
node -v
npm -v
```

2. Clone the repository
```sh
git clone --branch v5 https://github.com/TheNetsky/Microsoft-Rewards-Script.git
cd Microsoft-Rewards-Script
```

3. Configure accounts (`.env`)
Copy env.example to .env and fill in your credentials:
```sh
cp env.example .env
nano .env
```

4. Install dependencies, Chromium, build and sync config
```sh
npm ci
npm run install:browser
npm run build
node dist/config/ConfigSync.js sync --patch
```

- npm run install:browser downloads Patchright's Chromium (ubuntu24.04-x64 build on Ubuntu/Mint) along with FFmpeg and Headless Shell.

- node dist/config/ConfigSync.js sync --patch automatically generates config.json from the built-in defaults if it does not exist yet (or patches an existing config.json with new default fields).

- If Chromium fails to launch due to missing OS libraries on a minimal Ubuntu/Mint install, install system dependencies with:
```sh
npx patchright install-deps chromium
```

5. Run the script
```sh
npm start
```

## Modes and settings

Edit [config.json](config.example.json) before starting a run.

| Setting                            | Purpose                                                        |
| ---------------------------------- | -------------------------------------------------------------- |
| `rewardsMode: "browser"`           | Complete web activities through rendered cards and GhostCursor |
| `rewardsMode: "api"`               | Save the sign-in session, close its browser and use HTTP       |
| `searchSettings.parallelSearching` | Overlap desktop and mobile work when enabled                   |
| `workers`                          | Enable or disable individual tasks                             |
| `taskDelay`                        | Delay between activities                                       |
| `searchSettings.searchDelay`       | Delay between searches                                         |
| `searchSettings.useAccountLocale`  | Select queries using the account country                       |
| `ensureStreakProtection`           | Enable and verify protection over HTTP in both modes           |
| `experimental`                     | Optional features; disabled by default                         |

See the full [settings reference](docs/usage.md#settings-reference) for all options.

## Sessions

To sign in manually on a desktop with a visible browser:

```sh
npm run manual-login -- user@example.com --platform both
npm run open-session -- --help
```

Sessions are saved under `sessionPath`. Keep `.env`, local configuration,
session databases and diagnostics private.

## Docker

Create `.env` from `env.example`, then review the timezone and schedule in
[compose.yaml](compose.yaml).

```sh
docker compose up -d --build
docker compose logs -f
```

The published v5 image is `ghcr.io/thenetsky/microsoft-rewards-script:5`.
To use it instead of building locally, follow the [Docker image instructions](docs/usage.md#docker).

Docker persists configuration in `./config`, sessions in `./sessions` and optional
error captures in `./diagnostics`. It creates `config/config.json` from defaults
when needed. `RUN_ON_START` controls the first run; `CRON_SCHEDULE` sets the initial
schedule. Control API schedule changes persist in `config/schedule.json`.
See [Control API Docker setup](docs/control-api.md#docker) to enable the server.

## License

[GPL-3.0-or-later](LICENSE).
