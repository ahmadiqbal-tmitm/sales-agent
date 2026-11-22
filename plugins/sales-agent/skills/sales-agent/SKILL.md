---
name: sales-agent
description: Find, qualify and pitch business leads with the Sales Agent script (Google Places, then Claude Haiku 5.5, then Claude Opus 5.5). Use when the user asks to find leads, prospects or target businesses, run or set up a lead or outreach campaign, add a new client or industry to target, or draft outreach for leads.
---

# Sales Agent

The work is done by `scripts/sales_agent.py` in this skill's folder. Your job is to set up
campaigns, run the script, and report results. Do not research leads yourself with web search or
subagents: that is the expensive path this script replaces. Reading a few pages to answer a
question is fine.

Below, `SA` means: `<python> <this skill's folder>/scripts/sales_agent.py`, run from the user's
project folder. Campaign data is written to `./campaigns` in that folder, never into the plugin.

## First-time setup

Check each item and only walk the user through the ones that are missing. Never ask the user to
paste a key into the chat, and never print a key's value.

1. **Python 3.10 or newer.** Look for `python3.12`, `python3.11`, `python3.10` or a `python3`
   that reports 3.10+. If none: on a Mac, `brew install python@3.12`; on Windows, install from
   python.org.
2. **A project-only Python environment** in the user's project folder:
   `python3.12 -m venv .sales-agent-venv` then `.sales-agent-venv/bin/pip install anthropic`.
   Use `.sales-agent-venv/bin/python` as `<python>` from then on.
3. **Claude API access.** Either `ant auth login` (Anthropic's sign-in tool, no key to store) or
   an `ANTHROPIC_API_KEY` environment variable. A Claude subscription does not include API
   access; the user needs billing set up in the Anthropic Console.
4. **A Google Maps key** in the `GOOGLE_MAPS_API_KEY` environment variable, from a Google Cloud
   project with **Places API (New)** enabled. Tell the user to restrict the key to that one API
   and to set a daily quota on Text Search requests.

If a key was added to the shell profile after this session started, load it for a command with
`zsh -ic '<command>'` (or the user's shell) instead of asking them to restart.

## Campaigns

A campaign is one client plus one kind of target, in `campaigns/<name>/`:
- `offer.txt`: the client's business, sender name and tone. Only true facts.
- `searches.txt`: one Google Maps search per line, specific about area.
- `criteria.txt`: who counts as a good fit.
- `notes.txt`: what the 3 notes about each lead should cover.
- `leads_db.csv`: every lead already processed. Never edit or delete it without asking.

`examples/laundry-for-salons/` in this skill's folder is a filled-in campaign to copy the style of.

## New campaign

1. Run `SA new --campaign <short-name>`.
2. Interview the user for the offer: what they sell, to whom, the area served, true proof
   points, sender name and tone. Write `offer.txt` from their answers only. Never invent proof
   points, prices or client names.
3. Draft `searches.txt`, `criteria.txt` and `notes.txt` from the offer and target. Show them to
   the user and adjust until they agree.
4. If the user already has leads for this campaign (a CSV or a Google Sheet with a website
   column), export them to CSV and run `SA seed --campaign <name> <file.csv>` so they are skipped.

## Running a campaign

1. Run `SA find --campaign <name> --dry-run` and show the user the searches and the maximum
   number of Google requests.
2. Confirm the run with the user, including `--max-leads` (start with 20 on a new campaign) and
   whether to pitch `yes` only or `yes,unclear`. Each run spends money on Google and Anthropic.
3. Run `SA pipeline --campaign <name> --pages 1 --max-leads <n>` in the background; large runs
   take several minutes. `--pages` sets how many pages of 20 results each search fetches (1 to 3).
4. Report from the script's printed summary: leads found, contact fields filled, fit counts,
   pitches written, and the Claude cost line. Name the output file `campaigns/<name>/leads-*.csv`.
5. Spot-check 3 pitches against their lead facts before calling the run good. Flag any pitch that
   states something not in `offer.txt` or the lead's notes.
6. Before adding results to a sheet the user already keeps, compare business names against it:
   the script matches on website and Google place ID, so the same business under a different
   website can slip through.

## Rules

- Rows marked "check by hand" are sites that block automated visits. Leave them for the user; do
  not try to get around the block.
- Google's terms limit storing business names and addresses taken from Google Maps. The script
  only takes place IDs and websites from Google; keep it that way.
- Never send outreach. The script drafts messages; the user reviews and sends them.
