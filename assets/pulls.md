# The pull sequence

## 0. Account and the keyword set

`list_marketplaces` first. Never print `account_id`.

The keyword set comes from, in order of preference:

1. `trackiq-category-priority-keywords` output — terms chosen for volume and
   relevance, with revenue attached
2. the client's own list
3. the tracked roster from `get_keyword_rank`

Twenty to forty terms. **One Oxylabs credit per keyword per run**, so thirty
terms weekly is thirty credits a week, about 1,560 a year. Agree it before
committing.

## 1. The roster — useful, but not for ranks

```
get_keyword_rank(account_id, start_date, end_date, limit=500)
```

Returns per row: `keyword`, `asin`, `sku`, `title`, `link`, `ownership`,
`tracked_date`, `organic_rank`, `sponsored_rank`, `is_amazons_choice`.

**The rank fields are empty.** On the account this was built against:

- `organic_rank` null on **100 of 100** rows
- `sponsored_rank` null on **100 of 100** rows
- `is_amazons_choice` 0 on every row
- **one distinct `tracked_date`** across a request spanning three and a half
  months — the latest, regardless of the range asked for

So it is a **roster**: which keywords are being tracked, against which ASINs.
That is genuinely useful and it is the only thing it is.

**Dedupe it.** Rows are per SKU, so `_FBM` shadow SKUs duplicate every keyword
and ASIN pair. Dedupe on `(keyword, asin)` before counting.

### The coverage finding

Compare the priority keyword list against the roster:

```
untracked = priority terms not present in the roster
```

On the first real account, **103 of 131 priority terms were not tracked at
all.** That is a finding worth leading with — a brand cannot react to rank
movement on terms nothing is watching.

## 2. Rank — from Oxylabs

```
for each keyword:
    search_keyword(query=<term>)          # 1 credit
```

Find the brand's ASINs in the organic results and record the position.

**Sample the same way every time.** Same number of pages, same time of day if
possible, same marketplace. A comparison between a one-page sample and a
three-page sample is not a movement.

Record, per keyword:

```
keyword, asin, organic_position, page, sponsored_present,
sampled_at, pages_sampled
```

Where the brand does not appear in the sampled pages, record **"not in sampled
pages"** with the page depth — not a zero, and not a guess at position 300.

### Sponsored is not organic

If the brand appears only in sponsored results, the organic rank is *not in the
sampled pages*. Recording a sponsored placement as rank makes a paid position
look like an earned one, and the whole point of the report is to tell them
apart.

## 3. The revenue weight

```
get_search_query_performance(account_id, start_date, end_date, limit=300)
```

Per query: impressions, clicks, cart adds and purchases at query level.

**SQP is weekly, Sunday to Saturday, and ingestion is intermittent** — missing
weeks inside a month are normal and not an error. Say which weeks were present.

There is **no revenue field** in SQP. Derive a weight:

```
weight = query purchases x average selling price of the ASINs that rank for it
```

Take the ASP from `get_product_performance` (revenue / units). Label the result
**estimated keyword revenue** everywhere it appears — it is a derived figure,
not a reported one.

Where a term has no SQP row, fall back to `get_search_terms` purchases if the
brand advertises on it, and say which source each weight came from.

## 4. The previous run

**Ask for the previous report's file.** There is no rank history in any tool and
this skill is the only thing that will ever have one.

Save into every run's output, in a form the next run can read:

```
keyword, asin, organic_position, page, sampled_at, pages_sampled, weight
```

Without a previous file: record positions, **show no movement column**, and say
the comparison starts next week. Do not display zeros — a column of zeros reads
as "nothing moved".
