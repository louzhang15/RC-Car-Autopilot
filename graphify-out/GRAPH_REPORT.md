# Graph Report - Autopilot- RC Car  (2026-11-23)

## Corpus Check
- Corpus is ~2,024 words - fits in a single context window. You may not need a graph.

## Summary
- 49 nodes · 91 edges · 9 communities (6 shown, 3 thin omitted)
- Extraction: 99% EXTRACTED · 1% INFERRED · 0% AMBIGUOUS · INFERRED: 1 edges (avg confidence: 0.85)
- Token cost: unavailable; host sub-agent tool does not expose actual usage (zero placeholders are not measured cost).

## Community Hubs (Navigation)
- Mission scope and destinations
- Mission execution and evidence
- Navigation and actuator control
- Stopping and acceptance tests
- Chassis and printable mounts
- Localization and odometry
- Open hardware and initialization decisions
- Turning clearance and feasible planning
- Preflight and latched stops

## God Nodes (most connected - your core abstractions)
1. `Proposed implementation plan` - 13 edges
2. `Mission Runner (planned)` - 11 edges
3. `Product requirements v0.2` - 10 edges
4. `Planned mapping and localization` - 8 edges
5. `Chassis and hardware feasibility gate` - 8 edges
6. `Six staged delivery milestones` - 7 edges
7. `ROS 2 Nav2 planning and following` - 6 edges
8. `Planned stop supervisor` - 6 edges
9. `Planned durable local report store` - 6 edges
10. `Planned vehicle adapter and controller` - 5 edges

## Surprising Connections (you probably didn't know these)
- `Mission-Based Autonomous RC Car` --references--> `Proposed implementation plan`  [EXTRACTED]
  README.md → docs/implementation-plan.md
- `Mission-Based Autonomous RC Car` --references--> `Product requirements v0.2`  [EXTRACTED]
  README.md → docs/product-requirements.md
- `Planned vehicle adapter and controller` --shares_data_with--> `Retained chassis, steering servo, motor and ESC`  [EXTRACTED]
  docs/implementation-plan.md → docs/product-requirements.md
- `Acceptance evidence and fault checks` --conceptually_related_to--> `Clear path: at least 8 of 10 trials, two starts`  [EXTRACTED]
  docs/implementation-plan.md → docs/product-requirements.md
- `Proposed implementation plan` --references--> `Product requirements v0.2`  [EXTRACTED]
  docs/implementation-plan.md → docs/product-requirements.md

## Hyperedges (group relationships)
- **Inspection mission: destination, verified photo, return** — docs_implementation_plan_runner, docs_product_requirements_point, docs_implementation_plan_arrival, docs_implementation_plan_photo, docs_product_requirements_home [EXTRACTED 1.00]
- **Layered stopping and explicit restart** — docs_product_requirements_r04, docs_implementation_plan_supervisor, docs_implementation_plan_watchdog, docs_implementation_plan_latch, docs_implementation_plan_preflight [EXTRACTED 1.00]

## Communities (9 total, 3 thin omitted)

### Community 0 - "Mission scope and destinations"
Cohesion: 0.24
Nodes (12): Saved occupancy map and versioned destination poses, Clear path: at least 8 of 10 trials, two starts, Later rooms, rerouting and new mission actions, Home destination, Inspection mission: go, photograph, return, Inspection Point destination, R02: Named destinations, R03: Mission execution (+4 more)

### Community 1 - "Mission execution and evidence"
Cohesion: 0.43
Nodes (8): Arrival: navigation success, fresh pose and standstill, Planned inspection camera, Planned local operator interface, Required and proposed mission outcomes, Timestamped photo after verified inspection arrival, Restart interruption and ignored late callbacks, Planned durable local report store, Mission Runner (planned)

### Community 2 - "Navigation and actuator control"
Cohesion: 0.50
Nodes (8): Candidate Nav2 Collision Monitor, Six staged delivery milestones, ROS 2 Nav2 planning and following, Planned navigation adapter, Proposed implementation plan, Planned stop supervisor, Planned vehicle adapter and controller, Actuator-side command timeout

### Community 3 - "Stopping and acceptance tests"
Cohesion: 0.40
Nodes (6): Acceptance evidence and fault checks, Operating limits and tolerances not validated, Blocked path: stop without contact every trial, Manual stop and localization-loss test, R04: Safe stop without automatic resume, R05: Local reporting

### Community 4 - "Chassis and printable mounts"
Cohesion: 0.50
Nodes (5): Chassis and hardware feasibility gate, Compute, regulated power and payload budget, Bambu A1 removable printed mounts, R01: Ready-to-run retrofit, Retained chassis, steering servo, motor and ESC

### Community 5 - "Localization and odometry"
Cohesion: 0.50
Nodes (4): Planned mapping and localization, Measured wheel odometry / encoders, Range observations and sensor coverage, map → odom → base_link transforms

## Knowledge Gaps
- **5 isolated node(s):** `Planning only: no executable control software`, `Supervised single-level first release`, `Later rooms, rerouting and new mission actions`, `Compute, regulated power and payload budget`, `map → odom → base_link transforms`
  These have ≤1 connection - possible missing edges. (Counts symbols only; 5 node(s) total have ≤1 connection when file, concept and rationale nodes are included.)
- **3 thin communities (<3 nodes) omitted from report** — run `graphify query` to explore isolated nodes.

## Suggested Questions
_Questions this graph is uniquely positioned to answer:_

- **Why does `Proposed implementation plan` connect `Navigation and actuator control` to `Mission scope and destinations`, `Mission execution and evidence`, `Localization and odometry`, `Open hardware and initialization decisions`?**
  _High betweenness centrality (0.342) - this node is a cross-community bridge._
- **What connects `Planning only: no executable control software`, `Supervised single-level first release`, `Later rooms, rerouting and new mission actions` to the rest of the system?**
  _5 weakly-connected nodes found - possible missing edges._
- **Why does `Product requirements v0.2` connect `Mission scope and destinations` to `Navigation and actuator control`, `Stopping and acceptance tests`, `Chassis and printable mounts`?**
  _High betweenness centrality (0.270) - this node is a cross-community bridge._
- **Why does `Six staged delivery milestones` connect `Navigation and actuator control` to `Mission execution and evidence`, `Stopping and acceptance tests`, `Chassis and printable mounts`, `Localization and odometry`?**
  _High betweenness centrality (0.168) - this node is a cross-community bridge._

## Snapshot scope

This graph describes requirements and proposed components, not implemented software. Only README.md and the two project documents were extracted. Local chat logs and credentials were not indexed. Graphify version: 0.9.110.

The graphify-out/ viewer, report, and JSON are intended to be shared in Git; machine-local caches and intermediate state are ignored.
