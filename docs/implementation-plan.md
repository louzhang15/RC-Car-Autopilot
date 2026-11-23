# Implementation plan

This plan proposes engineering decisions for [requirements v0.2](product-requirements.md). It does not change the product requirements or claim that hardware has passed acceptance testing.

## Architecture

| Component | Responsibility |
|---|---|
| Local operator interface | Load a map, save named poses, start a mission, cancel, and inspect local reports. |
| Mission runner | Sequence navigation to Inspection Point, arrival verification, photo capture, and navigation to Home. Own mission state and reporting. |
| Navigation adapter | Submit and cancel Nav2 goals; expose navigation feedback and results to the mission runner. |
| Mapping and localization | Produce/load the room map and estimate the rover's current pose using range observations and odometry. |
| Nav2 | Plan and follow paths compatible with the car's geometry. |
| Stop supervisor | Inhibit driving on blockage, invalid localization, cancellation, or stale required inputs. Latch the stop until an explicit new operator command passes preflight. |
| Vehicle adapter and controller | Convert permitted motion commands to calibrated steering/throttle; enforce limits and a command timeout close to the actuator. |
| Inspection camera and report store | Save a timestamped photo and durable mission results locally. |

Proposed motion flow: Nav2 → stop supervisor → vehicle adapter → stock servo/ESC. The mission runner must not command the actuators directly. Actuator-side timeout handling must still stop the car if the onboard computer stops sending commands.

## Chassis and hardware gate (R01)

Before selecting or purchasing a chassis, record:

- Room dimensions, usable floor area, obstacles, and clearance for the entire swept vehicle footprint.
- Wheelbase, body dimensions including mounts, steering limits, and measured minimum turning radius in both directions.
- Whether turns into both destination poses are feasible and whether reverse is necessary. Validate reverse behavior separately before enabling it.
- Lowest controllable speed, ESC neutral/brake behavior, stopping distance, and repeatability on the actual floor.
- Servo/ESC signal requirements and a removable interface that preserves the stock drivetrain.
- Available mounting area, payload allowance, power budget, regulated supply, and runtime target.
- A workable source of measured odometry, such as wheel encoders. Throttle commands alone are not a validated position measurement.
- Range-sensor coverage and blind spots, including whether low obstacles can be detected; camera view at Inspection Point.

Print removable mounts with the Bambu A1 after checking dimensions, sensor sight lines, steering clearance, cable routing, and service access.

## Navigation integration (R02–R04)

Proposed starting point: a saved occupancy map, range-based localization, measured wheel odometry, and an optional IMU. Choose the ROS 2 distribution and compatible package versions before writing launch files or copying configuration examples.

Maintain the transform chain `map → odom → base_link` and fixed transforms to sensors. Store Home and Inspection Point as position and heading in the map frame, tied to the map identifier/version. Refuse a mission if either destination belongs to a different map.

Do not assume that loading a map establishes the car's pose. Provide an initial-pose workflow and verify localization from each acceptance-test start. Whether initialization is operator-assisted or automatic remains an explicit decision.

The car cannot rotate in place. Evaluate a planner that accounts for turning radius and the full footprint, together with a compatible controller. Nav2's [navigation plugin guidance](https://docs.nav2.org/jazzy/configuration_and_development/first_time_robot_setup_guide/navigation_plugins/setup_navigation_plugins/) describes these constraints; [Smac Hybrid-A*](https://docs.nav2.org/jazzy/configuration_and_development/configuration_guide/planners_plugins/smac/smac_hybrid/configuring_smac_hybrid/) is a candidate with a minimum-turning-radius parameter. These links are references, not a selection of Jazzy for this project.

Configure recovery behavior for car-like motion. Disable spin-in-place recovery and automatic retry/resume after a stop. Once the supervisor declares a blockage, cancel navigation and latch the stop even if the obstacle clears. Alternative-route handling belongs to a later milestone.

Nav2's [Collision Monitor](https://docs.nav2.org/jazzy/configuration_and_development/configuration_guide/core_servers/collision_monitor/configuring_collision_monitor_node/) can contribute an independent software stop path; its documentation does not claim hard real-time safety certification. Validate stopping with the actual drivetrain and command watchdog.

