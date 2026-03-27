# Power and Heat Management in Embedded Systems

A graduate research survey and presentation examining power and thermal management techniques in embedded systems, with a focus on Dynamic Voltage and Frequency Scaling (DVFS). Produced for **EECS 221 — Advanced Embedded Systems** at the University of California, Irvine (2025).

> **Note:** This is a literature survey, not original research. All experimental results cited belong to the original authors and are properly attributed.

---

## Overview

Performance demands in modern embedded systems have grown faster than our ability to dissipate the resulting heat and power. This is especially critical in battery-powered and size-constrained devices — phones, medical implants, automotive controllers — where thermal failures or power overruns can have serious consequences.

This survey explores how engineers quantify, model, and manage power and heat in embedded systems, covering both classical techniques and state-of-the-art DVFS implementations.

---

## Topics Covered

### Classical Techniques
- **Dynamic Power Management (DPM)** — selectively shutting down idle components using timeout policies and Markov Decision Process-based predictive models
- **Low Power Bus Encoding** — reducing switching activity (Hamming distance) on memory buses using Gray Code, Bus-Invert, T0 (Transition), and probabilistic encoding
- **Low Power System Synthesis** — hardware vs. software partitioning tradeoffs for power efficiency

### Dynamic Voltage and Frequency Scaling (DVFS)
The core focus of the survey. Since dynamic power scales as `P = α × C × V² × f`, reducing voltage and frequency directly reduces both power consumption and heat generation. The challenge is doing this without violating real-time deadlines or performance targets.

Three research papers are analyzed in depth:

---

## Papers Analyzed

### 1. RT-TRM — Real-Time Thermal-Aware Resource Management
**Y. Lee et al., IEEE TCAD, 2018**

Key insight: most prior work ignored **ambient temperature** as a heat factor. A 15°C rise in ambient temperature caused a 29% drop in max computational power. On a single car drive, ambient temperature increased by 180%.

**Approach:**
- Defines thermal feasibility conditions: steady-state temperature must stay below `T_max`, translated into a power constraint `PD(τ) ≤ PB(T_amb)`
- Solves an optimization problem over clock frequency and task periods to maximize weighted task throughput while satisfying thermal and scheduling constraints
- Uses online idle time scheduling with slack dispersal to handle transient temperatures

**Results vs. baseline EDF schedulers:**
| System | Task Rate | Thermal Violations |
|---|---|---|
| EDF-A (1 GHz) | 100% | 76.7% of the time |
| EDF-W (0.8 GHz) | 63.5% | None |
| RT-TRM (proposed) | 79.4% | None |

---

### 2. Secure Multimode DVFS — Power-Aware Design with Security Constraints
**K. Jiang et al., ACM TODAES, 2016**

Key insight: **security tasks themselves generate heat**. Increasing intrusion detection rate or encryption rounds directly increases CPU load and thermal output — security cannot be treated as independent of the power-thermal tradeoff.

**Approach:**
- Each system "mode" (set of active tasks) has thousands of possible configurations across DVFS states, encryption strength, intrusion detection rate, and QoS levels
- Computes **Pareto-optimal configurations offline** for important/frequent modes and supermodes
- At runtime: look up saved Pareto space, or derive from nearest supermode (~0.7ms overhead)

**Results:**
- 40–80% improvement in quality of service vs. greedy methods
- Only ~3% performance loss when deriving from a supermode vs. stored configuration
- Runtime overhead under 1ms — negligible relative to task periods

---

### 3. CGM-DVFS — CPU-GPU-Memory DVFS for Heterogeneous MPSoCs
**S. Dey et al., Future Internet, 2022**

Key insight: prior DVFS work focused on a single CPU. In modern heterogeneous systems, **memory alone can account for ~20% of total power** during intensive workloads — leaving major savings on the table.

**Approach:**
- Profiles real applications (YOLO, face detection, video streaming) offline across 216 carefully clustered DVFS configurations (Big CPU × Little CPU × GPU × Memory)
- At runtime: selects the minimum-power configuration that still meets the 60 FPS target
- Tested on real mobile hardware (Odroid XU4 / Exynos 5422)

**Results vs. standard Linux governors:**
- ~26–33% reduction in power consumption
- ~21–26% lower peak CPU temperature
- ~30ms runtime decision overhead — very lightweight

---

## Key Takeaways

1. **Ambient temperature matters** — thermal models that ignore environmental conditions underperform in real deployments
2. **Security and power are not independent** — security tasks have thermal costs that must be modeled
3. **Modern systems are heterogeneous** — DVFS applied only to the CPU misses significant power savings from GPU and memory
4. **Offline vs. runtime tradeoff** — pre-computing Pareto-optimal configurations offline enables fast, lightweight runtime decisions
5. **Future direction** — ML/AI-based real-time DVFS prediction is emerging and may outperform offline profiling given sufficient training

---

## Repository Contents

```
├── README.md
├── Power_and_Heat_Management_Essay.pdf       # Full written survey (~20 pages)
└── Power_and_Heat_Management_Slides.pdf      # Presentation slides
```

---

## References

1. K. Jiang, Y. Zhang, Q. Zhu, and M. A. Al Faruque, "Power-Aware Design Techniques of Secure Multimode Embedded Systems," *ACM TODAES*, vol. 21, no. 3, Feb. 2016.
2. Y. Lee, H. S. Chwa, K. G. Shin, and S. Wang, "Thermal-Aware Resource Management for Embedded Real-Time Systems," *IEEE TCAD*, vol. 37, no. 11, pp. 2857–2868, Nov. 2018.
3. M. Pedram, "Power and Heat Management in Embedded Systems," *ISLPED*, Aug. 2001.
4. S. Dey et al., "CPU-GPU-Memory DVFS for Power-Efficient MPSoC in Mobile Cyber Physical Systems," *Future Internet*, vol. 14, no. 3, Mar. 2022.
