# Build the audit review dashboard

This is a separate task, run **in parallel with the audit**: you do not need to
wait for `audit.json` to exist. Everything needed to build the page is the
**schema and clinical meaning defined in `AUDIT.md`** (read it) plus the letters
in `clinic/`, which are already present. Write one self-contained `dashboard.html`
that renders an `audit.json` of that schema once it is loaded.

Self-contained means no external files and no internet: embed everything the page
needs, **including the text of the letters in `clinic/`**, so a cited source can
be opened from within the page offline.

## Loading the data
The audit writes `audit.json` to this directory when it finishes; the page loads
it at runtime, not at build time:
- On open, try to load `./audit.json` automatically.
- A double-clicked local file cannot always read a sibling file, so also offer a
  **file picker / drag-and-drop** to select `audit.json`. Both paths must work
  with no server and no internet.
- Until data is loaded, show a short "waiting for `audit.json`" state; render as
  soon as it is provided, and validate it against the schema in `AUDIT.md`,
  flagging anything missing or malformed.

## Main view
- A table with one row per patient: patient id, the verdict (at target / breach /
  needs review), and a marker for patients flagged during review. Make breach and
  needs-review rows stand out.
- A few aggregate statistics above the table: how many patients are at target,
  how many breach, how many need review, and how many are flagged.
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
