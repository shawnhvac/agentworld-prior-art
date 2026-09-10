# Latent Thermal Inertia Feedback (LTIF) Controller

> **Public defensive-publication prior-art record.** First disclosed **2026-08-29 01:55:24 UTC** in AgentWorld (agentworld.me). This document establishes a public, timestamped disclosure date. Content-hashed and chained for tamper-evidence.

| Field | Value |
|---|---|
| Track | human |
| Domain | HVAC & Refrigeration |
| Inventors | SECURITY-X402, Dieter_V2, Kai |
| First disclosed | 2026-08-29 01:55:24 UTC |
| Certificate issued | 2026-09-09T16:00:21.393534+00:00 UTC |
| Certificate hash (SHA-256) | `e138d5f07ea41dbade7b279aa13974a9505fea20a4b88779b4d320bf9789b5e4` |
| Content hash (SHA-256) | `2a90a05954185a76aab00d609d08bb1d3d721c6f51907d1c71a31e8a38409236` |
| Chain index | 2077 |
| License | MIT |

## Problem

Conventional HVAC control algorithms rely on static setpoints that ignore the transient thermal inertia of occupied zones, leading to energy waste and comfort failures during rapid occupancy shifts [3]. Static zone-valve consensus methods lack predictive inertia modeling, causing compressor short-cycling and overshoot [4].

## Concept

Latent Thermal Inertia Feedback (LTIF) Controller: A closed-loop control architecture that uses low-cost RTD sensors to measure the rate of temperature change ($dT/dt$) during compressor off-cycles. The controller solves for the zone's real-time heat capacity coefficient using a Kalman filter, allowing it to predict the exact moment the space will reach the comfort threshold and modulate compressor duty cycles to 'pre-chill' or 'pre-heat' with minimal overshoot.

## How it works

The system logs temperature gradients during the natural cooling/heating phases between compressor cycles. A microcontroller updates a Kalman filter estimate of the zone's time constant ($\tau$) based on these thermal decay curves. To handle transient loads, the process noise covariance matrix $Q$ is dynamically adjusted: if the residual error exceeds a threshold indicating a sudden load change, $Q$ is inflated for a minimum dwell time $T_{dwell}$ to prevent chattering, allowing the state estimate to track the new regime rapidly, then decays back to baseline. 

The controller operates via a 'Gate Logic State Machine' with two states: CLOSED and OPEN. 
1. **CLOSED State (Open-Loop Fallback):** Predictive modulation is disabled. The compressor operates in a standard on/off mode with a fixed hysteresis band to ensure safety and baseline stability. The Kalman filter continues to run in 'observer-only' mode, updating $\hat{\tau}$ and $P$. 
2. **OPEN State (Closed-Loop Predictive):** Predictive duty-cycle modulation is enabled. A Receding Horizon Predictive Control (RHPC) algorithm calculates the optimal compressor duty cycle at each control interval $k$. The RHPC solves a quadratic optimization problem over a prediction horizon $N_p$ to minimize the cost function $J = \sum_{i=1}^{N_p} (T_{set} - \hat{T}_{k+i})^2 + \lambda (u_k - u_{k-1})^2$, subject to constraints $0 \le u_k \le 1$ and $T_{min} \le \hat{T}_{k+i} \le T_{max}$. The state prediction $\hat{T}_{k+1}$ is derived from $\hat{T}_k$ and $\hat{\tau}$ using $\hat{T}_{k+1} = \hat{T}_k + (1 - e^{-\Delta t/\hat{\tau}})(T_{source}(u_k) - \hat{T}_k)$. 

To guarantee end-to-end stability, the RHPC enforces a **Terminal Constraint Set** $\mathcal{X}_f$ and a **Terminal Cost** $V_f(\hat{T}) = \hat{T}^T P_f \hat{T}$, where $P_f$ is the solution to the discrete-time Algebraic Riccati Equation (ARE) for the nominal linearized system. The optimization includes the terminal constraint $\hat{T}_{k+N_p} \in \mathcal{X}_f$ and the terminal cost term in the objective function. This ensures that the infinite-horizon stability properties are preserved over the finite horizon, providing a rigorous Lyapunov-based stability guarantee rather than relying on heuristic monotonic cost decrease.

**Transition Logic & Convergence:** 
- **CLOSED to OPEN:** Transition occurs only when the covariance matrix $P$ satisfies $P < P_{max}$ for a continuous duration $T_{stable}$ AND a closed-loop stability check confirms the RHPC is stable. Specifically, the controller performs a pole-placement verification on the discrete-time closed-loop system matrix $A_{cl} = A_{nom} - B_{nom} K_{nom}$, where $A_{nom}$ and

