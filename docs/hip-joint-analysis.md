# Hip Joint Analysis: The Coupled ±45° Actuator Configuration

*Design rationale for Freyja's hip actuation. This document covers the motivation for a coupled diagonal actuator layout over an independent pitch/roll arrangement, the supporting kinematics, and the tradeoffs accepted.*

---

## 1. Problem

The hip is the most heavily loaded joint in bipedal locomotion. Each hip must support body weight through single-support phases, drive the swing leg, and stabilize the pelvis against lateral collapse. For Freyja's mass envelope, biomechanical scaling from normalized Winter / De Leva data places peak demands on the order of **~120–124 Nm** in both the pitch (flexion/extension) and roll (ab/adduction) axes.

The naive solution is two independent actuators, one per axis, each sized for its respective peak. This is simple to control but pays for that simplicity in mass: each actuator must individually carry the full peak of its axis, and the roll actuator in particular spends almost all of its duty cycle far below the capacity it is sized for.

The design question is whether a different arrangement can use the *same* actuators more efficiently across a real gait cycle.

---

## 2. The coupled configuration

Instead of aligning the two actuators with the pitch and roll axes, they are mounted on the diagonals — at ±45° to those axes. Joint torques are then produced by *combinations* of the two actuator outputs rather than one actuator each.

With actuator torques $\tau_a, \tau_b$ and joint torques $\tau_p$ (pitch), $\tau_r$ (roll), the mapping is a 45° rotation:

$$
\begin{bmatrix} \tau_p \\ \tau_r \end{bmatrix}
=
\frac{1}{\sqrt{2}}
\begin{bmatrix} 1 & -1 \\ 1 & \phantom{-}1 \end{bmatrix}
\begin{bmatrix} \tau_a \\ \tau_b \end{bmatrix}
$$

Inverting (the matrix is orthogonal, so the inverse is its transpose) gives what each actuator actually sees:

$$
\begin{bmatrix} \tau_a \\ \tau_b \end{bmatrix}
=
\frac{1}{\sqrt{2}}
\begin{bmatrix} 1 & \phantom{-}1 \\ -1 & 1 \end{bmatrix}
\begin{bmatrix} \tau_p \\ \tau_r \end{bmatrix}
\qquad\Rightarrow\qquad
\tau_a = \frac{\tau_p + \tau_r}{\sqrt{2}},\quad
\tau_b = \frac{-\tau_p + \tau_r}{\sqrt{2}}
$$

Each actuator carries a *blend* of pitch and roll demand. This single fact drives every consequence below.

---

## 3. Sizing: designed for the worst case

The peak load on an actuator occurs when pitch and roll demands peak **simultaneously and with matching sign**:

$$
\tau_{a,\text{max}} = \frac{\tau_{p,\text{peak}} + \tau_{r,\text{peak}}}{\sqrt{2}}
$$

Each actuator is sized to meet this simultaneous-peak case, with an additional safety factor applied on top. This is deliberately conservative: in practice the two axes rarely co-peak (Section 4), so the actuators are specified for a load they will almost never actually see. The penalty is some mass and cost above the theoretical minimum; the benefit is that the system has guaranteed headroom under any loading the controller might command, including off-nominal events. For a system intended to walk and fall and recover, that margin is worth paying for.

---

## 4. Why it pays off: phase offset and RMS load

The conservative sizing above describes the *worst case*. Normal gait does not look like the worst case.

In human walking, the pitch and roll torque profiles are **phase-offset**: peak hip flexion/extension demand and peak ab/adduction demand occur at different points in the gait cycle. The actuators rarely, if ever, see both peaks at once. Because each actuator carries the *sum* $\tfrac{\tau_p + \tau_r}{\sqrt2}$, and because $\tau_p$ and $\tau_r$ are seldom both large together, the instantaneous load on each actuator is, for most of the cycle, substantially below its rating.

