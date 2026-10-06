# Before you send it

## 1. Rank did not come from the rank tool

- **Every position on the page came from an Oxylabs `search_keyword` sample.**
- Nothing from `get_keyword_rank` is presented as a rank — it returns nulls.
- The roster was used only for coverage, and **deduped on (keyword, ASIN)** so
  `_FBM` shadows did not inflate any count.

## 2. The sampling was consistent

- **Same number of pages sampled as the previous run.** A one-page sample
  against a three-page sample is not a comparison.
- `pages_sampled` and `sampled_at` are recorded per keyword and shown on the
  report.
- The page-one boundary used is stated and is **the same as last run**.
- Where the brand was absent, the cell reads "not in sampled pages" with the
  depth — not a zero, not an invented position.

## 3. Sponsored is not organic

- No sponsored placement is recorded as an organic position.
- Terms where the brand appears only in sponsored results are labelled as such.

## 4. The baseline

- The previous run's file is named on the report, with its date.
- **First run:** no movement column at all, and the report says the comparison
  starts next week. No column of zeros.
- **The current positions are written into this run's output** in a form the
  next run can read. Check this — without it the series breaks and cannot be
  recovered.

## 5. Noise is suppressed

- Movements under three positions that did not cross a page boundary are **not
  in the report**.
- Page-boundary crossings are reported regardless of size, and sit above larger
  same-page deltas.
- The count of movements suppressed as noise is stated, so the reader knows the
  filter ran.

## 6. The weighting is labelled

- **"Estimated" appears in the column header and in the summary sentence**, not
  once in a method note.
- The click-share curve is described as an approximation.
- The source of each keyword's weight is stated — SQP, or search terms, or the
  client's own figure.
- Which SQP weeks were present is stated; ingestion is intermittent.

## 7. Non-ranking causes ruled out

- **Every disappearance was checked against stock** before being reported as a
  rank loss.
- Suppression and listing changes were checked where the tool was available.
- Any term whose drop is explained by a stockout is labelled that way and moved
  out of the rank findings.

## 8. Not-tracked coverage

- The count of priority terms with no rank data is stated.
- On early runs this leads the report.

## 9. The report is short

- Six to a dozen lines in the movement sections. Not sixty.
- Both directions are shown.

## 10. Render check

```js
({ overflows: document.documentElement.scrollWidth > document.documentElement.clientWidth,
   sections: document.querySelectorAll('section').length,
   rows: [...document.querySelectorAll('table')].map(t => t.querySelectorAll('tbody tr').length),
   logos: [...document.images].map(i => i.naturalWidth > 0),
   tokens: (document.body.innerHTML.match(/\{\{[A-Z0-9_]+\}\}/g) || []).length,
   // every value figure must be marked estimated
   values: document.querySelectorAll('[data-value-moved]').length,
   estimated: document.querySelectorAll('[data-value-moved][data-estimated]').length,
   // the baseline for next week must be embedded
   baseline: !!document.querySelector('#baseline') })
```

`overflows` false, `logos` all true, `tokens` zero, `values` equal to
`estimated`, and **`baseline` true** — a run that does not save its positions
breaks the series permanently.

## 11. Ship

Save as `<client>-rank-movement-<YYYY-MM-DD>.html`. Weekly, same day each week.

**Keep every file.** This is the only rank history the account will ever have,
and the report is only half the deliverable — the saved baseline is the other
half.