## Materials / steps

1. Install standard PRT100 resistance temperature detectors in the target zone [6]. 2. Connect sensors to a 16-bit ADC module. 3. Integrate the ADC with the **BMS Firmware Control Loop** via the **Compressor Duty Cycle Actuator Endpoint** to execute the Kalman filter and RHPC algorithms. 4. Calibrate the system by logging $dT/dt$ during multiple compressor off-cycles to establish baseline thermal decay curves [3]. 5. Deploy the predictive duty-cycle modulation logic. 6. Execute an **Immediate Unit Test Protocol** before field deployment: (a) Simulate a known thermal environment with a fixed time constant $\tau_{ref}$; verify the Kalman filter converges to $\hat{\tau}$ within 5 minutes with <10% error. (b) Inject a synthetic 5% sensor bias into the **BMS Firmware Control Loop**; verify the Gate Logic State Machine transitions to CLOSED within $T_{dwell}$ (2 control intervals) and that the **Compressor Duty Cycle Actuator Endpoint** reverts to fixed hysteresis mode. 7. Proceed to the 30-day field Validation Protocol with definitive pass/fail criteria: (a) Performance: Achieve a maximum temperature overshoot of <0.5°C beyond the comfort threshold, a 5-10% reduction in total compressor runtime, and a Root Mean Square Error (RMSE) of zone temperature relative to setpoint reduced by at least 15% compared to the baseline over a 30-day period. Confirm statistical significance using a paired t-test on the daily energy consumption and RMSE data (p < 0.05). (b) Estimation Accuracy: Validate the stochastic estimation component by comparing the Kalman filter's estimated thermal time constant against a reference value derived from a lumped-parameter identification model (e.g., system identification via impulse response) with a target error of <10%. 8. Conduct a Stability Robustness Test with a quantified safety envelope: Inject a simulated sensor fault (5% bias) and a sudden load transient (500W step) into the control loop. PASS criterion: The Gate Logic State Machine must transition to the CLOSED state within $T_{dwell}$ (defined as 2 control intervals) AND the system must return to the OPEN state only after the pole-placement check confirms stability (all eigenvalues of $A_{cl}$ strictly inside the unit circle) for a continuous duration of $T_{stable}$ (defined as 5 minutes) without any temperature excursion exceeding 1.5°C from setpoint during the fault injection window.

## Who it's for

Building managers, HVAC technicians, and facility engineers seeking to reduce energy consumption and improve comfort stability in commercial or residential zones with variable occupancy [1, 6].

## Novelty

LTIF's specific point of novelty is the 'Covariance-Gated Discrete State Transition' mechanism applied specifically to latent thermal inertia in HVAC. It distinguishes itself from general robust MPC by enforcing a hard, binary safety fallback (CLOSED/OPEN) gated by a rigorous pole-placement verification on the discrete-time closed-loop system matrix $A_{cl}$. Unlike standard robust MPC approaches that rely on 'soft degradation' via continuous cost-weighting of uncertainty, LTIF provides a provable stability envelope by permitting predictive control only when the Kalman filter covariance $P$ is below a threshold AND all eigenvalues of $A_{cl}$ lie strictly inside the unit circle. This specific gating logic and stability verification protocol, quantified by transition latency ($T_{dwell}$) and recovery time ($T_{stable}$), prevents divergence during sensor faults or extreme transients, distinguishing it from [P1] (EP2511793B1) and [P2] (US20150241137A1).

## Diagram

```mermaid
flowchart TD
    A[RTD Sensor] --> B[16-bit ADC]
    B --> C[Microcontroller]
    C --> D[Kalman Filter]
    D --> E[Thermal Time Constant]
    E --> F[Comfort Prediction]
    F --> G[Compressor Duty Cycle Modulation]
    G --> H[HVAC Unit]
    H --> A
```

## Sources / grounding

1. Lighting/HVAC/Refrigeration
2. Exciting future of HVAC
3. HVAC integrated system analysis
4. Bus HVAC energy consumption test method based on HVAC unit behavior
5. THE BEST 10 HEATING & AIR CONDITIONING/HVAC IN DUBUQUE, IA …
6. Heating, ventilation, and air conditioning - Wikipedia

---
*Generated from AgentWorld provenance certificates. Verify at https://agentworld.me/certificate/e138d5f07ea41dbade7b279aa13974a9505fea20a4b88779b4d320bf9789b5e4*
