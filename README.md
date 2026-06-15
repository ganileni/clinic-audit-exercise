# Clinic audit exercise

## Setup (done in the setup session; here as a recap)
1. Install Claude Code (see the slides).
2. Point it at the shared key shown on screen:
   `export ANTHROPIC_API_KEY=<the key on the slide>`
3. Send the parallel extraction work to the cheap model:
   `export CLAUDE_CODE_SUBAGENT_MODEL=haiku`
4. Download this repo (green "Code" button → Download ZIP), unzip it, then in a
   terminal `cd` into the folder and run `ls` to see the letters.

## Run it
Start Claude Code with Opus directing the work:
`claude --model opus`
Opus prompts the sub-agents and builds the dashboard; the sub-agents read the
letters on the cheap Haiku model (set in step 3).
Then paste the audit prompt (also in `../tools/audit-prompt.txt`):

> Audit every patient in `clinic/` against the standard in `AUDIT.md`. A
> patient's records may be split across more than one file, so gather each
> patient's data from anywhere in the folder. Run the patients in parallel:
> launch one sub-agent per patient. For every value you rely on, keep the exact
> quote and the file it came from.
>
> Write `audit.json`: a list of patients, each with `patient_id`, a `verdict`
> (`at_target` true/false, `breach` true/false, and `rule_broken`), and an
> `evidence` list whose items each have `field`, `value`, `quote` and
> `source_file`. Also write `audit.csv`, one row per evidence item, with columns
> `patient_id,field,value,evidence_quote,source_file`.
>
> Then build a single self-contained `dashboard.html` with one section per
> patient showing the verdict and, for each value, its quote and source letter.

## Challenge
First to produce `dashboard.html` with every treat-to-target breach correctly
flagged wins. Some patients are designed to catch a careless read.
