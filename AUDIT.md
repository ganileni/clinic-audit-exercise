# Treat-to-target audit standard (synthetic teaching data)

You are auditing rheumatoid arthritis outpatient records against the
treat-to-target standard.

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
- A patient's records may be split across more than one document, so gather all
  of a patient's data from anywhere in `clinic/` before judging.
- Run the patients in parallel: launch one sub-agent per patient. Use only
  Haiku sub-agents for this per-patient work.
- For every value you rely on, keep the exact quote and the file it came from.

## What to produce
1. `audit.json` — a list of patients, each with:
   - `patient_id`
   - `verdict`: `at_target` (true/false), `breach` (true/false), and `rule_broken`
   - `evidence`: a list whose items each have `field`, `value`, `quote` and `source_file`
2. `audit.csv` — one row per evidence item, with columns
   `patient_id,field,value,evidence_quote,source_file`
3. `dashboard.html` — a single self-contained page, one section per patient,
   showing the verdict and, for each value, its quote and source letter.
