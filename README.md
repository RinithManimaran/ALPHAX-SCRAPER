<p align="center">
  <img src="assets/logo.svg" width="420" alt="AlphaX logo">
</p>

# AlphaX Scraper

Find business contacts by profession and country/region, and export them to
an Excel spreadsheet (Name, Email, Phone, Title, Organization, Location,
LinkedIn) — built on [Apollo.io](https://www.apollo.io)'s People Search and
Enrichment API, **not** open-web scraping.

## Why Apollo instead of scraping the web

A tool that crawls the whole internet for people's personal emails, with no
regard for consent, is also a tool for building spam lists and unwanted
contact — regardless of what it's used for first. It also runs straight
into the terms of service of nearly every site it would touch.

Apollo's database is built and maintained under its own privacy program: it
won't reveal personal emails for people in GDPR-covered regions, it
supports opt-outs, and using its API keeps you within the terms of the
sites it draws from instead of scraping them yourself. That's the whole
reason this tool is built on Apollo rather than a general scraper.

If this is for outreach, you're still responsible for following
CAN-SPAM / GDPR / your local anti-spam law when you actually send email —
accurate sender identity, an unsubscribe mechanism, and honoring opt-out
requests. This tool finds contacts; it doesn't send anything.

## Install

1. Get an Apollo API key: https://docs.apollo.io/docs/create-api-key
   (free tier available; revealing emails/phones consumes credits — see
   [Apollo's pricing](https://docs.apollo.io/docs/api-pricing)).
2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
3. Copy `.env.example` to `.env` and add your key:
   ```bash
   cp .env.example .env
   # edit .env -> APOLLO_API_KEY=your_key_here
   ```

## Usage

**Preview** how many people match, without spending any credits:
```bash
python scripts/search_contacts.py \
  --profession "Physical Therapist" \
  --country "United States" \
  --region "California" \
  --max-results 50 \
  --dry-run
```

**Run for real** (reveals emails; asks you to confirm the estimated credit
cost first):
```bash
python scripts/search_contacts.py \
  --profession "Physical Therapist" \
  --country "United States" \
  --region "California" \
  --max-results 50
```

| Flag | Default | Notes |
|---|---|---|
| `--profession` | *required* | Job title(s), comma-separated — matches similar titles too unless `--exact-title-only` is set |
| `--country` | *required* | e.g. `"United States"` |
| `--region` | none | State / province / city |
| `--max-results` | 25 | Caps people found **and** credits spent |
| `--reveal-phones` | off | Costs more credits (8/phone vs 1/email); async/best-effort |
| `--exact-title-only` | off | Disable matching similar job titles |
| `--dry-run` | off | Search only — 0 credits spent, no emails |
| `--output` | `contacts.xlsx` | Output file path |
| `-y`, `--yes` | off | Skip the credit-spend confirmation prompt |

## Output columns

`Name · Email · Email Status · Phone · Title · Organization · City ·
State/Region · Country · LinkedIn`

`Email Status` is Apollo's own verification state (e.g. `verified`,
`guessed`) — worth checking before you rely on an address.

## Using this as a Claude Code skill

This repo includes a `SKILL.md`. Drop the folder into your skills
directory (or point your plugin loader at it) and you can just ask Claude
things like *"find me 30 marketing directors in Toronto and put them in a
spreadsheet."*

## Limits

- Apollo's database doesn't have everyone, and `--region` is matched as
  free text (e.g. `"California, US"`), so an unusual way of naming a place
  may return fewer results than expected.
- Phone-number parsing in `apollo_client.py` is best-effort: Apollo's exact
  async payload shape for a phone result wasn't fully visible in the public
  docs this was written against, so spot-check a small run before relying
  on the Phone column at scale.
- This is scoped to Apollo's own dataset on purpose — see "Why Apollo
  instead of scraping the web" above before extending it to pull from
  anywhere else.

## License

MIT — see [LICENSE](LICENSE).
