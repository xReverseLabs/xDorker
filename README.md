# xDorker

**AI-powered Google dork scanner** — generate dork queries with AI, run bulk scans, and export results to CSV, TXT, or JSON. Built by [xReverseLabs](https://xreverselabs.org).

🌐 **Live:** [xdorker.xreverselabs.org](https://xdorker.xreverselabs.org)

---

![setup token](https://i.ibb.co.com/hFs2qsXn/Screenshot-2026-04-02-at-08-31-16.png)

![scan run](https://i.ibb.co.com/bMVrCLbF/Screenshot-2026-04-02-at-08-31-43.png)

## Features

- 🤖 **AI Dork Generation** — describe a target, get ready-to-use dork queries powered by OpenRouter AI
- 🔍 **Bulk Scanning** — submit up to 100 dorks at once, up to 10 pages per dork
- 📦 **Multiple Export Formats** — CSV, TXT, JSON, or table output
- ⚡ **Background Processing** — scans run in the background, view live progress
- 🔑 **API Token Auth** — secure REST API for CLI and integrations
- 👥 **Multi-user** — plan-based quota system (Free, Starter, Agency, Enterprise)
- 💳 **Payment Integration** — Midtrans & Cryptomus support

---

## CLI Tool

The `xdorker` CLI lets you run scans, generate AI dorks, and export results directly from your terminal — no browser needed.

### Download

| Platform | File |
|----------|------|
| Linux x86_64 | [xdorker-linux-amd64](https://github.com/xReverseLabs/xDorker/raw/refs/heads/main/xdorker-linux-amd64) |
| Linux ARM64 | [xdorker-linux-arm64](https://github.com/xReverseLabs/xDorker/raw/refs/heads/main/xdorker-linux-arm64) |
| macOS Intel | [xdorker-mac-amd64](https://github.com/xReverseLabs/xDorker/raw/refs/heads/main/xdorker-mac-amd64) |
| macOS Apple Silicon | [xdorker-mac-arm64](https://github.com/xReverseLabs/xDorker/raw/refs/heads/main/xdorker-mac-arm64) |
| Windows 64-bit | [xdorker-windows-amd64.exe](https://github.com/xReverseLabs/xDorker/raw/refs/heads/main/xdorker-windows-amd64.exe) |

### Installation

**Linux / macOS:**
```bash
# Download (example: Linux x86_64)
wget https://github.com/xReverseLabs/xDorker/releases/latest/download/xdorker-linux-amd64 -O xdorker
chmod +x xdorker
./xdorker --help
```

**Windows:**
```
Download xdorker-windows-amd64.exe, rename to xdorker.exe, run from CMD or PowerShell.
```

### Setup

```bash
xdorker setup --url https://xdorker.xreverselabs.org --token xdk_your_token_here
```

Get your API token from the dashboard → **API Tokens** page.

---

## CLI Usage

```
xdorker <command> [flags]
```

### Commands

| Command | Description |
|---------|-------------|
| `setup` | Configure server URL and API token |
| `scan` | Run a scan — auto-waits and saves results |
| `jobs` | List recent scan jobs |
| `me` | Show account and quota info |
| `status` | Check a specific job by ID |
| `results` | Fetch results for a completed job |

---

### `scan` — Run a Scan

```bash
# AI-generated dorks from a keyword
xdorker scan --ai "wordpress indonesia" --pages 3 --output results.csv

# From a .txt file (one dork per line)
xdorker scan --batch dorks.txt --pages 5 --output urls.txt --format txt

# Single dork query
xdorker scan --dork "inurl:wp-config.php" --pages 2

# With page range
xdorker scan --batch dorks.txt --start-page 2 --end-page 5 --output out.json --format json
```

**Flags:**

| Flag | Description | Default |
|------|-------------|---------|
| `--ai "keyword"` | Generate dorks from keyword using AI | — |
| `--dork "query"` | Single dork query | — |
| `--batch file.txt` | File with dork queries (one per line) | — |
| `--pages N` | Number of pages per dork | `1` |
| `--start-page N` | Starting page number | `1` |
| `--end-page N` | Ending page number (overrides `--pages`) | — |
| `--output file` | Save results to file (auto-enables `--wait`) | — |
| `--format` | Output format: `csv`, `txt`, `json`, `table` | `csv` |
| `--wait` | Wait for scan to complete before returning | `false` |

---

### `results` — Fetch Results

```bash
# Print to terminal (table format)
xdorker results 42

# Save to file
xdorker results 42 --output out.csv --format csv
xdorker results 42 --output urls.txt --format txt
```

---

### `jobs` — List Recent Jobs

```bash
xdorker jobs
```

---

### `me` — Account & Quota

```bash
xdorker me
```

---

### `status` — Check a Job

```bash
xdorker status 42
```

---

## REST API

All endpoints require `Authorization: Bearer xdk_your_token` header.

### Base URL
```
https://xdorker.xreverselabs.org/api/v1
```

### Endpoints

#### `GET /me`
Returns account info and quota usage.

```bash
curl https://xdorker.xreverselabs.org/api/v1/me \
  -H "Authorization: Bearer xdk_your_token"
```

```json
{
  "name": "Bayu",
  "email": "user@example.com",
  "plan": "agency",
  "quota_used": 284,
  "quota_limit": 8000,
  "quota_remaining": 7716
}
```

---

#### `POST /scan`
Submit a new scan job.

```bash
curl -X POST https://xdorker.xreverselabs.org/api/v1/scan \
  -H "Authorization: Bearer xdk_your_token" \
  -H "Content-Type: application/json" \
  -d '{
    "dorks": ["inurl:wp-config.php", "filetype:env DB_PASSWORD"],
    "page_from": 1,
    "page_to": 3
  }'
```

```json
{
  "id": 42,
  "status": "pending",
  "total_dorks": 2,
  "processed_dorks": 0,
  "result_count": 0,
  "page_from": 1,
  "page_to": 3,
  "created_at": "2026-04-02T10:00:00+00:00"
}
```

---

#### `GET /scan`
List recent scan jobs.

```bash
curl "https://xdorker.xreverselabs.org/api/v1/scan?limit=10" \
  -H "Authorization: Bearer xdk_your_token"
```

---

#### `GET /scan/{id}`
Get status of a specific scan job.

```bash
curl https://xdorker.xreverselabs.org/api/v1/scan/42 \
  -H "Authorization: Bearer xdk_your_token"
```

---

#### `GET /scan/{id}/results`
Fetch results for a completed scan.

```bash
curl "https://xdorker.xreverselabs.org/api/v1/scan/42/results?per_page=100&page=1" \
  -H "Authorization: Bearer xdk_your_token"
```

```json
{
  "job_id": 42,
  "status": "completed",
  "data": [
    {
      "dork_query": "inurl:wp-config.php",
      "title": "...",
      "url": "https://example.com/wp-config.php",
      "snippet": "...",
      "position": 1
    }
  ],
  "total": 87,
  "page": 1,
  "per_page": 100,
  "last_page": 1
}
```

---

#### `POST /ai/generate`
Generate dork queries from a keyword using AI.

```bash
curl -X POST https://xdorker.xreverselabs.org/api/v1/ai/generate \
  -H "Authorization: Bearer xdk_your_token" \
  -H "Content-Type: application/json" \
  -d '{"keyword": "wordpress indonesia"}'
```

```json
{
  "dorks": [
    "site:id inurl:wp-admin",
    "site:id \"Powered by WordPress\"",
    "site:id filetype:sql wp_users"
  ]
}
```

---

## Plans

| Plan | Quota/month | Pages | API Access |
|------|-------------|-------|------------|
| Starter | 4,000 queries | up to 10 | ❌ |
| Agency | 8,000 queries | up to 10 | ❌ |
| Enterprise | 15,000 queries | up to 10 | ✅ |

---

## License

Proprietary — © 2026 xReverseLabs. All rights reserved.
