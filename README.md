# CLIProxyAPI Usage Dashboard

Local usage collector and dashboard for [CLIProxyAPI](https://github.com/router-for-me/CLIProxyAPI).

It records every proxied request from CLIProxyAPI's Redis-compatible usage queue into SQLite, then shows:

- per-request token usage
- today's total usage
- last 1h / 5h / 24h / 7d usage
- usage by account and model
- Codex account 5h / 7d quota remaining

The dashboard binds to `127.0.0.1` by default.

## Requirements

- macOS or Linux
- Python 3.9+
- CLIProxyAPI with Management API enabled
- `usage-statistics-enabled: true`

For best reliability, set CLIProxyAPI's queue retention to one hour:

```yaml
usage-statistics-enabled: true
redis-usage-queue-retention-seconds: 3600
```

## Install

```bash
mkdir -p ~/.cli-proxy-api/usage-dashboard
cp usage_dashboard.py ~/.cli-proxy-api/usage-dashboard/
cp config.example.json ~/.cli-proxy-api/usage-dashboard/config.json
chmod 700 ~/.cli-proxy-api/usage-dashboard
chmod 600 ~/.cli-proxy-api/usage-dashboard/config.json
```

Edit `~/.cli-proxy-api/usage-dashboard/config.json` and set:

- `management_key`
- `proxy_api_key`

You can also use environment variables:

```bash
export CLIPROXY_MANAGEMENT_KEY="..."
export CLIPROXY_API_KEY="..."
```

Initialize the database:

```bash
python3 ~/.cli-proxy-api/usage-dashboard/usage_dashboard.py init
```

## Run Manually

In one terminal:

```bash
python3 ~/.cli-proxy-api/usage-dashboard/usage_dashboard.py collect
```

In another terminal:

```bash
python3 ~/.cli-proxy-api/usage-dashboard/usage_dashboard.py serve
```

Open:

```text
http://127.0.0.1:8320
```

## macOS LaunchAgent

Copy the template plists and replace `/Users/YOUR_USER` with your home directory:

```bash
mkdir -p ~/Library/LaunchAgents
cp launchd/com.cliproxyapi.usage-collector.plist ~/Library/LaunchAgents/
cp launchd/com.cliproxyapi.usage-dashboard.plist ~/Library/LaunchAgents/
```

Then load:

```bash
launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/com.cliproxyapi.usage-collector.plist
launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/com.cliproxyapi.usage-dashboard.plist
```

## CLI Reports

```bash
python3 ~/.cli-proxy-api/usage-dashboard/usage_dashboard.py report today
python3 ~/.cli-proxy-api/usage-dashboard/usage_dashboard.py report 5h
python3 ~/.cli-proxy-api/usage-dashboard/usage_dashboard.py quota --force
```

## Privacy

Do not commit:

- `config.json`
- SQLite databases
- logs
- OAuth auth files from `~/.cli-proxy-api`

The included `.gitignore` excludes runtime files by default.
