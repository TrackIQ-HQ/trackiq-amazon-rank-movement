---
name: trackiq-amazon-rank-movement
description: Tracks weekly organic rank movement across a brand's priority keyword set by sampling live search results, compares it to the previous run, and weights every gain and loss by what that keyword is worth in revenue so the drops that cost real money surface first and the rest stay quiet. Use when the user asks about rank movement, organic rank changes, what moved this week, keyword rank tracking, did we lose rank, ranking report, or what a rank drop cost.
---

# Organic Rank Movement Tracker

`trackiq-category-priority-keywords` produces the list of terms worth ranking
for. On its first real run, **103 of 131 priority terms were not in the rank
tracker at all.** That list is worthless unless something watches it afterwards.

**What moved this week, and what did the drop cost?**

Output is a branded HTML report: the movements that matter, ranked by revenue
weight, and a saved baseline for next week.

## Requires

- **The Oxylabs scraper**, for `search_keyword` — this is where rank actually
  comes from. One credit per keyword per run.
- The TrackIQ MCP, for `list_marketplaces`, `get_search_query_performance` (to
  weight each term) and `get_keyword_rank` (for the tracked roster, **not** for
  ranks).
- **The previous run's file.** Movement needs a baseline and no tool stores one.
- Nothing else. No filesystem, no shell.
- **Without Oxylabs:** there is no rank source. Say so rather than presenting the
  roster as ranks.

## First run

Fill in a copy of `assets/account.example.md` saved as account.md beside the
skill. Every TrackIQ skill reads the same file, so an account already set up
for another TrackIQ report needs nothing added here.

If the runtime has no filesystem, print the same block and ask the user to
paste it into their project instructions once.

## Read first

- `assets/pulls.md` — where rank comes from, and why not from the obvious tool
- `assets/method.md` — the revenue weighting and what counts as a movement
- `assets/checks.md` — what to verify before anything is sent

Copy `assets/report-template.html` and replace every `{{TOKEN}}`.

## Non-negotiables

1. **`get_keyword_rank` does not return ranks.** On the account this was built
   against, `organic_rank` and `sponsored_rank` were **null on 100 of 100
   rows**, and every row carried the same `tracked_date` whatever date range was
   requested. It is a **roster of tracked keywords**, not a time series.
   **Never present it as rank data.**
2. **Rank comes from sampling `search_keyword`.** One credit per keyword per
   run. Thirty keywords weekly is thirty credits a week — agree the set with the
   client before committing to it.
3. **Movement needs the previous run's file.** There is no rank history
   anywhere. First run: record the positions, say the comparison starts next
   week, and show no movement column. Do not show zeros.
4. **Dedupe the roster.** `get_keyword_rank` returns one row per SKU, so `_FBM`
   shadows duplicate every keyword and ASIN pair. Dedupe on (keyword, ASIN)
   before counting anything.
5. **Weight every movement by revenue.** A drop from 4 to 9 on a term worth
   $18,000 a month is the report; a drop from 40 to 60 on a term worth $30 is
   noise and should stay quiet. The weighting is in `assets/method.md`.
6. **A single sample is not a rank.** Search results vary by location,
   personalisation and time of day. Treat a movement of fewer than three
   positions as noise unless it crosses a page boundary.
7. **Page boundaries matter more than positions.** Falling from 9 to 11 crosses
   off page one and costs far more than falling from 3 to 5. Flag boundary
   crossings explicitly.
8. **Say which terms are not being tracked.** Comparing the priority keyword
   list to the roster is a finding in its own right, and on the first real
   account it was the biggest one.
9. **Sponsored position is not organic rank.** If a term only shows the brand in
   sponsored results, the organic rank is "not in the sampled pages", not zero.
10. **Never print `account_id`.**

## What it pairs with

`trackiq-category-priority-keywords` defines the set. `trackiq-share-of-shelf`
measures the same page from the other direction — how much of it we hold rather
than where one ASIN sits. `trackiq-rank-readiness` decides which terms deserve a
push; this one says whether the push held.

## Delivery

The output is produced in the chat first. Delivery is the last step and the
method comes from the Delivery block in account.md — never ask per run.

| Method | What to do | Needs |
|---|---|---|
| `in-chat` | Return the report. The default, and the fallback for every other method. | nothing |
| `file` | Write it beside the skill, dated. | a filesystem |
| `slack` | Post the headline findings as text, then upload the file. | a connected Slack tool |
| `n8n` | POST it to the configured webhook. | network access |
| `email` | Hand it to the connected mail tool. | a connected mail tool |

Confirm before the first outward send of a session, fall back to in-chat
loudly when a method is unavailable, and never substitute a different
outward channel.

## Version

`trackiq-amazon-rank-movement` v1.0.1 (2026-10-06).

If the user asks whether this skill is current, fetch
`https://trackiq.com/skills/registry.json`, compare the `version` field for
`trackiq-amazon-rank-movement`, and if it is newer, give them the download link and the
one-line changelog. Do not fetch at any other time.
