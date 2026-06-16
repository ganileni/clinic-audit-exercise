# Treat-to-target audit standard (synthetic teaching data)

You are auditing rheumatoid arthritis outpatient records against the
treat-to-target standard. The records are synthetic teaching data.

## Target
A patient is **at target** if their DAS28-CRP (Disease Activity Score across 28
joints, using CRP as the inflammation marker) is 3.2 or below (remission < 2.6;
low disease activity 2.6–3.2). Above 3.2 is **not at target**. Every letter here
reports DAS28-CRP; the CRP- and ESR-based versions are not interchangeable (the
CRP-based score reads slightly lower near the 3.2 cut-off), so do not mix them.

This is a **simplified rule for teaching**, not the full EULAR treat-to-target
standard. In real practice a single above-target score at one visit is not by
itself a breach: treat-to-target is about not *sustaining* high disease activity
without acting. Apply the simplified rule below, but flag genuinely borderline or
ill-fitting cases for review rather than forcing a verdict (see "When the rule
does not fit").

## The rule, per patient
1. Find the patient's current DAS28-CRP. If it is not stated but the components
   are (tender joints, swollen joints, patient global, CRP), treat the
   patient as not at target if those components clearly indicate active disease.
2. If the patient is not at target, check the plan:
   - Was treatment **escalated**? Escalation = increasing a conventional DMARD
     dose (e.g. methotrexate), adding a second conventional DMARD
     (sulfasalazine, hydroxychloroquine, leflunomide), starting or switching a
     biologic or JAK inhibitor, or a short **systemic** steroid bridge (an oral
     taper or an intramuscular depot).
   - Or was there a **documented reason not to escalate**? Valid reasons:
     active or recent infection, malignancy, planned surgery, pregnancy or
     planning, a shared decision or documented patient preference not to
     escalate, a significant comorbidity.
3. **Breach** = not at target AND not escalated AND no documented reason.

Changing folic acid, adding a painkiller, or an **intra-articular (single-joint)
steroid injection** for symptom relief is **not** an escalation.

**When the rule does not fit:** do not force a breach-or-not verdict when either
(a) the situation is **outside** what these rules describe — for example a
clinical-trial or investigational agent — or (b) the rule is in scope but
genuinely **ambiguous** here, because a fact the verdict depends on is missing or
because reasonable clinicians would read it two ways. A common ambiguous case:
the patient is above target with no change at this visit, but treatment was
**escalated at a recent prior visit** and is being held to judge the response
(for example a biologic started a few weeks ago). The simplified rule would read
"no escalation this visit" as a breach, yet holding a recently started drug to
assess it is normal care — so the rule does not really fit. In all such cases
mark the patient **needs review** and explain why, so a clinician decides.
Flagging honestly is better than guessing.

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
- Some letters state the components but no DAS28-CRP score; judge activity from
  the components (step 1).
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
        "needs_review": false,
        "reasoning": "One or two sentences: the conclusion and the evidence it rests on."
      },
      "evidence": [
        {"field": "das28_current", "value": "4.6",
         "quote": "DAS28-CRP today 4.6", "source_file": "clinic/P07-clinic.txt"},
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
- `verdict.needs_review` is `true` only when the case is outside or ill-fitting
  the rules (see "When the rule does not fit" above); then leave `breach` `false`
  and use `reasoning` to say what a clinician should check. A verdict is never
  both a breach and needs review.
- Each `evidence` item needs a non-empty `field`, `value`, `quote` and
  `source_file`.

Use only these `field` names:
- `das28_current` — the current DAS28-CRP score, when the letter states one.
- `das28_components` — the joint counts / CRP / patient global, used when
  no DAS28-CRP score is stated.
- `treatment_change` — what the plan did this visit (an escalation, a
  non-escalating change such as folic acid, or no change).
- `documented_reason` — a documented reason not to escalate, included only when
  the letter records one.
- `das28_previous` — an earlier DAS28-CRP, only if you use a trend in your reasoning.

For every patient, include at least one disease-activity item (`das28_current`
or `das28_components`) and one `treatment_change` item. Where there is no
documented reason, say so in `reasoning` rather than adding an empty row.

## Worked example (not one of the clinic patients)
Mrs A's letter says: *"DAS28-CRP today 4.1. Continue methotrexate, increase folic
acid to 5 mg daily. Review in 3 months."* No escalation, and no reason not to
escalate is recorded.

- **Gather:** DAS28-CRP 4.1 (quote "DAS28-CRP today 4.1"); plan = increase folic
  acid only (quote "increase folic acid to 5 mg daily").
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
    "needs_review": false,
    "reasoning": "DAS28-CRP 4.1 is above the 3.2 target; the only change was folic acid, which is not an escalation, and no reason to defer is documented."
  },
  "evidence": [
    {"field": "das28_current", "value": "4.1", "quote": "DAS28-CRP today 4.1", "source_file": "clinic/EXAMPLE.txt"},
    {"field": "treatment_change", "value": "folic acid increased", "quote": "increase folic acid to 5 mg daily", "source_file": "clinic/EXAMPLE.txt"}
  ]
}
```

## Out-of-scope example (not one of the clinic patients)
Mr B's letter says: *"DAS28-CRP 5.2. Enrolled in a trial, started a blinded
investigational agent."* He is not at target, but a blinded trial agent is not
something the rules can classify as an escalation or not.

- **Verdict:** needs review. Do not guess a breach; flag it for a clinician.

```json
{
  "patient_id": "EXAMPLE-2",
  "verdict": {
    "at_target": false,
    "breach": false,
    "needs_review": true,
    "reasoning": "DAS28-CRP 5.2 is above target, but the plan is a blinded trial agent the rules cannot classify as an escalation; flagging for clinical review."
  },
  "evidence": [
    {"field": "das28_current", "value": "5.2", "quote": "DAS28-CRP 5.2", "source_file": "clinic/EXAMPLE-2.txt"},
    {"field": "treatment_change", "value": "enrolled in trial; blinded investigational agent", "quote": "started a blinded investigational agent", "source_file": "clinic/EXAMPLE-2.txt"}
  ]
}
```

## Ambiguous example (not one of the clinic patients)
Mr C's letter says: *"DAS28-CRP 4.3, down from 5.8 four weeks ago when adalimumab
was started. Continue, too early to judge response. Review in 8 weeks."* He is
above target with no change this visit, but he was escalated four weeks ago and
is being held to assess the new biologic.

- **Verdict:** needs review. The simplified rule would read "no escalation this
  visit" as a breach, but holding a recently started biologic to judge response
  is normal care; the rule does not fit, so flag it rather than recording a
  breach.

```json
{
  "patient_id": "EXAMPLE-3",
  "verdict": {
    "at_target": false,
    "breach": false,
    "needs_review": true,
    "reasoning": "DAS28-CRP 4.3 is above target, but adalimumab was started four weeks ago and is being held to judge response; the simplified 'no change this visit' rule does not fit recent-escalation care, so flagging for clinical review."
  },
  "evidence": [
    {"field": "das28_current", "value": "4.3", "quote": "DAS28-CRP 4.3", "source_file": "clinic/EXAMPLE-3.txt"},
    {"field": "das28_previous", "value": "5.8", "quote": "down from 5.8 four weeks ago when adalimumab was started", "source_file": "clinic/EXAMPLE-3.txt"},
    {"field": "treatment_change", "value": "no change this visit; adalimumab started 4 weeks ago", "quote": "Continue, too early to judge response", "source_file": "clinic/EXAMPLE-3.txt"}
  ]
}
```

## Extra: have the audit check its own output
After writing `audit.json`, build a small script that validates it against the
schema above — the `patients` wrapper; every verdict has `at_target`, `breach`,
`needs_review` and a non-empty `reasoning`, with no verdict both a breach and
needs review; every evidence item has a non-empty `field` (from the allowed
names), `value`, `quote` and `source_file`; and every patient has a
disease-activity item and a `treatment_change` item. Run it and report whether
`audit.json` passes.
