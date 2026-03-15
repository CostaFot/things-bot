# Things Bot

A Telegram bot that appends links and notes to a Markdown file in a GitHub repo. Send it a URL and it fetches the title, optionally adds a one-liner summary via Claude, and commits a new entry under today's date.

## Features

- Saves URLs with auto-fetched page titles
- Handles YouTube links (uses the video title)
- Supports an optional comment alongside any link
- Saves plain-text notes with no URL
- Optional AI summaries via the Claude API
- Output is a clean Markdown file — easy to read raw or publish via GitHub Pages

## Output format

```markdown
# Things

## Things — 16 March 2026

- [Why Kotlin context receivers are good now](https://...) — worth reading if you dismissed these early
- [Some YouTube Video Title](https://youtu.be/xxx)

## Things — 9 March 2026

- [Article title](https://...) — your comment here
- Just a plain note with no URL
```

## Usage

| You send | Bot does |
|---|---|
| `https://youtube.com/watch?v=xxx` | Fetches YouTube title, commits entry |
| `https://some-article.com` | Fetches page title, commits entry |
| `https://some-article.com great read` | Saves with your comment as the note |
| `reminder to look into Flow operators` | Saves as plain text note, no URL |

## Setup

### 1. Create a Telegram bot

1. Message [@BotFather](https://t.me/BotFather) on Telegram
2. Send `/newbot` and follow the prompts
3. Copy the token

### 2. Get your Telegram user ID

Message [@userinfobot](https://t.me/userinfobot) — it replies with your numeric user ID.

### 3. Create a GitHub token

1. Go to **GitHub → Settings → Developer settings → Personal access tokens**
2. Generate a new token (classic) with `repo` scope
3. Copy it

### 4. Create the content repo

Create a new public repo on GitHub (e.g. `yourname/things`). The bot will create the Markdown file automatically on first use.

### 5. Configure environment variables

```bash
cp .env.example .env
# Edit .env with your values
```

| Variable | Required | Description |
|---|---|---|
| `TELEGRAM_TOKEN` | Yes | Token from @BotFather |
| `GITHUB_TOKEN` | Yes | Personal access token with `repo` scope |
| `GITHUB_REPO` | Yes | Target repo in `owner/repo` format |
| `GITHUB_FILE_PATH` | No | Path to the Markdown file (default: `THINGS.md`) |
| `ALLOWED_USER_ID` | Yes | Your Telegram numeric user ID |
| `ANTHROPIC_API_KEY` | No | Claude API key for auto-summaries |

### 6. Run locally

```bash
pip install -r requirements.txt
export $(cat .env | grep -v '#' | xargs)
python bot.py
```

### 7. Deploy

**Railway (recommended — free tier):**

1. Push this repo to GitHub
2. Go to [railway.app](https://railway.app) → New Project → Deploy from GitHub
3. Add your environment variables in the Railway dashboard
4. Deploy — Railway runs it 24/7

**Docker (any VPS):**

```bash
docker build -t things-bot .
docker run -d --env-file .env --restart unless-stopped things-bot
```

## Publishing to GitHub Pages

To serve your Markdown file as a public webpage:

1. Set `GITHUB_FILE_PATH=docs/index.md` in your `.env` (the bot will create it on first use)
2. In your content repo, go to **Settings → Pages**
3. Set source to **Deploy from a branch**, branch `main`, folder `/docs`
4. Your file will be live at `https://yourname.github.io/things/`

GitHub Pages renders Markdown automatically via Jekyll — no extra config needed.

## Optional: AI summaries

Set `ANTHROPIC_API_KEY` to enable automatic one-liner summaries via Claude. If the key is blank, entries are saved with just the title and your comment (if any).
