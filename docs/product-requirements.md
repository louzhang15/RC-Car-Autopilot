# Mission-Based Autonomous RC Car

**Product requirements · Draft v0.2**

## Product goal

Convert a ready-to-run RC car into a rover that carries out missions in a mapped home. The operator chooses where to go and what to do there. The rover locates itself, plans a route, performs the task, and reports the result. The operator does not teach it a route or specify each turn.

**First mission:** Go to **Inspection Point**, take a photo, then return to **Home**. Both locations are saved on a map of one room. The rover can start from more than one position.

## First release requirements

| ID | Requirement | Expected behavior |
|---|---|---|
| R01 | Ready-to-run retrofit | Keep the stock chassis, steering servo, motor, and speed controller. Use the Bambu A1 to print removable electronics and sensor mounts. |
| R02 | Named destinations | Create or load a map of one room, locate the rover on it, and save Home and Inspection Point. |
| R03 | Mission execution | Let the operator start the inspection mission. Plan from the current position, confirm arrival before taking a photo, then plan the return to Home. |
| R04 | Safe stop | Drive at low speed. Stop if the path is blocked, localization is lost, or the operator cancels. Do not resume automatically. |
| R05 | Local reporting | Record completed steps, elapsed time, the timestamped photo if captured, and the outcome. Distinguish completed, path blocked, localization lost, and operator canceled. |

## Technical approach

Use [ROS 2 Nav2](https://github.com/ros-navigation/navigation2) for route planning and following. Keep the mission program separate from navigation so new tasks can be added without changing how the rover drives. A vehicle adapter translates motion commands into steering and throttle. Use odometry and a range sensor for navigation and a camera for inspection photos. Training a Donkeycar model is not required.

Before choosing a chassis, check that its minimum turning radius fits the test room and that it has enough space for the computer, power supply, and sensors.

## Acceptance tests

- **Clear path:** Complete at least 8 of 10 trials from at least two starting positions. Each pass requires reaching Inspection Point, saving a photo, and returning Home without manual steering.
- **Blocked path:** Stop without contact in every blocked-path trial. Record the interrupted step and do not mark the mission complete.
- **Manual stop and localization loss:** Stop, record the reason, and stay stopped until the operator gives a new command.

## Expansion path

1. Add named destinations in adjacent rooms and test doorway turns.
2. Add rerouting around obstacles when a clear alternative route exists.
3. Add mission actions such as checking for a tagged object or taking photos at multiple stops.
4. Validate unfamiliar homes and private outdoor areas as separate milestones with their own acceptance tests.

The first release is supervised and single-level. Stairs, public roads, and unattended operation are out of scope.
