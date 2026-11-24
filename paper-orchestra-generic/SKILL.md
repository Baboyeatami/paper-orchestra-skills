---
name: Paper Orchestra Generic
description: Generate a paper draft from any research materials using Google PaperOrchestra paper_writing_cli.py, with configurable guardrails
---

# Paper Orchestra Generic

Use this skill when the user asks to draft, regenerate, or revise a paper from
idea and experimental materials using Google Research's PaperOrchestra. It does
not install PaperOrchestra. It prepares bounded inputs, runs the CLI, and reports
an audit so the draft is not mistaken for a reviewed manuscript.

Stop and report the blocker instead of guessing if any precondition fails.

## 0. Resolve paths

- `PROJECT_DIR` = `$PAPER_PROJECT_DIR` if set, otherwise `$PWD`. All inputs and
  outputs live under this folder.
- `PO_DIR` = `$PAPER_ORCHESTRA_DIR` if set. Otherwise, if `$PWD/paper_writing_cli.py`
  exists, use `$PWD`. Otherwise run
  `find "$HOME" -maxdepth 7 -name paper_writing_cli.py 2>/dev/null | head -1`.
  If nothing is found, stop: "PaperOrchestra not found. Set PAPER_ORCHESTRA_DIR
  to the folder containing paper_writing_cli.py."
- Confirm `$PO_DIR/paper_writing_cli.py` exists.
- `TEMPLATE_DIR` = `$PAPER_LATEX_TEMPLATE_DIR` if set, otherwise
  `$PO_DIR/templates/cvpr2025`. If the user asked for IEEE formatting and the
  chosen template is not an IEEEtran template, warn that this is a smoke-test
  template, not an IEEE template.

## 1. Preflight checks

1. Confirm a conda environment named `paper_orchestra` exists:
   `conda env list | grep -E '^paper_orchestra\s'`. If absent, stop with the
   create commands from the upstream PaperOrchestra README.
2. Confirm `$PO_DIR/.env` exists. Check variable **names only** with
   `grep -oE '^[A-Z_]+=' "$PO_DIR/.env" | sort -u`. Never print or echo values.
3. Confirm at least one provider credential name is present:
   `GEMINI_API_KEY`, `OPENAI_API_KEY`, or `VERTEX_AI_PROJECT`. If none is present,
   stop and tell the user which one to add.
4. Required inputs in `PROJECT_DIR`: `idea_sparse.md` and `experimental_log.md`.
   Optional: `figures/`, `guardrails.md`.
   If either required file is missing, copy the matching file from this skill's
   `templates/` folder into `PROJECT_DIR`, then stop and tell the user to fill
   it before calling again. Do not overwrite existing files.

## 2. Build the raw-materials folder

Create a run folder:

```sh
RUN="$PROJECT_DIR/outputs/paper_orchestra/$(date -u +%Y%m%dT%H%M%SZ)"
mkdir -p "$RUN/raw_materials/figures" "$RUN/output"
cp "$PROJECT_DIR/idea_sparse.md" "$RUN/raw_materials/idea_sparse.md"
```

Build `experimental_log.md` from the project file plus any guardrails, without
modifying the originals:

```sh
cp "$PROJECT_DIR/experimental_log.md" "$RUN/raw_materials/experimental_log.md"
if [ -f "$PROJECT_DIR/guardrails.md" ]; then
  printf '\n## Generation guardrails\n\n' >> "$RUN/raw_materials/experimental_log.md"
  cat "$PROJECT_DIR/guardrails.md" >> "$RUN/raw_materials/experimental_log.md"
fi
```

Copy figures when present:

```sh
cp -R "$PROJECT_DIR/figures/." "$RUN/raw_materials/figures/" 2>/dev/null || true
```

## 3. Run the generator

From `$PO_DIR`, with the environment activated:

```sh
conda run -n paper_orchestra python paper_writing_cli.py \
  --raw_materials_dir "$RUN/raw_materials" \
  --latex_template_dir "$TEMPLATE_DIR" \
  --output_dir "$RUN/output" \
  --use_plotting false
```

- Use `--use_plotting false` by default so existing figures are reused.
- Add `--use_plotting true` only when the user explicitly asks for generated figures.
- If `$PAPER_WRITER_MODEL` is set, add `--writer_model_name "$PAPER_WRITER_MODEL"`.
- If `$PAPER_REFLECTION_MODEL` is set, add `--reflection_model_name "$PAPER_REFLECTION_MODEL"`.
- Do not pass API keys on the command line.

## 4. Audit the output

1. Find generated LaTeX: `find "$RUN/output" -name '*.tex' -type f`.
   If no `.tex` file exists, fail and report the run folder.
2. If `PROJECT_DIR/guardrails.md` exists, use its `BANNED:` and `REQUIRED:`
   sections. Report any banned phrase found and any required phrase missing.
3. If no guardrails file exists, apply the default check in this skill's
   `templates/guardrails.md` and state that the check is advisory, not proof.
4. State clearly that generated prose must be reviewed by a human before
   submission. Do not edit the generated LaTeX to hide a finding.

## 5. Handoff

Report:

- `PROJECT_DIR`, `PO_DIR`, and `TEMPLATE_DIR` used,
- the raw-materials and output paths,
- the audit result,
- the exact limitations listed in the project's `guardrails.md`, if present.

Never submit the draft to a journal or conference. The user owns that decision.

## Scope boundaries

- Do not install PaperOrchestra or modify its source.
- Do not create, print, or copy `.env` contents.
- Do not overwrite the user's `idea_sparse.md`, `experimental_log.md`, figures,
  or guardrails.
- Do not claim venue acceptance; a LaTeX template is a formatting choice.
