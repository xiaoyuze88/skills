# my-skills

Three Claude Code workflow skills.

## Installation

In Claude Code:

```text
/plugin marketplace add xiaoyuze88/skills
/plugin install my-skills@my-skills
```

## Skills

### User-invoked

- `my-skills:are-you-sure` — Ask the agent to name its least-certain claim and your biggest blind spot in the current situation. Use it before committing to a plan.
- `my-skills:handoff-supervise` — Supplement `/handoff` with the supervisor collaboration contract: who the implementer should ask, the two approval gates, delivery fallback, and the supervisor's review duties. Use it after `/handoff`.

### Model-invoked

- `my-skills:session-close-check` — Inventory the volatile information produced during a session before it is closed, check where each item is already recorded, and return an A/B/C checklist for the user to decide on. Use it before ending a session.

## Usage

```text
# Before ending a session
/my-skills:session-close-check

# Before handing work to another session
/handoff
/my-skills:handoff-supervise

# Before committing to a plan
/my-skills:are-you-sure
```
