# Learning Workflow — canonical lifecycle commands

This file owns operational commands and repository persistence. [Syllabus V2](../curriculum/SYLLABUS_V2.md) owns curriculum coverage and order; [Progress](../progress/PROGRESS.md) owns status and the single 4-column tracker; [Daily Log](../progress/DAILY_LOG.md) owns chronological context; the [Playbook](../docs/LEARNING_LAB_PLAYBOOK.md) owns teaching quality, assessment and chapter/visual standards. Follow the live GitHub version where accessible; do not rely on stale copies without checking.

## Command contract

| User intent / command | Behavior |
|---|---|
| `START TOPIC` / `START CHAPTER` | Start the explicitly named topic, or the next eligible NOT STARTED row; perform the start checklist below. |
| `CONTINUE` / `RESUME` | Resume the current STARTED topic at its last specific checkpoint. If several topics are STARTED, use the latest explicitly active topic in the Daily Log; if still ambiguous, ask which one. |
| `SPECIFIC TOPIC: <name>` | Study a named syllabus topic out of order, without changing unrelated topic status. For an unscheduled request, first add it under Misc / Ad-hoc if it is becoming a tracked session. |
| `PAUSE TOPIC` / `PAUSE CHAPTER` | Log where the discussion stopped, assumptions and next question. Keep the row STARTED; don't create the final chapter. |
| `COMPLETE TOPIC` / `COMPLETE CHAPTER` | Explicit authorization to run the full completion-and-publishing pipeline without another confirmation. |
| `UPDATE LEARNING STATE` | Report current Progress and latest Daily Log context; if explicitly asked to persist changes, write only demonstrated changes and verify them. |

Natural-language equivalents work. Commands are not case-sensitive. A user asking for an explanation, reference notes or 'discussion only' does **not** implicitly start or complete a tracked topic. Do not reveal exercise solutions before a learner attempt unless directly requested.

## START TOPIC / START CHAPTER pipeline

1. **Resolve:** Honor a named topic first; otherwise pick the latest relevant STARTED core topic on CONTINUE, else the first eligible NOT STARTED topic in ordered Progress. Check syllabus phase, prerequisites, relevant chapter and exercise links; never infer mastery from the file list.
2. **Resume or calibrate:** Read the latest topic-specific Daily Log before repeating questions. For a new topic, share a short motivation, desired outcomes, boundaries and one meaningful diagnostic question. For a resume, restate the precise last checkpoint and pick up there.
3. **Interactive teaching:** Use a repeating **teach → concrete example/trace → one question → wait → evaluate the learner's response → explain gaps → next concept chunk** cycle. Teach the relevant mental model and mechanics *before* asking a question that would otherwise require inventing unknown material. Move progressively from recall to application, failures and Principal judgment; never run the topic as a chain of unsupported hypothetical questions. Keep design labs attempt-first *after* giving adequate requirements and prerequisite explanation. Use relevant visuals; see Playbook.
4. **Track the start:** When it is genuinely a tracked lesson and repository write permission is available, change only that row NOT STARTED → STARTED and add a concise dated Daily Log entry noting objective, checkpoint and next action. If writes are unavailable, present the proposed update without claiming it happened.
5. **No premature chapter:** Do not create empty topic directories, completed reference chapters, model solutions or a numeric rating when starting. The working discussion can span sessions.

## CONTINUE / PAUSE behavior

- Keep partial reasoning, learner attempts, unresolved calculations, misconceptions, decision assumptions, and the next question in the single Daily Log rather than a competing tracker.
- If a specific topic is chosen out of order, do not silently complete or reset the existing STARTED topic.
- More than one STARTED row may exist across contexts; latest explicitly active Daily Log topic wins for CONTINUE, not table order alone. Ask for disambiguation only when truly necessary.
- PAUSE never changes STARTED to COMPLETED.

## COMPLETE TOPIC / COMPLETE CHAPTER pipeline

**Trigger:** User explicitly says COMPLETE TOPIC / COMPLETE CHAPTER (or equivalent), OR assistant offers to mark the topic complete after checking for remaining questions and user confirms. Never treat silence, 'thanks', a final explanation or 'discussion only' as confirmation.

