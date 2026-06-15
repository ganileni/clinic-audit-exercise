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

A patient's information may be spread across more than one document; gather all
of it before judging.
