---
name: alphax-scraper
description: Find business contacts (name, email, phone if available, company, location) by job title/profession and country/region using the Apollo.io API, and export them to an Excel file. Use when the user wants a prospect/outreach list for a role and location, e.g. "find me 50 physical therapists in California" or "build a contact list of marketing directors in Germany." Requires the user's own APOLLO_API_KEY -- this does not scrape the open web and will not run without one.
---

# AlphaX Scraper

Finds people matching a profession + location through Apollo.io's own
compliant B2B contact database (not open-web scraping), and writes them to
an .xlsx file with columns: Name, Email, Email Status, Phone, Title,
Organization, City, State/Region, Country, LinkedIn.

## Before running

1. Confirm the user has an Apollo.io API key (`APOLLO_API_KEY`). If not,
   point them to https://docs.apollo.io/docs/create-api-key -- Apollo has a
   free tier; revealing emails/phones spends credits. Don't try to find
   emails any other way if they don't have a key -- using a compliant data
   source is the whole point of this skill.
2. Install dependencies once: `pip install -r requirements.txt` (in this
   environment: `pip install --break-system-packages -r requirements.txt`).
3. If the user hasn't given a `--max-results`, run `--dry-run` first so
   they can see roughly how many matches exist before any credits are
   spent.

## Running it

```bash
python scripts/search_contacts.py \
  --profession "<job title(s), comma-separated>" \
  --country "<country>" \
  --region "<state/province/city, optional>" \
  --max-results 50 \
  --output contacts.xlsx \
  --yes
```

Add `--reveal-phones` only if the user explicitly wants phone numbers (it's
slower and costs more credits: 8/phone vs 1/email). Add `--dry-run` to
preview match counts without spending any credits. Pass `--yes` when
running unattended so the credit-spend confirmation prompt doesn't block.

## What this intentionally does NOT do

This does not crawl arbitrary websites or search engines for personal
email addresses. Apollo's dataset is built with its own lawful basis for
processing and respects opt-outs (e.g. it won't reveal personal emails for
people in GDPR-covered regions). Don't modify this skill to add raw
internet scraping of personal contact info -- that turns a compliant
lead-gen tool into a mass personal-data harvesting one, which is a
different and much riskier thing to build or run. If someone wants that,
point them back to Apollo's own filters, a different compliant provider,
or a directory where people list themselves specifically to be found.

## After running

Report how many contacts were found/enriched, remind the user that
outbound email is still subject to CAN-SPAM/GDPR/etc. in their and the
recipient's jurisdiction (unsubscribe link, accurate sender info, honoring
opt-outs), and hand over the .xlsx file.