1. **Confirm scope and evidence:** Audit discussed outcomes, syllabus expectations and unanswered questions. Distinguish independent learner answers, answers reached with hints, merely explained topics and unattempted tests. Completion means the agreed discussion/artifact is finished; it **does not** mean interview or Principal mastery.
2. **Assemble the full original artifact:** For a concept, publish one useful readable Markdown chapter under its phase-appropriate `concepts/.../<topic>/README.md`. For a lab, publish one completed design under `design-exercises/<topic>/README.md` (based on `TEMPLATE.md`). Include applicable technical internals, worked examples, diagrams/visuals, calculations, assumptions, trade-offs, failure/operational/security/cost analysis, common misconceptions, exercises with progressive hints, clearly separated **model solutions/answer keys for every exercise and discussion checkpoint at completion, including questions the learner did not attempt** (publication itself is expressly authorized by COMPLETE CHAPTER; still do not reveal solutions during interactive work before an attempt or direct request). Preserve the learner's actual attempts verbatim or accurately attributed separately; explicitly label unattempted and model-only material as not evidence of competence. Include reasoning, alternatives, failure modes and test criteria rather than answer-only keys, revision, references and open gaps. The Playbook specifies quality and asset conventions.
3. **Assess honestly:** Add a `0–5` discussion-coverage rating (Progress displays `N/5` only on completion). Separately state explain/apply/diagnose/defend/validate evidence, interview readiness and production/Principal-level readiness; mark anything unassessed rather than inventing success. Note specific reinforcement and retest prompts.
4. **Prepare related artifacts:** Update the relevant concept/lab index, links, glossary and recall prompts if useful. Keep one canonical chapter per concept; update the source register only for material source-backed technical claims. For each published chapter, **consult the latest [Resources catalog](../resources/README.md)** and add a short, optional **topic-to-resource deep-dive guide where helpful**, using recommendations *only from resources named in that catalog*. State what concept each helps with and why; provide verified edition/section/video information if available, otherwise avoid invented precise references. Do not duplicate entire lessons in Daily Log.
5. **Publish coherently:** Write and validate the chapter/lab artifact first. Then update the index and Daily Log with summary, last checkpoint, assessment and next step. Mark the single Progress row COMPLETED with its honest `N/5` and link its topic name to the published folder **only after that folder exists and is readable**. Keep all other row statuses/ratings unchanged.
6. **Validate all outputs:** Fetch the written files back from GitHub; confirm status, artifact path, link targets, Markdown/Mermaid syntax where possible, relative links, revision and source references. Verify the final commit(s) or source contents. Report actual paths and commit(s), not a claim of success without verification.
7. **Handle partial failure:** If any write or validation fails, never claim the topic is fully published. Preserve/recover the latest truthful state, identify which writes succeeded, and repair consistency before retrying; do not mark COMPLETED with a broken or missing artifact.
8. **Close:** Give a brief recap, link the finished artifact, explain the rating versus mastery distinction, flag reinforcement needs, and identify next eligible topic.

If the user only requests a draft rather than persistence, produce the proposed artifact and state clearly that the repository was not changed.

## Review and change control

For approved curriculum or workflow changes, inspect canonical syllabus, ordered Progress rows, competency matrix, relevant concept indexes, exercise cases/template, exit evidence/scorecards, source register/resources, README navigation and relative links. Update each affected file in the same coordinated change set; avoid unrelated cleanup. For a new required topic: define placement/prerequisites and evidence; create its NOT STARTED Progress row with rating `—`; map it to a lab/practice or justify deferral. For new labs, add **solution-free** case prompts. Preserve every existing status/rating and only adjust paths when the actual artifact exists. Record authoritative sources and verify written outputs; never claim repository, Drive or Project attachment updates without successful operations.

## Public resource hygiene

Treat books, PDFs, course files and images as copyrighted unless redistribution permission is established. A personal copy is not evidence of permission to publish it in this public repository. Before adding materials, document publisher/source and license, or link to the official resource instead. Do not reproduce book pages or copyrighted illustrations inside notes; create original visuals or reuse properly licensed assets with attribution.
