# Sales Agent

A Claude Code plugin that finds local businesses, qualifies them, and drafts outreach for you.

You describe what you sell and who you want to reach. Sales Agent then:

1. **Finds** matching businesses on Google Maps.
2. **Reads** each business's website and pulls out emails, phones, WhatsApp links and Instagram with plain code, which costs nothing.
3. **Qualifies** each one with Claude Haiku 5.5: name, owner, address, three notes, and whether it fits your criteria.
4. **Pitches** the good fits with Claude Opus 5.5: what they need, your angle, a WhatsApp opener and a short email.

It never sends anything. You review the drafts and send them yourself.

## What it costs to run

From a real run of 20 salons in Dubai:

| Step | Cost |
|---|---|
| 8 Google Maps searches | Inside Google's free monthly allowance (list price about $0.28) |
| Reading and qualifying 20 websites | About $0.006 |
| 5 pitches | About $0.07 |

The same job done by having Claude research each business by hand cost about $0.30 per business.

## Install

In Claude Code:

```
/plugin marketplace add ahmadiqbal-tmitm/sales-agent
/plugin install sales-agent@ahmads-build
```

Then say: **"Set up Sales Agent"**. Claude checks what's missing and walks you through it.

## What you need

- [Claude Code](https://claude.com/claude-code)
- Python 3.10 or newer
- Claude API access from the [Anthropic Console](https://console.anthropic.com) (separate from a Claude subscription)
- A Google Cloud project with **Places API (New)** enabled, and an API key restricted to that API

Your keys stay on your machine. The plugin has no server and sends nothing to its author.

## Use

- "Set up a Sales Agent campaign for my business" – Claude interviews you and writes the four campaign files.
- "Run the salons campaign for 20 leads" – Claude shows the maximum cost, asks for your go-ahead, runs it, and checks the pitches.

Results land in `campaigns/<name>/leads-<date>.csv` in your project folder.

## How a campaign is defined

Four plain-English files in `campaigns/<name>/`:

| File | What goes in it |
|---|---|
| `offer.txt` | Your business, what you sell, true proof points, sender name, tone |
| `searches.txt` | Google Maps searches, one per line |
| `criteria.txt` | Who counts as a good fit |
| `notes.txt` | What the three notes about each lead should cover |

A filled-in example is in `plugins/sales-agent/skills/sales-agent/examples/laundry-for-salons`.

## Good to know

- **No repeats.** Every processed lead is recorded in the campaign's `leads_db.csv` and skipped on later runs.
- **Honest pitches.** The pitch writer may only use facts from your offer file and the lead's own website.
- **Google's terms.** Only place IDs and websites are taken from Google. Everything else comes from each business's own site.
- **Blocked sites.** Websites that block automated visits are marked "check by hand" and left alone.
- **Phone formatting** is tuned for UAE numbers. Other countries work, but phone numbers found in page text may be missed.

## Licence

MIT
