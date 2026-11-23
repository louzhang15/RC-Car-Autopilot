# Claude Code project instructions

Read and follow [AGENTS.md](AGENTS.md), the shared workflow for Claude and Codex.

Use the installed Graphify skill and the same `graphify-out/graph.json` as Codex. Prefer a focused `explain` call for a known node, or start with a query requesting an 800-token budget, then verify only the relevant source sections. The budget is approximate and the installed CLI can exceed it. Update the graph after code changes and refresh semantic extraction when architecture documents change. Do not load the full graph or report as routine session context.
