# Hub import collection

This folder is a collection of the skills in `skills/` (19 skills), plus SkillEvaluator's own skill. It is prepared for import into the SkillEvaluator hub, which checks skill quality, security, and harness compatibility.

- **Source:** `panthesys-design/skills` at `e4bb5d9` (branch `ccr-4413682f-n9coy7`)
- **Checker:** SkillEvaluator 0.9.0 at `d2cb138`, run from source
- **Date:** 2026-11-19
- **Machine-readable data:** [`skill-collection.json`](./skill-collection.json)

## Results

Five ready, fourteen need review, and one excluded.

| Skill | Quality | Tier 1 | Recommendation | My use |
| --- | ---: | --- | --- | --- |
| slack-gif-creator | 90.2 | incomplete | review: pin dependencies | hub only |
| academy-guide | 89.8 | failed | review: dead-link check unverified | hub only |
| frontend-design | 88.5 | failed | review: wording flag | loaded here |
| algorithmic-art | 87.8 | failed | review: scanner parser limit | hub only |
| brand-guidelines | 86.0 | passed | **ready** | hub only |
| doc-coauthoring | 86.0 | passed | **ready**: license still open | not decided |
| llm-api-sdk (was claude-api) | 86.8 | failed | review: scanner limits, large body | loaded here as claude-api |
| theme-factory | 83.5 | failed | review: binary-file flag | hub only |
| mcp-builder | 83.2 | failed | review | **absorb** |
| webapp-testing | 83.2 | failed | review: `shell=True` | **absorb** |
| discernment-nudge | 81.5 | failed | **excluded** | excluded |
| canvas-design | 81.0 | incomplete | review: license-text false positive | hub only |
| internal-comms | 81.0 | passed | **ready** | hub only |
| web-artifacts-builder | 80.8 | passed | **ready** | hub only |
| pptx | 79.5 | failed | review | loaded here |
| skill-creator | 79.0 | failed | review | loaded here |
| docx | 75.8 | failed | review | loaded here |
| pdf | 75.8 | incomplete | review: trigger wording | loaded here |
| xlsx | 73.2 | failed | review | loaded here |
| **skillevaluator** (from SkillEvaluator) | 98.8 | passed (quality only) | **ready** | **absorb** |

"Loaded here" means a skill with the same name is already available in this session. I did not compare the content.

Detail for each skill is in `skill-collection.json`.

## What changed this round

These were the four "fix first" skills, plus the discernment-nudge decision.

- **claude-api renamed to llm-api-sdk.** The skill name cannot contain `claude` or `anthropic`, and the folder name has to match. I updated the marketplace entry, the body text and the language subfolders. The `/claude-api <subcommand>` invocations are now `/llm-api-sdk <subcommand>`. The skill loaded in this session still uses the old name until the session reloads.
- **llm-api-sdk description shortened** from 1,068 to 1,015 characters, keeping the trigger wording.
- **Placeholder emails** `jane@co.com` and `john@co.com` changed to `example.com` addresses.
- **Probe script hardened.** `shared/preserved-thinking-migration/drop_block_probe.py` now accepts only http(s) base URLs. Before, `ANTHROPIC_BASE_URL` could point `urlopen` at `file:` URLs.
- **Missing `# Title` added** to algorithmic-art, canvas-design and internal-comms. internal-comms now passes Tier 1.
- **discernment-nudge excluded** from the collection, at the owner's request. It is still in `skills/` as upstream content.

## Still blocking

These need either a scanner change or a larger content edit. I did not work around them.

- **algorithmic-art:** security `analysis-evasion (partial)` on `templates/generator_template.js`. This is a parser limit. Also, `SKILL.md` is about 19.8k characters, over OpenClaw's read cap.
- **canvas-design:** security `incomplete`. The flag is `NOT LIMITED TO` in the OFL font license texts under `canvas-fonts/`. That is a false positive, and I did not edit the licenses.
- **llm-api-sdk:**
  - security `analysis-evasion (partial)` in `shared/tool-use-concepts.md` and `shared/model-migration.md`.
  - Code Risk flags the `urlopen` call in `drop_block_probe.py`, even with the scheme check in place. It is a static rule that doesn't see the guard.
  - `SKILL.md` is about 105k characters, so codex, hermes, openclaw and opencode still degrade. copilot-cli now loads.

"analysis-evasion (partial)" means the scanner's static parser could not resolve a referenced file. The scanner's own suggestion is to fix the scanner, not the content. It is not evidence of malicious content.

## Decisions for the owner

- **doc-coauthoring license.** The checker does not require one. The skill passes all 12 Tier 1 checks, including License Compliance. Upstream ships no `LICENSE.txt`, unlike the other 18 skills, which all carry the Apache License 2.0 text. If you want it to match its siblings, copy `skills/docx/LICENSE.txt` in. That is a legal claim about the content, so it is yours to make.
- **discernment-nudge.** Excluded from the collection. Say if you want it deleted from `skills/` too.

## Collection-level finding

The 19 descriptions together total about 9,384 characters. Claude Code's skill listing fits about 8,000 characters, and Codex's is about the same. When the whole folder loads together, descriptions are shortened, which is why most skills show "2 of 6 harnesses degraded". Import in groups, or shorten descriptions.

## Caveats on the findings

- **Security findings are pattern matches.** SkillSpector flags patterns such as `shell=True`, `os.environ.copy()`, and `rm -f`. Each one needs a human to confirm it. Several (LibreOffice setup in docx/xlsx, binary PDF in theme-factory) look like false positives.
- **academy-guide dead link is unverified.** The `academy.claude.com` links did not respond from this sandbox, so the link check cannot confirm them.
- **Quality score is structural.** It measures how well `SKILL.md` follows documented conventions. It does not show that a skill helps an agent. That needs Tier 3.

## What was not run

- **Tier 2 (deduplication and catalog).** Needs an embeddings provider key, which this sandbox does not have. Once a key is set, build the catalog from the SkillEvaluator checkout with:
  `uv run --python 3.13 --extra tier2 skillevaluator similarity-check /path/to/skills --save-catalog ./skill-catalog.json`
- **Tier 3 (live with-skill vs. without-skill evaluation).** Needs a provider key and Docker. None of these skills ships an `evals/` suite, so the hub would have to generate one first.

## Absorbed into my working set

- **Already loaded here** (same names, content not compared): docx, pdf, pptx, xlsx, skill-creator, frontend-design, and llm-api-sdk (as claude-api).
- **New:** `webapp-testing` (review `shell=True` before running it), `mcp-builder`, and SkillEvaluator's `skillevaluator` skill, which I'd use to check other skills.
- **Hub only:** academy-guide and the creative and niche skills.
- **Excluded:** discernment-nudge.
- **Not decided:** doc-coauthoring (see the license decision above).

## Re-running the checks

From a SkillEvaluator checkout (`uv sync --python 3.13` first):

```
uv run --python 3.13 skillevaluator quality-check <skill-dir> -r cli -r json -o <out>
uv run --python 3.13 skillevaluator validate <skill-dir> --tiers 1 --no-llm -o <out>
uv run --python 3.13 skillevaluator harness-check <skill-dir>
```

Raw HTML and JSON reports are not committed. They are large (about 13 MB), and the manifest records their results.
