# Clinic audit project

This folder contains synthetic rheumatoid arthritis outpatient records in
`clinic/` (no real patients). The audit standard is in `AUDIT.md`.

When asked to run the audit:
- Treat each patient as an independent job. A patient's data may be split
  across more than one file, so gather all of a patient's documents from
  anywhere in `clinic/` before judging.
- Run the patients in parallel: launch one sub-agent per patient.
- For every value you rely on, record the exact quote and the file it came
  from.

Write two outputs:
1. `audit.json`:
   {
     "patients": [
       {"patient_id": "...",
        "verdict": {"at_target": bool, "breach": bool, "rule_broken": "..."},
        "evidence": [
          {"field": "...", "value": "...", "quote": "...", "source_file": "..."}
        ]}
     ]
   }
2. `audit.csv`: one row per evidence item, columns
   patient_id,field,value,evidence_quote,source_file
