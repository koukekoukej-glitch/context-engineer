# Context Engineer

A skill for **context engineering** — designing behavior-control artifacts (Skills, Rules, Docs, Hooks) for a specific reader (a future model instance), not tweaking prompt blobs.

**[中文版 / Chinese →](README.md)**

---

## What's inside

One skill, two entry points:

| Entry point | What it does | When to invoke |
|-------------|--------------|----------------|
| `/context-engineer` | Production flow. Mines your real situation, picks the right artifact type (Skill / Rule / Doc / Hook), writes with failure-mode discipline, then automatically spawns an independent reviewer. | "Write a skill for …", "I want the agent to do X whenever Y", "improve this prompt" |
| `/context-review` | Independent audit. Spawns a fresh agent with a clean attention pool to review an existing artifact — finds structural issues that silently break model behavior, not style nits. | "Review this skill", "check my rule", "审一下" |

Both entries share the same reader model and review checklist — the production flow calls the reviewer in its Step 4; the reviewer is also independently callable on artifacts you didn't write.

---

## Why it exists

Ad-hoc prompt writing produces artifacts that work in a demo and drift under real use. This skill treats every artifact as **infrastructure** aimed at a specific reader (a future model with no memory of your conversation), and enforces:

- **Situation before solution.** "I want a code-review skill" can mean ten different things — extract the real problem before writing a line.
- **Severity-graded constraints.** High-risk rules use *consequences* without reasoning (to block rationalization). Mid-risk rules demand a *WHY* (to activate generalization). Preferences stay light.
- **Attention budget is a real budget.** Rules < 50 lines. Skill core path < 500 lines. Bloat is a defect, not thoroughness.
- **Independent review by default.** The author's attention pool is contaminated by their own draft — a clean one catches what you can't.
- **No stale scaffolding.** Instructions written for older models (anti-emoji, anti-over-apologizing, forced progress summaries) are audited at each model upgrade — dead rules still burn attention and can conflict with new defaults.

The full reader model (7 traits of how models read context) and the writing rules (R1–R8) live inside [`skills/context-engineer/SKILL.md`](skills/context-engineer/SKILL.md).

---

## Example sessions

**Using `/context-engineer`:**

> User: *"I want the agent to run our database migrations more carefully."*
>
> The skill first asks sharpening questions: is this for you or your team? What went wrong the last time? Can you show a concrete incident? It classifies the need as a **Rule** (a 50-line behavior constraint), not a Skill — because the behavior is always-on, not step-by-step. It names the likely failure modes (skipping validation, claiming success without checking), drafts the artifact, then spawns a fresh reviewer before handing back the result.

**Using `/context-review`:**

> User: *"Audit `skills/deploy-guard/SKILL.md`."*
>
> The skill spawns an independent agent with no conversation context, feeding it only the artifact and the review checklist. It reports findings by severity: attention-budget overruns, completeness illusions (lists the model will treat as exhaustive), rules missing a WHY, rigid rules that should allow edge-case judgment. Each finding comes with a concrete rewrite, not a vague "consider improving".

---

## Install

The skill ships in a widely-adopted skill file layout (a markdown file with YAML frontmatter + optional `references/`). If your agent harness supports this layout directly, drop the folders into its skills directory:

```bash
git clone https://github.com/koukekoukej-glitch/context-engineer.git
cp -r context-engineer/skills/* <your-skills-dir>/
```

Or symlink, if you want to pull updates later:

```bash
ln -s "$(pwd)/context-engineer/skills/context-engineer" <your-skills-dir>/context-engineer
ln -s "$(pwd)/context-engineer/skills/context-review"   <your-skills-dir>/context-review
```

Then invoke:

- `/context-engineer` — design a new artifact
- `/context-review` — audit an existing one

Both also trigger on natural language ("write a skill for …", "审一下这个 rule"), not only slash commands.

**Using a harness that doesn't understand this layout?** The file format is convention, but the methodology — reader model, severity-graded constraints, attention budgeting, independent review — is portable. Paste `SKILL.md` into whatever prompt layer your framework provides.

---

## Calibrated for modern frontier models

The writing style is tuned for strong-instruction-following model defaults:

- Positive-framed instructions preferred over negative suppression — modern models already default to restraint, so most "don't do X" rules are dead weight.
- Per-step effort annotations ("think carefully here", "this is mechanical, don't over-think") — a controllable dimension in recent models that the skill uses by design.
- Rigid rules only for truly high-risk cases. Most rules carry a WHY so the model can generalize to unseen situations instead of memorizing.

Older scaffolding — "don't use emojis", "don't over-apologize", "summarize every N steps" — has been intentionally removed.

---

## License

[MIT](LICENSE)