## Mission behavior and reports (R03–R05)

Proposed success sequence:

`idle → preflight → navigating_to_inspection → verifying_arrival → capturing_photo → navigating_home → verifying_home → completed`

Preflight requires valid localization, fresh required sensors, valid destinations, working local storage, and an explicitly armed vehicle. Reject a start request while another mission is active.

Arrival requires a successful navigation result, a fresh valid pose within agreed position/heading tolerances, and confirmed standstill. Capture only after inspection arrival; confirm Home arrival before marking the mission completed. A failed capture or file write cannot count as success.

Every active step must handle operator cancellation, blockage, and localization loss. Stop/inhibit motion, cancel the active navigation goal, record the interrupted step, and finalize the mission. Ignore late navigation or camera callbacks for finalized mission IDs. Restored sensor data, a cleared obstacle, or a process restart must not resume an interrupted mission. A fresh operator command starts a new mission after preflight.

Use the required outcomes `completed`, `path_blocked`, `localization_lost`, and `operator_canceled`. Proposed additional outcomes are `preflight_failed`, `navigation_failed`, `capture_failed`, `system_fault`, and `interrupted`; distinguish a confirmed blocked path from an unrelated navigation error. Record the first terminal cause and subsequent fault details without overwriting the cause.

For each run, persist a unique mission ID, map/version, destination poses, start/end timestamps, monotonic elapsed time, completed steps, interrupted step, outcome/reason, and photo path/capture timestamp when available. Preserve a captured photo if the return trip fails. Save step progress as it occurs so a crash leaves evidence; mark unfinished runs interrupted on restart.

## Delivery milestones

| Milestone | Work | Exit evidence |
|---|---|---|
| 1. Feasibility | Measure room and candidate chassis; choose odometry, sensors, compute, and power. | Recorded turning/footprint clearance and a reviewable hardware selection with unresolved risks listed. |
| 2. Bench control | Implement calibrated adapter, arming, speed limits, cancellation, and watchdog. | Wheels-raised checks followed by supervised low-speed stopping measurements on the floor. |
| 3. Mapping and localization | Integrate transforms, measured odometry, range sensor, saved map, and named poses. | Repeatable localization from at least two starts with documented initialization. |
| 4. Navigation | Configure feasible planning/following and latched stops. | Reach each destination independently; demonstrate blocked-path, localization-loss, and cancellation stops. |
| 5. Mission and reporting | Implement the mission runner, verified capture, return, and local reports. | Full mission in simulation or a mocked integration harness, then supervised hardware runs. |
| 6. Acceptance | Run the release matrix below. | Trial logs, photos, and measured results against every product requirement. |

## Acceptance evidence

Before trials, agree and record the speed cap, arrival tolerances, standstill criterion, localization validity thresholds/timeouts, sensor/command timeouts, stop clearance, and mission timeout. Values must come from measurements; none are validated yet.

| Requirement | Test and evidence |
|---|---|
| R01 | Inspect retained stock components and removable printed mounts; record fit and power checks. |
| R02 | Save/load the room map and destinations; establish valid localization from at least two different starts. |
| R03 | Run 10 clear-path trials, proposed as five from each of two starts. At least eight must reach Inspection Point, save a photo after arrival, and return Home without manual steering. |
| R04 | Block outbound and return legs; separately cancel and invalidate localization during each moving leg. Require no contact in every blocked-path trial and no automatic restart after any stop. Record stopping distance and time. |
| R05 | Inspect each trial's report for steps, elapsed time, timestamped photo if captured, interrupted step, and accurate outcome. A stopped/failed mission must never be marked complete. |

Also verify command loss, stale range data, camera/storage failures, and process restart. These are proposed robustness checks in addition to the product acceptance tests. Fix the number of stop/fault trials before running the campaign, and retain failed trials in the results.

## Decisions still required

1. Test-room dimensions and candidate chassis geometry.
2. Compute platform, ROS 2 distribution, range sensor, odometry hardware, camera, and power design.
3. Initial-pose workflow and whether reverse is needed for the first room.
4. Measured operating limits, localization criteria, arrival tolerances, and trial counts for stop/fault cases.
5. Minimal local operator interface and report storage format/location.
