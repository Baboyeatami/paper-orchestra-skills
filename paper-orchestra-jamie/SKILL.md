---
name: Paper Orchestra Jamie
description: Generate a PaperOrchestra draft and rewrite it in Jamie Eduardo C. Rosal's academic voice with reviewer attribution
---

# Paper Orchestra Jamie

Use this skill when the user asks for a Jamie-voiced paper draft, invokes this
skill by name, or asks to draft a paper "the Jamie way" with PaperOrchestra. It
requires the `paper-orchestra-generic` skill to be installed, because this skill
adds a voice and attribution layer on top of the generic workflow.

## 0. Delegate the base workflow

Load `paper-orchestra-generic` with the skill tool and follow its path
resolution, preflight, raw-material build, CLI run, and output audit. Apply the
input pass in section 2 before the CLI run, and the output pass in section 4
after the CLI run.

## 1. Load the voice source

Read `references/jamie-voice.md` in this skill folder. If the
`humanize-like-jamie` skill is available, also load it and read its `STYLE.md`
and newest sample. If the sample disagrees with the distilled profile, follow the
sample. Keep every fact, number, name, date, and citation exactly as the source
gives it.

## 2. Input pass

Rewrite `PROJECT_DIR/idea_sparse.md` and `PROJECT_DIR/experimental_log.md` in
clean Jamie voice before they are copied into the raw-materials folder. Keep the
headings and every factual value. Do not add facts, citations, or results. Use
long layered sentences, passive voice for methods and results, and cautious
hedging where the evidence is limited. Do not use em dashes or en dashes.

## 3. CLI run

Run the generic skill's CLI command from `$PO_DIR` with `--use_plotting false`
unless the user explicitly asks for generated figures. Do not add model flags
that the user did not set.

## 4. Output pass

For each generated `.tex` file:

1. Rewrite narrative prose only. Keep equations, math, code, tables, figure
   commands, citation keys, labels, and all numbers unchanged.
2. Apply the voice profile: long layered sentences, passive voice in methods and
   results, present tense for general truths, Jamie's connectors and framing
   moves, exact numbers, and explicit limitations before recommendations.
3. Remove AI writing tells: not-X-but-Y contrasts, one-line closers, staged
   openers, forced triads, inflated claims, stock AI words, and decorative bold.
4. Insert the reviewer line after the author and affiliation block:
   `\noindent {\small Reviewed by Jamie Eduardo Rosal}\par`
   If the template structure makes this unsafe, insert the plain line
   `Reviewed by Jamie Eduardo Rosal` after the authors and tell the user to
   adjust the LaTeX placement.
5. Do not use em dashes or en dashes in the prose. Do not add emoji.

## 5. Audit and handoff

Run the generic skill's audit, then check that the generated prose has no em
dashes or en dashes, no decorative bold, no contractions, and contains the
reviewer attribution. Report the paths used and a short list of what the voice
pass changed. State that the draft still needs human review before submission.

Never submit the draft to a journal or conference. The user owns that decision.

## Scope boundaries

- Do not change facts, numbers, citations, tables, figures, or code.
- Do not turn clean Jamie into polished magazine English. The longer sentence
  rhythm is part of the voice.
- Do not use full quirks mode unless the user explicitly asks for it.
- Do not claim venue acceptance; a LaTeX template is a formatting choice.
