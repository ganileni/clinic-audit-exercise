# Clinic audit exercise

## Setup (done in the setup session; here as a recap)
1. Install Claude Code (see the slides).
2. Point it at the shared key shown on screen:
   `export ANTHROPIC_API_KEY=<the key on the slide>`
3. Send the parallel extraction work to the cheap model:
   `export CLAUDE_CODE_SUBAGENT_MODEL=haiku`
4. Download this repo (green "Code" button → Download ZIP), unzip it, then in a
   terminal `cd` into the folder and run `ls` to see the letters.

Everything you need to type or paste (commands, both prompts, the links) is in
`copypaste.txt`, and a PDF of the slides is in `slides.pdf`.

## Run it
Start Claude Code with Opus directing the work:
`claude --model opus`
Opus prompts the per-patient sub-agents, which read the letters on the cheap
Haiku model (set in step 3).

The audit rules, the output schema, and a worked example all live in `AUDIT.md`;
the prompt you paste just points at it. Paste the audit prompt (also in
`copypaste.txt`):

> Follow the instructions in `AUDIT.md`. The patient letters are in `clinic/`.
> Write your output to `audit.json` in this directory.

The dashboard runs **in parallel** with the audit: it is built from the schema in
`AUDIT.md`, so you can send it straight after the audit prompt, in a second agent
(also in `copypaste.txt`). The page loads `audit.json` once the audit has
written it.

> Follow the instructions in `DASHBOARD.md`. Build `dashboard.html` in this
> directory now; it loads `audit.json` (written by the audit) at runtime, so you
> do not need to wait for the audit to finish.

## Challenge
First to produce a correct `audit.json` (every treat-to-target breach correctly
flagged) wins. Some patients are designed to catch a careless read.

## Going deeper
- Anthropic's interactive prompt-engineering guide:
  https://github.com/anthropics/prompt-eng-interactive-tutorial
- Claude Code best practices (the whole guide is worth reading):
  https://code.claude.com/docs/en/best-practices
- Power-user tricks: https://arps18.github.io/posts/claude-code-mastery/
