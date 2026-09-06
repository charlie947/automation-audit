# Automation Audit

A skill that reviews the recurring jobs you have handed to an AI assistant and tells you which ones
are in the wrong place.

List each job once and get a verdict with the reasoning behind it.

## What it does

You paste a list of your recurring jobs. It applies four tests to each one and returns:

- **Move** — the job needs something its current host cannot give it.
- **Keep** — the AI assistant is the right place for it.
- **Delete** — another job on your list already does the same work.

It also flags the job most likely to fail quietly, where the evidence supports that finding.

Then, and only when you ask, it rebuilds the worst one as a workflow, with no trigger attached, so
you can test it before anything goes live.

## The four tests

| Test | What it asks |
|---|---|
| Closed laptop | Does it have to run at a fixed time whether or not your computer is awake? |
| Broken step | If one step fails, should the independent steps after it still run? |
| Waiting | Does it pause for a person, then resume hours or days later? |
| Same every time | Does it move through three or more apps on a route that never varies? |

Each test is a question about your job on the host it actually runs on, not a claim about any
vendor. If the audit cannot tell, it asks.

## Quick start

1. **Load the instructions.** Upload `automation-audit.zip` as a skill, or paste
   `skills/automation-audit/SKILL.md` into a new chat.
2. **Connect Zapier MCP** and authorise access, if you want the rebuild.
3. **Audit your recurring jobs.** List them one per line and send `audit my automations`.
4. **Build the first Move job.** Send `migrate it`. It is built with no trigger attached.
5. **Test it by hand** and check the real output in each app.
6. **Switch the jobs over.** Add the trigger, turn the new job on, then turn the old one off.

The audit works in an assistant that can follow the written instructions. **Steps 4 to 6 need workflow build tools available in your session** —
without them the skill will give you the build prompt rather than pretend to build.

## Files

- [`automation-audit.zip`](automation-audit.zip) — the packaged skill for clients that support ZIP uploads.
- [`SKILL.md`](SKILL.md) — the plain instructions to read or paste.
- [`skills/automation-audit/SKILL.md`](skills/automation-audit/SKILL.md) — the same skill in its folder structure.
- [`setup-guide.md`](setup-guide.md) — client-by-client setup, including the Gemini CLI route and
  the ChatGPT plan and permission conditions.

## Notes

Availability differs by client, plan and account permissions. The setup guide links the official
documentation for each route rather than promising any of them will be open to you.

The audit and build leave existing jobs untouched. Switching over requires a passing test and
your explicit instruction.
