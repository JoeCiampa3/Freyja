# Freyja

**A full-scale humanoid robot built as an exercise in first-principles mechanical design.**

Freyja is an independent, long-term engineering project: a 170 cm, 65 kg bipedal humanoid with a carbon-fiber-reinforced-polymer (CFRP) structural skeleton, designed from the ground up around the physical constraints of human-scale locomotion. The goal is not to assemble off-the-shelf modules into an approximate humanoid shape, but to derive each subsystem from the underlying mechanics — joint torque budgets from biomechanics, actuator selection from load analysis, structural geometry from impact and fatigue considerations.

An emphasis is put on elegant and 

This repository documents the design methodology, engineering rationale, and current development state.

<!-- Replace with an isometric render of the pelvis/hip assembly. -->
<!-- ![Freyja pelvis assembly](docs/img/pelvis-iso.png) -->

---

## Project status

**Current phase: detailed design.** The system-level architecture is settled and CAD development is underway, currently focused on the pelvis and lower-limb assemblies. The near-term engineering milestone is a validated single-leg test stand before progressing to a dual-leg walking platform.

This is a design-first project. The work that matters most at this stage lives in the analysis and rationale behind each decision — see [`docs/`](docs/) — as much as in the CAD itself.

---

## Design approach

The project is organized around a few principles that drive every subsystem decision:

**Derive, don't guess.** Joint actuator requirements come from biomechanical torque analysis, not from picking a motor that "looks strong enough." The hip torque study ([`docs/hip-joint-analysis.md`](docs/hip-joint-analysis.md)) is representative: peak pitch and roll demands were computed independently (~120–124 Nm), and that near-symmetry is what justified the coupled ±45° diagonal actuator configuration.

**Separate the things that should be separate.** Load-bearing structure and actuation are treated as distinct problems. A remote cable-driven scheme on selected axes keeps actuators off the structural members that absorb ground-contact impacts, protecting drivetrain components from shock loads.

**Match the controller to the task.** Compute is split across two tiers: real-time, low-level motor control runs on dedicated STM32 microcontrollers at each joint, while a higher-level compute module handles inference and coordination. Each layer runs where its latency and throughput requirements are actually met.

**Modularity:** components are designed with invariant interfaces and wiring hubs such that a design update in one component does not propogate downstream. Furthermore, there is effort invested in designing each subassembly to be modular as well, e.g. actuators can be updated and swapped out as required without requiring a fundamental design update.

---

## System overview



Freyja is designed as a layered stack, each level resting on the one beneath it — from the structural foundation up to whole-body coordination.

| Layer | Implementation |
|---|---|
| **Structural skeleton** | CFRP frame; segment lengths and mass properties derived directly from normalized Winter/De Leva anthropometric data |
| **Joints & actuation** | Full human ROM at each joint; remote cable-driven actuation throughout for functional separation and proximal mass placement |
| **Simulation & validation** | Zeroth-order: human biomechanical data drives actuator sizing and initial mass placement in CAD. First-order: simplified multibody models targeting MuJoCo/OpenSim for iterative refinement before hardware commitment |
| **Software architecture** | Distributed STM32 microcontrollers for real-time joint-level control; onboard inference module for whole-body coordination and high-level reasoning |




---

## Repository structure

```
freyja/
├── docs/        Design documentation and engineering analysis
│   └── img/     Renders, diagrams, photos
├── cad/         Mechanical models (Fusion 360, exported to STEP)
├── firmware/    STM32 motor-control firmware
└── analysis/    Scripts and notebooks for torque/kinematic studies
```

---

## Background

Freyja is developed independently by Joe Ciampa, a physics graduate and college mathematics and physics instructor pursuing graduate study in robotics. The project doubles as the practical counterpart to that academic trajectory — a place to work real mechanical, control, and embedded-systems problems end to end.

*This README describes a project under active development; specifications reflect current design targets and will evolve as subsystems are validated.*