The thermal consequence is the main prize. Actuator heating scales with RMS torque, not peak torque. By distributing two phase-offset demands across two shared actuators, the coupled layout flattens the torque profile each actuator experiences — clipping the spikes that an independent, axis-aligned actuator would absorb alone. Lower RMS load means lower steady-state winding temperature, which directly extends service life and reduces thermal derating. The system spends more of its duty cycle in an efficient operating band rather than lurching between idle and peak.

In short: sized for a coincidence that rarely happens, but operating — most of the time — well inside its comfortable regime.

---

## 5. The speed benefit

The same rotation that mixes torques also mixes velocities. For a pure pitch motion ($\dot\theta_r = 0$), the kinematics give:

$$
\dot\theta_a = \frac{\dot\theta_p}{\sqrt{2}},\qquad
\dot\theta_b = -\frac{\dot\theta_p}{\sqrt{2}}
$$

so the achievable pitch rate is

$$
\dot\theta_{p,\text{max}} = \sqrt{2}\,\dot\theta_{a,\text{max}}.
$$

Both actuators contribute to pitch motion, and the diagonal geometry amplifies their combined output speed by **√2** relative to a single actuator.

In principle this √2 speed gain would be irrelevant if both axes demanded high angular velocity at once — you cannot spend the same speed budget twice. But in practice **roll requires very little angular displacement or rate**: ab/adduction sweeps a small range slowly compared to the large, fast arcs of flexion/extension during swing. Because roll barely draws on the velocity budget, pitch captures nearly the full √2 benefit. The axis that needs the speed is the one that gets it.

---

## 6. Smaller, lighter actuators via higher reduction

The speed amplification has a direct mass payoff. Because the coupled configuration multiplies output speed by √2, the actuators themselves can run at a **higher gear reduction** than an axis-aligned design would allow while still meeting the pitch velocity requirement at the joint.

Higher reduction means each actuator can produce its required joint torque from a smaller, lighter motor — torque-dense at the output, with the geometry making up the speed that the higher reduction would otherwise cost. The result is lower actuator mass at the hip, which (because hip actuators sit relatively proximally) is favorable for the whole-leg inertia budget and consistent with Freyja's broader strategy of keeping mass proximal.

---

## 7. Tradeoffs accepted

The coupled configuration is not free, and the costs are worth stating plainly:

**Control complexity.** What was conceptually one DOF per axis is now two actuators that must be coordinated for *every* hip motion. Pitch and roll are no longer independently commanded — the controller continuously resolves both axes into paired actuator setpoints through the transform above. This is tractable (the mapping is linear and constant) but it is genuinely more involved than driving two independent joints.

**Sensitivity to asymmetry.** Because both actuators contribute to both axes, any mismatch between them — friction, backlash, calibration drift, thermal variation — couples into *both* pitch and roll rather than staying contained to one axis. The configuration demands well-matched hardware and careful calibration to avoid cross-axis error.

**Conservative mass penalty.** As noted in Section 3, sizing for the rarely-seen simultaneous peak carries a mass cost relative to the theoretical optimum. This is an accepted, deliberate tradeoff in favor of margin and longevity.

---

## 8. Summary

| Property | Independent (axis-aligned) | Coupled (±45°) |
|---|---|---|
| Actuators per peak | Each carries its own axis peak | Load shared across both |
| RMS thermal load | Spikes absorbed per-axis | Flattened by phase offset |
| Pitch speed | 1× actuator | √2 × actuator |
| Achievable reduction | Limited by pitch speed need | Higher (speed amplified) |
| Actuator mass | Higher | Lower |
| Control | Simple, decoupled | Coupled, requires coordination |
| Asymmetry sensitivity | Contained per axis | Couples into both axes |

The coupled ±45° configuration trades control simplicity for a meaningfully better duty cycle: lower RMS load and longer life in normal operation, a √2 speed benefit that accrues almost entirely to the axis that needs it, and lighter actuators via higher reduction — all while retaining full worst-case headroom by design.

---

*Specifications reflect current design targets and will be refined as first-order multibody validation (MuJoCo/OpenSim) is brought online.*

