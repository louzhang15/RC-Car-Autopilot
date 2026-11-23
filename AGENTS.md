# Shared Claude / Codex project workflow

## Graphify first

Use the installed Graphify skill to locate project context before opening source files. Both agents must use this checkout's `graphify-out/graph.json`; do not create separate agent-specific graphs.

1. Start with a focused `graphify query "<topic>" --budget 800`. Use the skill's vocabulary expansion if the question does not match graph labels. Print only relevant labels when inspecting vocabulary, not the whole graph JSON.
2. For a known concept, prefer `graphify explain "<node label>"`. For relationships, use `graphify path "<node A>" "<node B>"` and choose traversal direction deliberately.
3. Follow the returned `source_file` and `source_location`. Read only the relevant heading or line range to verify the result. In this installed version, `graphify section` fails on this graph; use targeted source reads instead.
4. If a query is truncated or verbose, narrow it or explain one node before raising the budget. An 800-token budget is approximate, can be exceeded by the installed CLI, and does not guarantee complete coverage. Bound tool output too, while preserving truncation warnings.
5. If the graph has no match, appears stale, or omits necessary context, use focused `rg` and targeted source reads. Source files are authoritative; absence from the graph is not proof that something does not exist.

Do not routinely read `graph.html`, all of `graph.json`, the full report, or every project document into context. The viewer is optional; the graph is primarily a reusable retrieval index. Do not rebuild it on every question.

## Freshness and scope

- If the graph is missing, run `graphify extract . --code-only` for code. This project currently has no application code, so an empty code-only extraction is expected; use the Graphify skill's semantic document pass for the requirements and implementation plan.
- Run `graphify update .` after each batch of code edits and before handoff. When project requirements or architecture change, also refresh their semantic extraction through the skill; a code-only update cannot refresh document concepts.
- Keep ordinary non-code work, private chat logs, credentials, and generated graph outputs out of the source corpus. The current semantic corpus is `README.md`, `docs/product-requirements.md`, and `docs/implementation-plan.md`. Add future code through AST extraction.
- Instruction files and graph usage guidance are workflow configuration, not rover-domain knowledge. Do not semantically index them.
- Keep portable graph artifacts in Git. Keep machine-specific root/interpreter state, caches, and intermediates local. Do not overwrite a useful graph with an empty extraction.

## Working rules

Use sub-agents for useful independent tasks and verification. Give them a focused graph question and bounded context rather than copying the whole repository. Verify their results against relevant sources before reporting.

The graph currently describes a proposed supervised indoor rover. Planned components are not implemented features or validated hardware. Do not claim measured token savings without an actual measurement; for this small repository, graph overhead can exceed the cost of a direct targeted read.
