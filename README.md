---
id: e648a637-ddf0-4f5a-bd9f-2394cc30de49
---

# Q — Flowpad's QA manager agent

Q is an agent asset extracted from the Flowpad OSS repo (`langware-labs/flowpad`).
It bundles the agent definition, its avatar, and the `e2e-qa` skill Q delegates to.

## Contents

```
agentic-assets/agent/q/
  agent.md        # Q's frontmatter + system prompt (id 004f3ab7-d33b-48c0-ae0e-6e61e181a343)
  avatar.png      # 1254x1254 PNG, sha256 438b5806edae1c9eaf1da9950a9735d2a11af7aafb84e445338f2a452635e8f8
.claude/skills/e2e-qa/
  SKILL.md        # id ae32bd1d-2fca-50c2-bf33-fa24a06aad61 — the skill Q's prompt names
  agents/         # qa-tester, test_debugger, bug_fixer, testing_analysis_expert
  modes/          # qa-cycle, run, debug, analyze, report, bug-detector, team-setup, reference
  schemas/        # test-result + cycle-report JSON schemas
  examples/       # sample result + cycle report
  templates/      # HTML report template
  e2e_qa_cleanup.py
```

Both files are byte-identical copies of the originals; nothing in the source repo was modified.

## Install into a Flowpad project

Copy the two trees into the target project root, keeping the paths:

```bash
cp -r agentic-assets/agent/q  <project>/agentic-assets/agent/q
cp -r .claude/skills/e2e-qa   <project>/.claude/skills/e2e-qa
```

Flowpad's asset indexer picks up `agentic-assets/agent/*/agent.md` and `.claude/skills/*/SKILL.md`
on the next scan. The ids in the frontmatter are preserved, so the agent→skill link
(`skills: [skill-ae32bd1d-…]`) resolves without editing.

## What was deliberately NOT extracted

* **`mcp-3d4d6687-0432-44ed-af70-853cd2ed6a82`** — Q's `mcp_servers` entry. This is a runtime
  MCP-server record in the Flowpad instance store (`~/.flow/instances/<name>/`), not a file in
  the repo; it is shared by several agents (`eranmail`, `emailer`, `asset-cleanup`,
  `artifact-setup`). Recreate or re-point it in the target install, or drop the line from
  `agent.md` if the target has no equivalent server.
* **`ui/tests/manual_regression/**`** (357 files, 2.2 MB) — the Flowpad *product's* test
  scenarios that `SKILL.md` reads via `scenarios-dir`. Product-specific, not part of Q.
* **`.flow/skills/agentic-qa/{instructions,test_index,coverage_analysis}.md`** — per-project
  learnings and index that the skill generates and maintains as it runs.
* **`tests/unit/agent/test_q_bundle.py`** — the source repo's guard test for this bundle; it
  imports `flow_sdk`, so it only runs there.

## Retargeting the skill to another project

`SKILL.md`'s frontmatter carries Flowpad-relative paths. Point them at the new project's
layout before the first cycle:

```yaml
output-dir:        ui/tests/manual_regression/_results
scenarios-dir:     ui/tests/manual_regression
fast-paths-dir:    ui/tests/manual_regression/_fast_paths
instructions-file: .flow/skills/agentic-qa/instructions.md
test-index-file:   .flow/skills/agentic-qa/test_index.md
```

`SKILL.md` also documents starting the app under test with
`uv run -m flow_sdk.server.run`; replace that with the target project's launch command.

## Provenance

Extracted from `langware-labs/flowpad`, branch `FLOWPAD-2106`, at commit `7034dcddc`.
