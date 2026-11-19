# Hub import collection

This folder is a collection of the skills in `skills/` (19 skills), plus SkillEvaluator's own skill. It is prepared for import into the SkillEvaluator hub, which checks skill quality, security, and harness compatibility.

- **Source:** `panthesys-design/skills` at `e4bb5d9` (branch `ccr-4413682f-n9coy7`)
- **Checker:** SkillEvaluator 0.9.0 at `d2cb138`, run from source
- **Date:** 2026-11-19
- **Machine-readable data:** [`skill-collection.json`](./skill-collection.json)

## Results

Three ready, twelve need review, four need fixes before import, and one is on hold.

| Skill | Quality | Grade | Tier 1 | Recommendation | My use |
| --- | ---: | :---: | --- | --- | --- |
| slack-gif-creator | 90.2 | A | incomplete | review: pin dependencies | hub only |
| academy-guide | 89.8 | B | failed | review: dead-link check unverified | hub only |
| frontend-design | 88.5 | B | failed | review: wording flag | loaded here |
| algorithmic-art | 87.8 | B | failed | fix first: headings, size | hub only |
| brand-guidelines | 86.0 | B | passed | **ready** | hub only |
| doc-coauthoring | 86.0 | B | passed | **hold: no license** | held |
| theme-factory | 83.5 | B | failed | review: binary-file flag | hub only |
| mcp-builder | 83.2 | B | failed | review | **absorb** |
| webapp-testing | 83.2 | B | failed | review: `shell=True` | **absorb** |
| claude-api | 81.5 | B | failed | fix first: reserved name, long description | loaded here |
| discernment-nudge | 81.5 | B | failed | review: keep out of hub? | skip |
| canvas-design | 81.0 | B | failed | fix first: headings | hub only |
| internal-comms | 81.0 | B | failed | fix first: headings | hub only |
| web-artifacts-builder | 80.8 | B | passed | **ready** | hub only |
| pptx | 79.5 | C | failed | review | loaded here |
| skill-creator | 79.0 | C | failed | review | loaded here |
| docx | 75.8 | C | failed | review | loaded here |
| pdf | 75.8 | C | incomplete | review: trigger wording | loaded here |
| xlsx | 73.2 | C | failed | review | loaded here |
| **skillevaluator** (from SkillEvaluator) | 98.8 | A | passed (quality only) | **ready** | **absorb** |

"Loaded here" means a skill with the same name is already available in this session. I did not compare the content.

Detail for each skill is in `skill-collection.json`.

## Collection-level finding

The 19 descriptions together total 9,384 characters. Claude Code's skill listing fits about 8,000 characters, and Codex's is about the same. When the whole folder loads together, descriptions are shortened, which is why most skills show "2 of 6 harnesses degraded". Import in groups, or shorten descriptions.

## Caveats on the findings

- **Security findings are pattern matches.** SkillSpector flags patterns such as `shell=True`, `os.environ.copy()`, and `rm -f`. Each one needs a human to confirm it. Several (LibreOffice setup in docx/xlsx, binary PDF in theme-factory) look like false positives.
- **academy-guide dead link is unverified.** The `academy.claude.com` links did not respond from this sandbox, so the link check cannot confirm them.
- **Quality score is structural.** It measures how well `SKILL.md` follows documented conventions. It does not show that a skill helps an agent. That needs Tier 3.

## What was not run

- **Tier 2 (deduplication and catalog).** Needs an embeddings provider key, which this sandbox does not have. Once a key is set, build the catalog from the SkillEvaluator checkout with:
  `uv run --python 3.13 --extra tier2 skillevaluator similarity-check /path/to/skills --save-catalog ./skill-catalog.json`
- **Tier 3 (live with-skill vs. without-skill evaluation).** Needs a provider key and Docker. None of these skills ships an `evals/` suite, so the hub would have to generate one first.

## Decisions for the owner

1. **doc-coauthoring:** confirm its license before importing it.
2. **Fixes:** approve fixes for the four "fix first" skills (`claude-api` needs a rename, since `claude` and `anthropic` are reserved), or import them as they are.
3. **discernment-nudge:** it appends follow-up questions to every substantive answer. Decide whether it belongs in the hub.

## Absorbed into my working set

- **Already loaded here** (same names, content not compared): docx, pdf, pptx, xlsx, skill-creator, claude-api, frontend-design.
- **New:** `webapp-testing` (review `shell=True` before running it), `mcp-builder`, and SkillEvaluator's `skillevaluator` skill, which I'd use to check other skills.
- **Held:** `doc-coauthoring` (no license).
- **Not absorbed:** `discernment-nudge` (unrequested behavior) and `academy-guide` (end-user onboarding). Both stay in the hub only.

## Re-running the checks

From a SkillEvaluator checkout (`uv sync --python 3.13` first):

```
uv run --python 3.13 skillevaluator quality-check <skill-dir> -r cli -r json -o <out>
uv run --python 3.13 skillevaluator validate <skill-dir> --tiers 1 --no-llm -o <out>
uv run --python 3.13 skillevaluator harness-check <skill-dir>
```

Raw HTML and JSON reports are not committed. They are large (about 13 MB), and the manifest records their results.
