# Mission-Based Autonomous RC Car

Convert a ready-to-run RC car into a supervised rover that carries out missions in a mapped home. The operator chooses a destination and task; the rover localizes, plans, drives, performs the task, and reports the result.

**First mission:** go to **Inspection Point**, save a timestamped photo, and return to **Home**. Both destinations belong to a saved map of one room. The rover must work from more than one starting position without teaching a route or specifying turns.

## Project status

Requirements and implementation planning only. Hardware, ROS 2 distribution, sensor models, and operating limits have not been selected or validated. There is no executable navigation or vehicle-control software yet.

## Project documents

- [Product requirements, draft v0.2](docs/product-requirements.md): scope, requirements, acceptance tests, and expansion path.
- [Implementation plan](docs/implementation-plan.md): architecture, hardware measurements, delivery milestones, and decisions to resolve.

## Approach

Use [ROS 2 Nav2](https://github.com/ros-navigation/navigation2) for planning and path following. Keep mission orchestration separate from navigation. A vehicle adapter translates motion commands into stock steering-servo and speed-controller inputs. Odometry and a range sensor support navigation; a camera captures inspection photos. Donkeycar model training is not required.

The first release is supervised, indoors, and single-level. Stairs, public roads, and unattended operation are out of scope.
