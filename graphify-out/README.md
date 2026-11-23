# Project knowledge graph

This graph is primarily a persistent retrieval index for Claude and Codex. Both follow [the shared project instructions](../AGENTS.md): query first with a small output budget, then read only relevant source sections. The HTML viewer is optional.

The graph maps the project's requirements and proposed architecture. It describes a planned rover; it is not evidence of implemented software or validated hardware.

## Open the graph

Open [graph.html](graph.html) in a browser. On GitHub, download the HTML file first; the repository file view does not run it. The graph supports searching and inspecting connected concepts.

- [graph.json](graph.json): queryable graph with source references and confidence tags.
- [GRAPH_REPORT.md](GRAPH_REPORT.md): important nodes, communities, cohesion scores, and suggested questions.

## Query from the project root

With [Graphify](https://github.com/Graphify-Labs/graphify) installed:

```powershell
graphify query "How does the rover stop when localization is lost?" --budget 800
graphify explain "Mission Runner"
graphify explain "Actuator-side command timeout"
```

Labels are searchable; use the exact node labels shown in the viewer for focused `explain` and `path` commands.

Do not load the entire graph JSON or report into every conversation. Narrow truncated or verbose queries, verify answers in the cited sources, and fall back to targeted searches when the graph misses something. The installed version can exceed the requested query budget; its `section` command also failed against this graph, so use bounded source reads for headings. No model-token savings have been measured. This repository is still small, so graph overhead can outweigh savings for simple lookups.

## Refresh

Ask a Graphify-enabled coding assistant to rebuild the project graph after requirements or architecture changes. This initial graph uses semantic extraction from `README.md`, `docs/product-requirements.md`, and `docs/implementation-plan.md` because the repository has no application code yet. Generated graph files are excluded from their own source corpus.

Once code exists, use `graphify update .` after code edits. A code-only refresh does not replace the semantic document pass; rebuild document relationships when their sources change. Keep chat logs, credentials, and unrelated documents out of the corpus.

Machine-specific interpreter/root paths, caches, and intermediate files are ignored by Git. Share only the viewer, graph data, report, and this guide. Extraction token usage is unavailable from the host sub-agent tool and must not be interpreted as zero cost.
