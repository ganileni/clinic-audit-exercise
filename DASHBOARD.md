# Build the audit review dashboard

This is a separate task, run alongside the audit. The data you work from and its
clinical meaning are defined in `AUDIT.md` — read it for the shape of
`audit.json` and the context. Read `audit.json` (and the letters in `clinic/`
that it cites), then write one self-contained `dashboard.html`.

Self-contained means no external files and no internet: embed everything you
need, **including the text of the cited letters**, so the page works offline and
a source can be opened from within it.

## Main view
- A table with one row per patient: patient id, the verdict (at target / breach),
  and a marker for patients flagged during review.
- A few aggregate statistics above the table: how many patients are at target,
  how many breach, and how many are flagged.
- Clicking a row opens that patient's page.
- A **Review mode** button steps through patients one page at a time so the
  reviewer can flag the ones with problems. Flags are reflected back in the
  table, and persist across a page reload (store them in the browser).

## Patient page
- The patient id and the verdict.
- The reasoning / motivation behind the verdict (from `verdict.reasoning`); it
  cites the evidence.
- The evidence itself: each value with its `field`, the value, the **exact
  quote**, and the **source file**. Clicking a source opens the cited letter
  (its text is embedded in the page) so the reviewer can check the quote in
  context.
- A control to flag this patient (used by Review mode) and a way back to the
  table.

The point of the page is that a clinician can trace every value back to its
letter, so always show the quote and the source next to the value.
