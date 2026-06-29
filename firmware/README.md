# firmware/

Embedded firmware for Freyja's distributed joint controllers.

Low-level, real-time motor control runs on **STM32** microcontrollers local to each joint, handling the control loops that have hard latency requirements. Higher-level coordination and inference run on a separate compute module off-board of this layer.

## Status

Early. Architecture is defined; implementation begins alongside the first single-joint hardware testbed. This directory will hold the per-joint control firmware, communication layer, and calibration routines as they are developed.
