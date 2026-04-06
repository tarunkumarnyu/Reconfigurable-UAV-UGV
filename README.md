# Reconfigurable UAV + UGV

[![Status](https://img.shields.io/badge/Status-Built%20%26%20Flown-1D9E75)](#)
[![Inspiration](https://img.shields.io/badge/Inspired%20by-CalTech%20M4%20Morphbot-4A90D9)](https://www.caltech.edu/about/news/caltech-engineers-design-a-robot-that-can-walk-fly-and-skateboard)
[![Flight Controller](https://img.shields.io/badge/FC-STM32F405-D85A30)](#)
[![Frame](https://img.shields.io/badge/Frame-PLA%2B%20%2B%20Carbon%20Fibre-666)](#)

A hybrid robot that morphs between **quadrotor (UAV)** and **two-wheeled rover (UGV)** modes — inspired by the CalTech M4 Morphbot. The vehicle flies to a search location for rapid response, then transitions to ground mode in cluttered terrain to gain manoeuvrability and dramatically cut power consumption (dynamically stable → statically stable).

Targeted at search-and-rescue missions where pure ground robots get stuck and pure quadrotors burn through battery hovering.

## Demo

<!-- Drag-and-drop reconfig_demo.mp4 here in the GitHub web editor to embed -->

https://github.com/tarunkumarnyu/Reconfigurable-UAV-UGV/raw/main/assets/reconfig_demo.mp4

## Configurations

<p align="center">
  <img src="assets/Conf.png" width="85%" alt="UAV and UGV configurations"/>
</p>

| Mode | Actuators | When it's used |
|---|---|---|
| **UAV** | 4× BLDC motors | Long-distance traversal, gap-crossing, rapid deployment to a waypoint |
| **UGV** | 2× DC motors driving the wheels through a 1:10 gear ring | Cluttered indoor / collapsed-structure search where flight is unsafe or wasteful |
| **Reconfiguration** | 2× servos rotating the motor-arm assemblies | Switches between modes in place |

Total of **8 actuators**: 4 BLDC + 2 DC + 2 servos.

## Specifications

| | |
|---|---|
| Take-off weight | 1134 g |
| Thrust per motor | 340.2 g (≈1.2× hover thrust) |
| Power required | 31.08 Wh |
| Estimated endurance | 4.21 min (UAV mode) |
| Payload limit | 100 g |
| Length × height | 310 mm × 230 mm |
| Wheelbase (motor mount, diagonal) | 225 mm |
| Wheel diameter | 150 mm |
| Propeller clearance | 33 mm |
| Landing-gear height | 50 mm (15 mm ground clearance in UGV mode) |

<p align="center">
  <img src="assets/sizing.png" width="85%" alt="Dimensions and sizing"/>
</p>

## System Architecture

<p align="center">
  <img src="assets/Architecture.png" width="100%" alt="Electrical / control architecture"/>
</p>

**Flight controller.** STM32F405 ARM microcontroller with 6× UART for peripherals. Runs the UAV control loop and routes commands during reconfiguration.

**Ground controller.** Separate ATmega328-based board drives the H-bridge for the DC wheel motors in UGV mode — keeps the high-current ground drive isolated from the flight controller.

**Sensing.**
- **DPS310 barometer** — altitude hold.
- **Ublox NEO-M8N GPS** — 0.6–0.9 m position accuracy, on FC UART6 for waypoint navigation.
- **Compass** — heading reference for waypoint following, on I²C.
- **2.4 GHz SBUS receiver** — manual override, on UART2.

**FPV.** 1200 TVL 5 MP camera + 5.8 GHz video transmitter streaming live to the ground station's FPV display.

**ESCs.** 4-in-1 integrated 55 A ESC — single board for all four BLDC motors, total weight 15 g.

**Power tree.**
- 6S 1350 mAh Li-ion → 25.2 V → 4-in-1 ESC (BLDCs)
- LM2596S buck → 12 V → H-bridge → DC wheel motors
- XL4005 buck → 7.2 V @ 5 A → reconfiguration servos

## Structural Design

The frame has three structural sub-assemblies: **central body**, **motor-mount beams**, and **wheels**. Most parts are 3D printed in PLA+; the central chassis plate is **carbon fibre** for stiffness and weight.

**Central body.** Houses the flight controller, ATmega ground controller, ESCs, receiver, and battery. The battery sits at the geometric centre to keep CG aligned for both flight and ground modes.

**Motor-mount beams.** Run parallel to the central body on each side and pivot on the reconfiguration servos. They carry the BLDC motors in flight mode and become the rolling axle in ground mode. Mounting holes integrate a bearing seat to hold the wheel inner race.

<p align="center">
  <img src="assets/bearing.png" width="70%" alt="Bearing attachment detail"/>
</p>

**Wheels.** Each wheel turns on a bearing to eliminate axle friction during ground motion. Both wheels are driven by a single DC motor through a common ring gear, so the wheels themselves are printed with **integrated gear teeth on the outer circumference** giving a **1:10 gear ratio** — high torque for climbing obstacles, in trade for top speed. The wheel webs are **topology-optimised** to remove material while preserving stiffness, also reducing aerodynamic drag in flight mode.

<p align="center">
  <img src="assets/wheel.png" width="48%" alt="Wheel with integrated gear teeth"/>
  &nbsp;
  <img src="assets/topology.png" width="48%" alt="Topology optimisation result"/>
</p>

## Why a hybrid platform

A pure quadrotor wastes energy hovering and is unsafe near survivors and rubble. A pure ground robot can't cross gaps, climb collapsed slabs, or be deployed quickly across a wide search area. The reconfigurable approach uses each mode where it's actually efficient:

- **Fly** to the search zone — fast, terrain-independent.
- **Drive** inside the zone — orders-of-magnitude longer endurance, safer around people, and the static-stability margin means the vehicle can sit still without burning power.

## Status

Built, flown, and ground-driven. Reconfiguration is mechanical (servo-driven), GPS waypoint navigation works in UAV mode, and the FPV link feeds the ground station for manual operation in either mode.

## Credits

Concept inspired by the CalTech **M4 Morphbot**. Mechanical design, electronics integration, and bring-up by Tarunkumar Palanivelan.
