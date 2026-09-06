# Automation audit — setup guide

The graphic tells you what to do next. This tells you exactly where to click.

## 1. The files

- `skills/automation-audit/SKILL.md` — the instructions themselves.
- `automation-audit.zip` — the same skill packaged for assistants that accept an uploaded skill.
- `setup-guide.md` — this file.

A "skill" here is a set of written instructions you give the assistant once, so it knows how to
audit your automations without being re-briefed each time.

## 2. Load the instructions

Two routes. Upload the ZIP where a client supports uploaded skills, otherwise paste the text.

### Claude

1. `Customize → Skills → +`
2. `Create skill → Upload a skill`
3. Drop `automation-audit.zip` into the upload area.
4. Toggle the skill to **Enabled**.
5. Start a new chat. The `automation-audit` skill chip should be attached to it.

Skill upload availability varies by plan and surface. If you do not see it, use the paste route
below instead — the instructions are identical.

### ChatGPT

1. Open `SKILL.md` and copy all of its text.
2. Paste it into a new chat as the first message.

There is nothing to install for this step.

### Gemini

Use **Gemini CLI, in your terminal**, not the Gemini web app. The MCP connection in step 3 is a
terminal-client feature, so starting in the CLI saves repeating yourself later.

1. Open Gemini CLI.
2. Paste all of `SKILL.md` as your first message.

## 3. Connect Zapier MCP

**What this connection does.** It gives your assistant a route into Zapier: the assistant asks
Zapier to do something, and Zapier carries it out in the apps you have connected through it. Set the
route up once, then connect whichever apps a given job needs.

**You only need this for the rebuild.** The audit in step 4 runs without it.

Shared endpoint, Streamable HTTP:

```
https://mcp.zapier.com/api/v1/connect
```

### Claude Code

```
claude mcp add --transport http "Zapier-MCP" https://mcp.zapier.com/api/v1/connect
```

### Claude, and other MCP clients

Ask in chat: *"Connect to Zapier MCP."* Then sign in to Zapier and allow access. Client-by-client
setup: https://docs.zapier.com/mcp/get-started/quickstart

### ChatGPT

This route depends on your plan and your account's permissions. **It is not available to every
account**, and an admin may have to allow it.

1. `Settings → Apps → Advanced Settings`, turn on developer mode if your plan allows it.
2. `Settings → Apps → Create`.
3. Supply the endpoint above and complete authentication.

Details: https://help.openai.com/en/articles/12584461

If developer mode is not open to you, you can still run the audit. You just cannot have ChatGPT do
the rebuild through MCP.

### Gemini CLI

```
gemini extensions install https://github.com/zapier/zapier-mcp
```

Then, inside Gemini CLI:

```
/mcp auth zapier
```

Zapier's own client configuration: https://github.com/zapier/zapier-mcp/blob/main/AGENTS.md

## 4. Run the audit

List your recurring jobs, one per line, then send:

```
audit my automations
```

Every job comes back **Move**, **Keep** or **Delete**. Four tests decide it, and each one is a
question about that job on the host it actually runs on:

| Test | What it asks |
|---|---|
| Closed laptop | Does it have to run at a fixed time whether or not your computer is awake? A cloud-hosted schedule passes. For a local job, check whether sleeping, wake and catch-up behaviour meet the required timing. |
| Broken step | If step three of five fails, should steps four and five still run? It fails only when those later steps are independent and get abandoned anyway. |
| Waiting | Does it pause for a person, then continue hours or days later? That needs somewhere durable to hold the job's place, and something that can resume it. |
| Same every time | Does it move through three or more apps on a fixed route that should never vary? It fails when a model chooses the settled sequence again each run. An already enforced route passes. |

A job stays where it is when the hard part is judgement, writing, reading something new or deciding
what matters, **and its current setup already passes all four tests**.

Delete is for duplicates only: another job on your list already does the same work.

If the audit cannot tell where a job runs, it should ask you rather than guess.

Pick the first job marked Move and do step 5 for it.

## 5. Move one job

Send:

```
migrate it
```

This is a separate step. The audit does not build anything, and it never changes an existing job.

**Build.** Your assistant rebuilds the job as a Zapier workflow with **no trigger attached**, so it
cannot start on its own while you are checking it. If your session has no workflow build tools, it
should tell you and hand you the build prompt rather than describing a build it did not do.

**Test.** Run it once by hand on a safe input, then open each app and confirm what actually
happened. Do not trust a tick in the editor alone: for example, check a row appeared or an email arrived if that is what your job should produce.

**Switch on.** Only after the test passes, and only when you say so:

1. Add the trigger, the event or time that should start it.
2. Turn the **new** job on.
3. Then turn the **old** job off.

That order matters. Enabling the replacement before retiring the original means there is never a
window with neither running.

Repeat from `migrate it` for the next job marked Move. You do not need to re-run the audit, reload
the instructions or reconnect Zapier MCP — those are one-time steps.
