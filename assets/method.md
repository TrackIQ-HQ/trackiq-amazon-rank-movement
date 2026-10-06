# Method

## What counts as a movement

A single SERP sample is not a rank. Results vary by location, personalisation
and time of day, and a two-position wobble is the instrument, not the world.

```
delta = previous_position - current_position       # positive = improved
```

| Test | Treatment |
|---|---|
| `abs(delta) < 3` and no page change | **noise.** Record it, do not report it. |
| crossed a page boundary | **report it**, however small the delta |
| `abs(delta) >= 3` | report it |
| appeared from nothing | report it |
| disappeared from the sampled pages | **report it, loudly** |

**Page boundaries matter more than positions.** Falling from 9 to 11 crosses off
page one and costs far more traffic than falling from 3 to 5. Flag boundary
crossings explicitly and put them above larger deltas that stayed on the same
page.

Page one is positions 1–16 or so on desktop and varies. State the boundary used
and keep it the same between runs, or every week will show phantom crossings.

## The revenue weighting

The whole point is to stop a report full of movements nobody should care about.

```
weight            = estimated keyword revenue per month (see assets/pulls.md)
position_value[p] = share of clicks at position p
value_moved       = weight x (position_value[new] - position_value[old])
```

A simple, defensible click-share curve:

```
position_value[p] = 1 / (p ^ 0.8)          # normalised across the page
```

It is an **approximation and must be labelled one.** Real click distributions
vary by category and by how many sponsored slots sit above the fold. It is used
to order the list, not to bill anyone.

Rank the report by `abs(value_moved)`, descending. A drop from 4 to 9 on an
$18,000 term lands above a drop from 40 to 60 on a $30 term, which is the right
way round and is not what a position-sorted report does.

## Estimated, everywhere

`value_moved` is built on two derived numbers — an estimated keyword revenue and
an approximated click curve. **Say "estimated" every time it appears**, in the
column header and in the summary sentence, not once in a method note.

A client who quotes "we lost $4,100 of rank this week" to their board is
quoting something this data cannot support. The figure orders the list; it does
not price the damage.

## The four sections

**1. Off page one.** Terms that crossed the boundary in either direction. The
most consequential section and usually the shortest.

**2. Big movers by value.** Ranked by `abs(value_moved)`. Both directions —
a report that only shows losses gets read as pessimism.

**3. Disappeared.** Terms where the brand is no longer in the sampled pages.
Check each against stock and listing status before calling it a rank loss: an
out-of-stock or suppressed ASIN falls out of results and that is not an SEO
problem. `trackiq-restock-priority` and `trackiq-listing-monitor` answer it.

**4. Not tracked.** Priority terms with no rank data at all. On the first real
account this was 103 of 131 terms. Lead with it on the first few runs; it is the
finding that changes what the client does next.

Everything else stays quiet. A weekly report with six lines gets read every
week; one with sixty gets read once.

## Before blaming rank

A term that dropped may have dropped for a reason that is not about ranking:

| Check | Tool |
|---|---|
| Out of stock | `get_inventory_snapshot` — cover per ASIN |
| Listing suppressed or changed | `trackiq-listing-monitor` |
| Ads stopped | `get_product_ads` — sponsored presence supports organic |
| Price moved materially | `get_product` |

Run at least the stock check before reporting any disappearance. A stockout that
gets reported as a rank collapse sends the client to the wrong meeting.

## Saving the baseline

Every run writes the positions into its own output so the next run can read
them. This is the only rank history that will ever exist for this account —
**the file is the product**, as much as the report is.

Keep every week's file. The value compounds; a single week's movement says
little and twelve weeks says everything.

## What this skill does not do

- **No rank from the MCP.** `get_keyword_rank` has null ranks and no history.
- **No competitor rank tracking.** Possible with more credits; scope separately.
- **No causal claim.** It reports movement and rules out the obvious
  non-ranking causes. It does not explain why Amazon re-ordered a page.
- **No prediction.**
