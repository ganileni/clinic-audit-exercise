# Treat-to-target audit standard (synthetic teaching data)

You are auditing rheumatoid arthritis outpatient records against the
treat-to-target standard. The records are synthetic teaching data.

## Target
A patient is **at target** if their DAS28 (Disease Activity Score, 28 joints)
is 3.2 or below (remission < 2.6; low disease activity 2.6–3.2).
Above 3.2 is **not at target**.

## The rule, per patient
1. Find the patient's current DAS28. If it is not stated but the components are
   (tender joints, swollen joints, patient global, CRP or ESR), treat the
   patient as not at target if those components clearly indicate active disease.
2. If the patient is not at target, check the plan:
   - Was treatment **escalated**? Escalation = increasing a conventional DMARD
     dose (e.g. methotrexate), adding a second conventional DMARD
     (sulfasalazine, hydroxychloroquine, leflunomide), starting or switching a
     biologic or JAK inhibitor, or a short steroid bridge.
   - Or was there a **documented reason not to escalate**? Valid reasons:
     active or recent infection, malignancy, planned surgery, pregnancy or
     planning, the patient declines, a significant comorbidity.
3. **Breach** = not at target AND not escalated AND no documented reason.

Changing folic acid, or adding a painkiller, is **not** an escalation.

## How to run the audit
Work one patient at a time, in this order:

1. **Gather the evidence.** A patient's records may be split across more than
   one document, so collect all of a patient's data from anywhere in `clinic/`
   first. For every value you will rely on, keep the **exact quote** and the
   **file it came from**.
2. **Reason over the evidence.** Decide whether the patient is at target and, if
   not, whether the plan escalated or recorded a reason not to. Your reasoning
   must rest on the evidence you gathered, and cite it.
3. **Give the verdict and a short motivation.** State at-target / breach, with
   one or two sentences explaining how the evidence leads there.

Run the patients in parallel: launch **one sub-agent per patient**, each doing
the three steps above for its patient.

Caveats to watch:
- Some letters state the components but no DAS28 score; judge activity from the
  components (step 1).
- Some patients' records are scattered across more than one file, so the
  evidence for a single patient may come from different `source_file`s.
- The output is for a clinician to check, not to trust blindly: every value must
  be traceable to its quote and source.

## What to produce
Write a single file, `audit.json`, with this shape:

```json
{
  "patients": [
    {
      "patient_id": "P07",
      "verdict": {
        "at_target": false,
        "breach": true,
        "reasoning": "One or two sentences: the conclusion and the evidence it rests on."
      },
      "evidence": [
        {"field": "das28_current", "value": "4.6",
         "quote": "DAS28 today 4.6", "source_file": "clinic/P07-clinic.txt"},
        {"field": "treatment_change", "value": "folic acid increased; analgesia added",
         "quote": "increase folic acid, add co-codamol", "source_file": "clinic/P07-clinic.txt"}
      ]
    }
  ]
}
```

- The top level is an object with a single key `patients` (a list), **not** a
  bare list.
- `verdict.reasoning` is required and must not be empty: it is the motivation a
  clinician reads, and it must cite the evidence below it.
- Each `evidence` item needs a non-empty `field`, `value`, `quote` and
  `source_file`.

Use only these `field` names:
- `das28_current` — the current DAS28 score, when the letter states one.
- `das28_components` — the joint counts / CRP / ESR / patient global, used when
  no DAS28 score is stated.
- `treatment_change` — what the plan did this visit (an escalation, a
  non-escalating change such as folic acid, or no change).
- `documented_reason` — a documented reason not to escalate, included only when
  the letter records one.
- `das28_previous` — an earlier DAS28, only if you use a trend in your reasoning.

For every patient, include at least one disease-activity item (`das28_current`
or `das28_components`) and one `treatment_change` item. Where there is no
documented reason, say so in `reasoning` rather than adding an empty row.

The dashboard is built from `audit.json` as a separate step; see `DASHBOARD.md`.

## Worked example (not one of the clinic patients)
Mrs A's letter says: *"DAS28 today 4.1. Continue methotrexate, increase folic
acid to 5 mg daily. Review in 3 months."* No escalation, and no reason not to
escalate is recorded.

- **Gather:** DAS28 4.1 (quote "DAS28 today 4.1"); plan = increase folic acid
  only (quote "increase folic acid to 5 mg daily").
- **Reason:** 4.1 is above the 3.2 target, so not at target. Folic acid is not
  an escalation, and no reason to defer treatment is documented.
- **Verdict:** breach.

That patient in `audit.json`:

```json
{
  "patient_id": "EXAMPLE",
  "verdict": {
    "at_target": false,
    "breach": true,
    "reasoning": "DAS28 4.1 is above the 3.2 target; the only change was folic acid, which is not an escalation, and no reason to defer is documented."
  },
  "evidence": [
    {"field": "das28_current", "value": "4.1", "quote": "DAS28 today 4.1", "source_file": "clinic/EXAMPLE.txt"},
    {"field": "treatment_change", "value": "folic acid increased", "quote": "increase folic acid to 5 mg daily", "source_file": "clinic/EXAMPLE.txt"}
  ]
}
```

## Going deeper
For how to write prompts like this well, Anthropic's interactive guide is
excellent: https://github.com/anthropics/prompt-eng-interactive-tutorial
