# Agentic Workflow Calibration, the Transcript Vault, and Seeds Above Beads

**Date**: 2026-10-14
**Source**: A Claude Code session brainstorming what seeds can be as a layer above beads, combined with a system that keeps immutable copies of transcripts.
**Status**: Parked as a doc until the transcript vault exists; not yet filed as seeds.

## The layered picture

```
 raw audio / Claude sessions / meetings
              │  (immutable, outside any repo)
        ┌─────▼─────┐
        │  VAULT    │  every source kept verbatim, identified by a hash of its contents
        └─┬───────┬─┘
  memories│       │proposed seeds
 (what was│       │(what's still unsettled)
  decided)▼       ▼
     clancey/   SEEDS  ── questions, options, tensions, deferrals
     memory      │
                 │ resolve → promote (decision / spec)
                 ▼
               BEADS  ── tasks to do, and when
                 │
                 └── what implementation turns up flows back into seeds
```

Seeds is the "why and whether" layer, beads is the "what and when" layer, and the vault is the record both of them point back to.

## Part 1: Workflow failure modes as calibration settings

The list of possible failure modes:

- Your effort level is too high
- Your skills are too long
- You're not aligning before you start work
- You're aligning too often, and too hard
- Your automated checks are too lax
- Your custom tooling is too token-inefficient
- You aren't doing enough automated review
- You aren't reviewing previous coding sessions (with an agent)
- You're doing too much human review
- You aren't vibe coding enough prototypes
- Your agent can't verify its own work
- You're babysitting the agent too much
- You've overloaded your global steering files
- Your codebase is hard to navigate
- You aren't building a shared language
- Your codebase architecture allows bad tests to be written
- You aren't thinking strategically enough
- You aren't thinking enough

### Key observation: they come in opposing pairs

| Too much | Too little |
|---|---|
| Effort level too high | Not thinking enough, not strategic enough |
| Aligning too often and too hard | Not aligning before starting |
| Too much human review, babysitting | Not enough automated review, checks too lax |

These are settings to tune, not problems to fix once. The right setting depends on the task and drifts over time, so each one has to be judged against evidence. That is the case for the vault: it holds the evidence, and seeds is where you record your judgments about where to set each one.

### How each one maps to seeds, the vault and beads

**What the vault makes possible**
- *Not reviewing previous coding sessions with an agent.* This is the vault's main job. An agent reads the backed-up sessions and proposes candidate seeds ("we redid alignment 3 times in this session"). Without stored transcripts this can't happen at all.
- *Effort, alignment and babysitting settings.* You only learn these from what actually happened across many sessions. Each could become a seed that holds a working rule ("align once, on scope, before any code") plus links to the vault moments that support it.

**Things seeds would track or produce**
- *Not building a shared language.* Seeds is a natural home for a project glossary. seeds-125 (gleaning) already shows the pattern. Terms that settle could be promoted to a glossary file.
- *Not thinking strategically enough.* This is exactly what the "why and whether" layer is for. One test: does a bead trace back to a seed that weighed alternatives?
- *Not vibe coding enough prototypes.* A prototype is just one way to answer a seed's question. Seeds should accept "answered by prototype X, here's what we learned" as an answer, so throwaway code still leaves a record.

**Risks to seeds' own design**
- *Global steering files overloaded.* This directly conflicts with seeds-147.3, which promotes decisions into CLAUDE.md. Promoting everything bloats those files. The promote step needs a budget, or a target that's loaded only when needed rather than every session.
- *Skills too long.* This supports seeds-152.2 (keep shipped skills small, like prompt macros).
- *Custom tooling too token-inefficient.* That covers seeds itself: `prime`, `show` and `list` output size. It's worth measuring.

**Better handled in beads or the codebase**
- *Automated checks too lax, not enough automated review, agent can't verify its own work.* These belong in beads, as acceptance criteria that can be checked. Seeds' part is only to hand those criteria down when a seed becomes beads (seeds-186).
- *Hard-to-navigate codebase, architecture that allows bad tests.* These are outside seeds' scope, but they're good candidate seeds inside each project.

## Part 2: Gaps for the vault and the layer above beads

Already captured: seeds-4, 126, 128, 131, 133 (storing sources); seeds-142, 142.3, 74.2.4 (pulling seeds out of transcripts); seeds-181 (sower); seeds-156, 116 (memory vs. seeds); seeds-12, 12.1, 12.2, 147.3, 187 (handing off to beads); `docs/seed-vs-spec-tension-2026-05-19.md`.

Not yet captured:

**Vault**
1. **The original recording should be the permanent record.** Apple and Fluid Voice can produce different transcripts of the same memo. Treat the audio as the original and each transcript as a version made from it. Seeds should then point to a moment in the audio (a timestamp), not a line number in one transcript.
2. **"Immutable" still needs a way to delete.** Recordings of colleagues, and legally sensitive work content, will sometimes have to be removed. Use a deletion record that leaves a trace, so "append-only" isn't promised more strongly than it can be kept.
3. **Claude Code transcripts disappear by default.** Local session logs are removed after a set number of days, and cloud sessions vanish when the container is reclaimed. Hooks that copy the session to the vault at the end of a session and before compaction are probably the first concrete thing to build.
4. **Work and personal need separate vaults.** Work transcripts shouldn't end up in a personal vault, so the privacy boundary should be a property of the vault itself, not something each seed has to handle.

**Re-reading sources and keeping a record of it**

5. **Record each extraction run.** For every run, keep which source, the state of the seeds database, the model and prompt used, and the seeds it produced or changed. That gives you:
   - a way to audit made-up IDs (142.3)
   - the ability to ask "what would re-reading this find today that it missed in June?" (131)
   - a way to undo one bad run
6. **The same source feeds two outputs:** memories (what was decided) and seeds (what's still open). If you don't decide who owns what, things get captured twice. Proposed rule: once a seed resolves, its decision becomes a memory or durable context, and the seed stops being an active item.

**What seeds should hold that beads doesn't**

7. **Options that were rejected**, with the reason. Beads only keeps what was chosen.
8. **Assumptions that say when to revisit them**, e.g. "if X changes, reopen this." Deferrals should also say what would bring them back, not just "later."
9. **Feedback from implementation.** Surprises, code-review pushback (seeds-177) and beads that drift away from what their seed decided should flow back into seeds. Today the handoff only goes one way.
10. **Link types across all three layers:** `derived-from:<vault ref>`, `spin-off-of`, `supersedes`, `implemented-by:<bead>`, `invalidated-by`. This is the tension doc's spin-off problem, extended across layers.
11. **The spec layer** might not need a new tool. It could be the output of `seeds promote`: a versioned file that's frozen once beads start using it.

**Risks to keep watching**
- Turning into a graveyard of unread notes (`docs/immediateissues.md`), or growing without converging (seeds-45)
- intent.build and beads taking over ground seeds was meant to cover (176.5, 12.1). The vault plus the "why and whether" layer may be the part that's genuinely different.
- Other people's words in transcripts: consent and how long they're kept

## Proposed filing when ready

- One umbrella seed, **"agentic workflow calibration"**, with one concern for each opposing pair above rather than 18 separate seeds. Each concern links to vault evidence once the vault exists.
- The two that bear on seeds' own design (overloaded steering files vs. promote, and token use) go under seeds-147.3 and seeds-152.2.
- A new umbrella seed, **"transcript vault"**, holding Part 2 items 1–5.
- Items 6–9 as children of seeds-12, item 10 under seeds-6, item 11 linked to seeds-147.3.
