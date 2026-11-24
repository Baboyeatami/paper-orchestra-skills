# paper-orchestra-skills

Two OpenCode skills that wrap Google Research's PaperOrchestra CLI:

- `paper-orchestra-generic`: run `paper_writing_cli.py` from any project folder
  with configurable inputs and guardrails.
- `paper-orchestra-jamie`: run the generic workflow, then rewrite the draft in
  Jamie Eduardo C. Rosal's academic voice and add reviewer attribution.

These skills do not install PaperOrchestra. PaperOrchestra still needs its own
Python 3.11 conda environment named `paper_orchestra` and a `.env` with model
provider keys. See the upstream repository for that setup.

## Install

Copy both skill folders into your OpenCode skills directory:

```sh
cp -R paper-orchestra-generic ~/.config/opencode/skills/
cp -R paper-orchestra-jamie ~/.config/opencode/skills/
```

Optional environment variables:

```sh
# Optional: auto-detected at ~/.local/share/paper-orchestra
export PAPER_ORCHESTRA_DIR="$HOME/.local/share/paper-orchestra"
export PAPER_PROJECT_DIR=/path/to/research-project
export PAPER_LATEX_TEMPLATE_DIR=/path/to/ieee-template
export PAPER_WRITER_MODEL=gemini-3.1-pro-preview
export PAPER_REFLECTION_MODEL=gemini-3.1-pro-preview
```

`PAPER_PROJECT_DIR` defaults to the folder where the skill is called. Outputs are
written under `<project>/outputs/paper_orchestra/<UTC timestamp>/`.

## Use

Generic:

```text
@paper-orchestra-generic
```

Jamie:

```text
@paper-orchestra-jamie
```

The Jamie skill also uses `humanize-like-jamie` when that skill is installed.

## Project files

In the folder where you call either skill:

```text
idea_sparse.md
experimental_log.md
figures/            optional
guardrails.md       optional, recommended
```

If `idea_sparse.md` or `experimental_log.md` is missing, the generic skill copies
a template into the project folder and stops so you can fill it.

## Guardrails

`guardrails.md` has `BANNED:` and `REQUIRED:` sections. The generic skill appends
it to the generated experimental log and uses those lists for the output audit.
See `paper-orchestra-generic/templates/guardrails.md`.

## License

MIT.
