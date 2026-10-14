---
citekey: vanfraassen2022optimisation
title: "Optimisation of geometric aspect ratio of thin film transistors for low-cost flexible CMOS inverters and its practical implementation"
authors:
  - N. C. A. van Fraassen
  - K. M. Niang
  - J. D. Parish
  - A. L. Johnson
  - A. J. Flewitt
year: 2022
venue: "Scientific Reports"
type: paper
status: ingested
tags:
  - flexible-electronics
  - thin-film-transistors
  - cmos-inverters
  - metal-oxide-semiconductors
  - low-power-circuits
  - device-modeling
topics:
  - Thin-Film Transistor CMOS
  - Static Power in Flexible Logic
  - Metal-Oxide Inverters
areas:
  - Flexible Electronics
scenarios:
  - Low-Power Flexible CMOS Design
  - Low-Cost Flexible Processor Logic
methods:
  - Geometric Aspect-Ratio Optimisation
  - TFT Inverter Modelling
  - Experimental Device Characterisation
doi: "10.1038/s41598-022-19989-6"
arxiv: null
official_url: "https://www.nature.com/articles/s41598-022-19989-6"
pdf_url: "https://www.nature.com/articles/s41598-022-19989-6.pdf"
code_url: null
local_pdf: null
source_acquired: true
source_verified_against_original: true
verification_status: verified
analysis_confidence: high
verification_notes:
  - "Title, author order, publication date, journal, volume, article number, DOI, and official URLs were verified against the official Nature article page and publisher PDF."
  - "The inspected source PDF is byte-for-byte identical to the official Nature PDF (matching SHA-256 digest); the complete 12-page main article was read and visually checked, including Figures 1-5, Table 1, Methods, and Conclusions."
  - "The separately hosted Supplementary Information was not inspected; claims in this note are limited to evidence available in the main article."
created: 2026-08-17
updated: 2026-08-17
---

# Optimisation of geometric aspect ratio of thin film transistors for low-cost flexible CMOS inverters and its practical implementation

## Summary

This paper asks how to size complementary thin-film transistors when the weaker transistor has not only lower on-current but also much higher off-current. The usual silicon-inspired rule enlarges the p-type device in inverse proportion to its mobility. For metal-oxide TFTs, however, enlarging the p-type width-to-length ratio can improve charging current while proportionally increasing the leakage that dominates static power.

The paper replaces mobility matching with a current-aware design flow:

1. Measure or model each transistor's maximum and minimum output currents at the intended supply voltage.
2. Convert the maximum currents into an average switching-current proxy, `I_p`, and compare it with the sum of the two static currents, `I_stat`.
3. Choose the p-type geometry that balances maximum-current mismatch against minimum-current mismatch, then check noise margin and speed constraints.
4. Validate the choice through compact modeling and interconnected n-type amorphous indium-silicon-oxide (a-ISO) and p-type tin-monoxide (SnO) TFTs.

In the measured a-ISO/SnO case, reducing p-type `W/L` from 100 to 20 cuts static current by a factor of five while reducing the switching-current proxy by a factor of three, improving `I_p/I_stat` by about 60%. The result matters because geometry can recover part of the low-static-power benefit of CMOS without first solving every intrinsic p-type material limitation. It is a device-and-circuit sizing result, not a demonstration of a monolithically integrated flexible processor.

## Problem

Low-cost flexible processors need much lower static power than unipolar resistive-load logic can provide. Complementary logic is a natural solution, but available n- and p-type TFT technologies have strongly asymmetric mobility, threshold voltage, subthreshold swing, on-current, and off-current. In particular, p-type oxide TFTs such as SnO can have off-current that grows with drain voltage and device `W/L`.

The conventional response is to enlarge the slower p-type transistor until its on-current approaches that of the n-type transistor. That rule ignores the simultaneous increase in p-type leakage: it can improve transition speed or switching voltage while making static power worse. Voltage gain and noise margin alone do not reveal this tradeoff, so the paper needs both a sizing criterion and a performance metric that connect switching capability to static current.

## Research Area And Scenario

- Area(s): Flexible Electronics, with emphasis on thin-film electronic devices and complementary circuit design.
- Scenario(s): Low-power metal-oxide CMOS inverters for low-cost flexible processors and other flexible systems whose gate count is limited by static power.
- Why it matters here: The work connects device-level transfer/output characteristics to circuit-level geometry, static power, switching capability, noise margin, area, and prospective integration density.
- Indexing rationale: It belongs in Flexible Electronics because the target is low-temperature, large-area-compatible TFT logic; in Low-Power Flexible CMOS Design because it supplies a sizing workflow; and in Thin-Film Transistor CMOS because the central issue is asymmetric n-/p-type device behavior.

## Assumptions And Scope

- Stated assumptions:
  - Claim: The oxide sizing expression applies when both the maximum and minimum p-type currents scale approximately with p-type `W/L`.
    - Key constraint: **linear current scaling with geometry** over the design range.
    - Why it matters: The closed-form optimum follows from differentiating `I_p/I_stat` after treating both p-type current terms as proportional to `W/L`.
    - Excludes or weakens: Strong contact resistance, short-channel effects, geometry-dependent mobility, or leakage mechanisms that make either current non-linear in `W/L`.
  - Claim: The intended circuit is limited by static power strongly enough that `I_p/I_stat` is a useful primary objective.
    - Key constraint: **static-power-dominated flexible logic**.
    - Why it matters: The metric rewards switching-current capacity per unit static current; it does not minimize total switching energy.
    - Excludes or weakens: High-frequency silicon CMOS and other regimes where dynamic power dominates.
  - Claim: `I_p` is a useful proxy for switching frequency at fixed supply voltage and load capacitance.
    - Key constraint: **fixed `V_DD` and load capacitance** with propagation delay dominated by charge/discharge current.
    - Why it matters: The derivation replaces the full transition waveform with the harmonic combination of maximum pull-up and pull-down currents.
    - Excludes or weakens: Circuits whose delay is dominated by interconnect, input slew, parasitic variation, or detailed current-voltage waveform shape.
  - Claim: The optimum is evaluated at a specified supply voltage.
    - Key constraint: **voltage-dependent output characteristics**.
    - Why it matters: Maximum and minimum currents change with `V_DS`, so the optimal geometry changes with `V_DD`; the paper recommends designing at the maximum required supply voltage.
    - Excludes or weakens: A single geometry claimed to be universally optimal across a broad voltage range without a multi-objective check.
- Implied assumptions:
  - Claim: The characterized devices represent the process well enough for geometry selection.
    - Key constraint: **representative device characteristics**, especially SnO leakage and hysteresis.
    - Why it matters: The model and optimum use measured currents directly; process variation or aging moves the calculated ratio.
    - Excludes or weakens: Poorly controlled processes where device-to-device spread is comparable to the intended geometry benefit.
  - Claim: Separate n- and p-type TFTs can be integrated into a practical common process after being characterized independently.
    - Key constraint: **process compatibility is projected rather than monolithically demonstrated**.
    - Why it matters: The reported inverters interconnect a-ISO and SnO devices, while the SnO device uses a p+-Si/SiO2 substrate for fabrication convenience.
    - Excludes or weakens: Claims that the paper already demonstrates a complete flexible-substrate CMOS process or processor.
- What is ignored or abstracted away:
  - Claim: The optimization treats transition-current shape as a second-order effect and does not directly model a full processor's dynamic activity.
    - Key constraint: **current-based static-efficiency abstraction**.
    - Why it matters: Parasitic capacitance, waveform-dependent delay, fan-out, routing, clocking, and activity factor can change the system-level optimum.
    - Excludes or weakens: Direct use of `I_p/I_stat` as a measured energy-delay product or whole-chip power result.
- Applies when:
  - Claim: Complementary TFT technologies have a substantial off-current mismatch that geometry can trade against on-current mismatch.
    - Key constraint: **large leakage asymmetry** between n- and p-type devices.
    - Why it matters: If leakage is negligible, conventional mobility or noise-margin matching is usually more relevant.
- May not apply when:
  - Claim: Noise margin, absolute speed, or dynamic energy is the binding design target rather than static current.
    - Key constraint: **a different primary objective**.
    - Why it matters: The geometry that maximizes `I_p/I_stat` is a lower-bound design point; the paper itself sometimes recommends a larger device to improve noise margin or switching current.

## Core Idea

Size the complementary TFT pair by balancing two mismatch ratios rather than matching mobility alone. For an oxide inverter with fixed n-type geometry and p-type maximum/minimum currents normalized to `(W/L)_p = 1`, the paper derives:

`(W/L)_p,opt = sqrt[(I_max,n / I_max,p) (I_min,n / I_min,p)]`.

This setting equalizes the relative mismatch in maximum currents with the opposite mismatch in minimum currents and maximizes the paper's inverter-efficiency metric, `I_p/I_stat`, under its linear-scaling assumptions. The formula reduces to familiar on-current or mobility matching when off-current is geometry-independent, and it has a mirrored form for organic complementary circuits where the n-type device is the weaker, leakier side.

## Method

The workflow begins with measured TFT transfer and output characteristics rather than a nominal mobility ratio. A compact transistor model reconstructs n- and p-type output-current families; their intersection points produce inverter voltage/current transfer curves, static currents, current-based switching capacity, and noise margins. An analytical objective then maps those quantities to an optimal geometry, while a second-stage check exposes tradeoffs with noise margin, absolute speed, area, and supply voltage.

The experimental path mirrors the model. The authors fabricate a bottom-gate staggered a-ISO n-type TFT and SnO p-type TFTs, interconnect alternative p-type geometries to the same n-type device, and compare measured voltage/current transfer curves with the model. This closes the loop from device characterization to sizing rule to inverter behavior.

- Component: TFT electrical characterization
  - Role: Establish the geometry- and voltage-dependent currents used by every later calculation.
  - Input: Gate/drain-voltage sweeps for a-ISO and SnO TFTs with known `W/L` values.
  - Output: Transfer/output curves and extracted `I_max`, `I_min`, `I_off`, threshold voltage, subthreshold swing, and mobility behavior.
  - Transformation: A Keithley 4200 setup sweeps the devices; comparing SnO geometries reveals how both useful current and leakage scale with width.
  - Minimal example: At SnO `W/L` values 20, 50, and 100, the reported off-current rises from about 280 pA to 750 pA and 1400 pA in the stated transfer-characteristic measurement.
- Component: Compact transistor and inverter model
  - Role: Predict voltage transfer characteristics (VTCs), current transfer characteristics (CTCs), and noise margins for geometries not all fabricated as complete inverter configurations.
  - Input: Measured n-/p-type output curves, oxide capacitances, threshold parameters, empirical mobility prefactors, device geometry, and `V_DD`.
  - Output: Modeled transistor current families, inverter operating points, VTCs, CTCs, maximum/minimum currents, and MEC noise margins.
  - Transformation: Modified MOSFET equations fit each TFT's linear and saturation regions; intersections between pull-up and pull-down output curves determine the inverter's static operating point at each input voltage.
  - Minimal example: Replacing a 6 MΩ pull-up with modeled SnO devices at `(W/L)_p = 100` or 20 produces two different speed/leakage/noise-margin tradeoffs against the same a-ISO pull-down.
- Component: Inverter-efficiency metric
  - Role: Compare switching capability with static-current cost across different inverter configurations.
  - Input: Maximum high-to-low and low-to-high currents plus the two logic-state static currents.
  - Output: `I_p = I_max,HL I_max,LH / (I_max,HL + I_max,LH)`, `I_stat = I_stat,0 + I_stat,1`, and the dimensionless ratio `I_p/I_stat`.
  - Transformation: The two transition currents are combined as the reciprocal-delay proxy for charging and discharging the same capacitive load, then normalized by total static current.
  - Minimal example: The modeled 6 MΩ resistive-load inverter has `I_p/I_stat = 0.86`, whereas the a-ISO/SnO inverter with equal `W/L = 20/20` reaches about 26 at `V_DD = 5 V`.
- Component: Geometric optimum and multi-objective check
  - Role: Turn measured current mismatch into a candidate device ratio, then prevent a single metric from hiding unusable noise margin or speed.
  - Input: Geometry-normalized maximum/minimum currents, supply voltage, modeled noise margin, and desired switching current.
  - Output: An efficiency-maximizing `W/L`, plus a design range adjusted for noise margin, speed, or area.
  - Transformation: Differentiating `I_p/I_stat` gives the square-root optimum; the paper then evaluates `I_p/I_stat`, normalized noise margin, and their product over geometry and `V_DD`.
  - Minimal example: For the reported a-ISO device with `(W/L)_n = 20`, the calculated optimum `(W/L)_p` is 4 at 5 V and 5 at 10 V, but a larger value can be chosen when added noise margin is worth a small efficiency loss.
- Component: Interconnected-inverter validation
  - Role: Test whether the fitted model and geometry trend appear in measured CMOS behavior.
  - Input: One a-ISO n-type TFT and SnO p-type TFTs with `(W/L)_p = 100` and 20, connected through a six-probe setup.
  - Output: Measured VTCs and CTCs across supply voltages, with forward/backward sweeps exposing SnO hysteresis.
  - Transformation: The p-type device is swapped while the n-type device and measurement topology remain fixed, isolating the effect of the geometric ratio.
  - Minimal example: Moving from `(W/L)_p = 100` to 20 reduces measured static current fivefold and `I_p` threefold, so `I_p/I_stat` improves by about 60%.

## 优化问题形式化 / Optimization Problem Formulation

> **User-directed revision:** Added a bilingual formulation of the optimization inputs, decision variable, objective, assumptions, practical constraints, and closed-form solution.

本节把论文第 3-9 式和第 10-15 式整理成一个显式的单变量优化问题。所有电流均在同一个给定工作电压 $V_{DD}$ 下取值；改变 $V_{DD}$ 会改变输入电流，从而改变最优解。

This section rewrites Eqs. 3-9 and 10-15 as an explicit single-variable optimization problem. All currents are evaluated at the same chosen supply voltage $V_{DD}$; changing $V_{DD}$ changes those input currents and therefore changes the optimum.

### 1. 决策变量 / Decision Variable

$$
x = \left(\frac{W}{L}\right)_p, \qquad x > 0.
$$

- 中文：$x$ 是待设计的 p 型 TFT 宽长比。n 型 TFT 的 $\left(W/L\right)_n$ 在优化期间保持固定；论文的 a-ISO 器件取 $\left(W/L\right)_n=20$。
- English: $x$ is the p-type TFT width-to-length ratio to be designed. The n-type ratio $\left(W/L\right)_n$ remains fixed during optimization; the paper uses $\left(W/L\right)_n=20$ for its a-ISO device.
- 中文：论文常把“几何纵横比”写成 $r=\left(W/L\right)_p/\left(W/L\right)_n$。因为分母固定，优化 $x$ 与优化 $r$ 等价，且 $r^*=x^*/\left(W/L\right)_n$。
- English: The paper often reports the geometric aspect ratio as $r=\left(W/L\right)_p/\left(W/L\right)_n$. Because the denominator is fixed, optimizing $x$ is equivalent to optimizing $r$, with $r^*=x^*/\left(W/L\right)_n$.

### 2. 已知输入与核心变量 / Known Inputs And Core Variables

| 符号 / Symbol | 中文解释 | English explanation |
|---|---|---|
| $V_{DD}$ | 选定的反相器电源电压；下列四个电流都必须在该电压对应的输出特性上取得。 | Chosen inverter supply voltage; all four currents below must come from output characteristics at this voltage. |
| $A=I_{\max,n}$ | 固定尺寸 n 型 TFT 的最大导通电流，决定最大放电能力。 | Maximum on-current of the fixed-size n-type TFT; it sets the strongest pull-down capability. |
| $C=I_{\min,n}$ | 固定尺寸 n 型 TFT 在静态关断状态下的最小电流。 | Minimum current of the fixed-size n-type TFT in its static off-state. |
| $B=I_{\max,p}$ | p 型 TFT 在单位宽长比 $\left(W/L\right)_p=1$ 下归一化的最大导通电流。 | Maximum p-type on-current normalized to unit geometry $\left(W/L\right)_p=1$. |
| $D=I_{\min,p}$ | p 型 TFT 在单位宽长比 $\left(W/L\right)_p=1$ 下归一化的最小关断电流。 | Minimum p-type off-current normalized to unit geometry $\left(W/L\right)_p=1$. |
| $I_p(x)$ | 有效切换电流代理量；在固定负载电容和电源电压下与切换频率同向变化。 | Effective switching-current proxy; at fixed load capacitance and supply voltage it scales with switching frequency. |
| $I_{\mathrm{stat}}(x)$ | 两个逻辑静态状态的电流之和。 | Sum of the currents in the two static logic states. |
| $\eta(x)$ | 论文定义的无量纲反相器效率 $I_p/I_{\mathrm{stat}}$。 | Paper's dimensionless inverter efficiency $I_p/I_{\mathrm{stat}}$. |

论文假设 p 型最大、最小电流在相关尺寸范围内都随 $x$ 近似线性变化，因此实际 p 型电流为：

The paper assumes that both p-type maximum and minimum currents scale approximately linearly with $x$ over the relevant geometry range, giving:

$$
I_{\max,p}^{\mathrm{actual}}(x)=xB, \qquad
I_{\min,p}^{\mathrm{actual}}(x)=xD.
$$

### 3. 优化目标 / Optimization Objective

CMOS 的有效切换电流和静态电流写为：

For the CMOS inverter, the effective switching current and static current are:

$$
I_p(x)=\frac{A(xB)}{A+xB}, \qquad
I_{\mathrm{stat}}(x)=C+xD.
$$

因此论文的单变量目标函数为：

The paper's single-variable objective is therefore:

$$
\underset{x>0}{\operatorname{maximize}}\;\eta(x)
=\frac{I_p(x)}{I_{\mathrm{stat}}(x)}
=\frac{ABx}{(A+Bx)(C+Dx)}.
$$

- 中文：增大 $x$ 会提高 p 型充电电流 $xB$，但也会同比放大静态泄漏 $xD$；目标函数寻找两者之间的最佳折中。
- English: Increasing $x$ raises the p-type charging current $xB$, but it also proportionally increases static leakage $xD$; the objective finds the best tradeoff between them.
- 中文：$I_p$ 是由静态输出特性推导的切换能力代理量，不是直接测得的传播延迟或能量延迟积。
- English: $I_p$ is a switching-capability proxy derived from static output characteristics, not a directly measured propagation delay or energy-delay product.

### 4. 约束与假设 / Constraints And Assumptions

**论文闭式推导中的数学条件 / Mathematical conditions in the paper's closed-form derivation**

- 中文：$x>0$，且 $A,B,C,D>0$；$V_{DD}$ 与 n 型几何尺寸在一次优化中固定。
- English: $x>0$ and $A,B,C,D>0$; $V_{DD}$ and the n-type geometry are fixed within one optimization.
- 中文：p 型的 $I_{\max,p}$ 与 $I_{\min,p}$ 均随 $x$ 线性缩放；若接触电阻、短沟道效应或尺寸相关迁移率破坏这一关系，闭式解不再严格成立。
- English: Both p-type $I_{\max,p}$ and $I_{\min,p}$ scale linearly with $x$; if contact resistance, short-channel effects, or geometry-dependent mobility break this relation, the closed-form result is no longer exact.
- 中文：$A,B,C,D$ 必须来自同一 $V_{DD}$。当 n 型最小电流低到缺乏实际意义时，论文建议用系统中最低关键电流替代 $C$，避免得到不可实现的极小尺寸。
- English: $A,B,C,D$ must correspond to the same $V_{DD}$. When the n-type minimum current is unrealistically small, the paper recommends replacing $C$ with the system's lowest critical current to avoid an impractically small geometry.

**实际设计约束与次级目标 / Practical design constraints and secondary objectives**

- 中文：噪声裕量、绝对切换速度、总面积、寄生电容与可制造尺寸没有进入上述闭式目标。实际设计可额外要求 $NM(x)\ge NM_{\min}$、$I_p(x)\ge I_{p,\min}$ 和 $x_{\min}\le x\le x_{\max}$。
- English: Noise margin, absolute switching speed, total area, parasitic capacitance, and manufacturable geometry do not enter the closed-form objective above. A practical design may additionally require $NM(x)\ge NM_{\min}$, $I_p(x)\ge I_{p,\min}$, and $x_{\min}\le x\le x_{\max}$.
- 中文：这些并非论文第 15 式中的硬约束，而是论文在图 5 中通过噪声裕量、组合指标和尺寸比较进行的后续设计检查。
- English: These are not hard constraints in the paper's Eq. 15; they are downstream design checks explored through noise margin, a combined metric, and geometry comparisons in Figure 5.

### 5. 闭式解与物理解释 / Closed-Form Solution And Physical Interpretation

目标函数的导数可写为：

The derivative of the objective can be written as:

$$
\frac{d\eta}{dx}
=\frac{AB\left(AC-BDx^2\right)}{(A+Bx)^2(C+Dx)^2}.
$$

因为分母始终为正，唯一内部极值由 $AC-BDx^2=0$ 给出：

Because the denominator is always positive, the unique interior stationary point is given by $AC-BDx^2=0$:

$$
x^*=\sqrt{\frac{AC}{BD}}
=\sqrt{\frac{A}{B}\cdot\frac{C}{D}}
=\sqrt{\frac{I_{\max,n}}{I_{\max,p}}
\cdot\frac{I_{\min,n}}{I_{\min,p}}}.
$$

当 $x\rightarrow0$ 或 $x\rightarrow\infty$ 时 $\eta(x)\rightarrow0$，所以该内部极值是全局最大值。它也满足：

As $x\rightarrow0$ or $x\rightarrow\infty$, $\eta(x)\rightarrow0$, so this interior stationary point is the global maximum. It also satisfies:

$$
\frac{A}{x^*B}=\frac{x^*D}{C}.
$$

- 中文：最优点不是让最大电流完全相等，而是让“最大电流失配倍数”和方向相反的“最小电流失配倍数”相等。
- English: The optimum does not force the maximum currents to be equal; it equalizes the maximum-current mismatch factor with the opposing minimum-current mismatch factor.
- 中文：对论文的 $\left(W/L\right)_n=20$ 器件，计算得到 $x^*=4$（$V_{DD}=5\,\mathrm{V}$）和 $x^*=5$（$V_{DD}=10\,\mathrm{V}$），即 $r^*=0.20$ 和 $0.25$。若噪声裕量或速度约束更严格，最终设计可在此效率最优点之上增大 $x$。
- English: For the paper's device with $\left(W/L\right)_n=20$, the calculation gives $x^*=4$ at $V_{DD}=5\,\mathrm{V}$ and $x^*=5$ at $V_{DD}=10\,\mathrm{V}$, corresponding to $r^*=0.20$ and $0.25$. If noise-margin or speed constraints are tighter, the final design may increase $x$ above this efficiency-optimal point.

## Experiments And Evidence

- **a-ISO vs. SnO device scaling -> the p-type leakage term cannot be ignored.**
  - Selected evidence: The a-ISO TFT at `W/L = 20` has roughly 400 pA off-current that is reported as independent of `V_DS`; for SnO, off-current rises with both `V_DS` and `W/L`, and the SnO device also shows much lower mobility and a larger subthreshold swing.
  - Supports: A mobility-only sizing rule can enlarge both useful p-type current and the dominant static-current term.
  - Original: Figure 2 and the section "Characteristics of n-type a-ISO and p-type SnO TFTs."
- **Resistive load vs. enlarged SnO vs. equal-geometry SnO -> reducing p-type width trades some speed for a larger leakage reduction.**
  - Selected evidence: At `V_DD = 5 V`, the model reports `I_p/I_stat = 0.86` for the 6 MΩ resistive load, 15 for `(W/L)_p/(W/L)_n = 100/20`, and about 26 for `20/20`. The `20/20` case lowers `I_stat,1` from 200 nA to 40 nA relative to `100/20`, while `I_p` falls from 3.0 µA to 1.1 µA.
  - Supports: Matching minimum currents more closely can improve static efficiency and noise margins even when maximum currents become less closely matched.
  - Original: Figure 3 and Eqs. 3-9.
- **Model vs. interconnected a-ISO/SnO inverters -> the geometry trend survives measured device behavior.**
  - Selected evidence: The measured and modeled VTC/CTC curves track across the evaluated supply voltages. Changing `(W/L)_p` from 100 to 20 reduces static current by a factor of five and switching-current proxy by a factor of three, yielding an approximately 60% efficiency increase.
  - Supports: The analytical/modeling result is not only an algebraic consequence; it is consistent with the two measured inverter configurations.
  - Original: Figure 4a-d and "All-oxide CMOS inverter performance."
- **Efficiency optimum vs. noise-margin objective -> the square-root result is a design bound, not the only acceptable geometry.**
  - Selected evidence: For the paper's devices, modeled `(W/L)_p = 5` maximizes or nearly maximizes `I_p/I_stat`, but `(W/L)_p = 20` can provide a better combined efficiency/noise-margin score above about 5 V. A `60/60` pair is also modeled to match the switching-current proxy of `100/20` with half the static current and the same total `W/L` area at 5 V.
  - Supports: Designers should calculate the static-efficiency optimum first, then move upward in size only for explicit noise-margin or speed requirements.
  - Original: Figure 5 and "Optimal geometric aspect ratio."
- **Current work vs. published oxide and organic inverters -> the rule can diagnose both large and small remaining geometry headroom.**
  - Selected evidence: Table 1 applies the same current-ratio calculation to several literature devices; the paper estimates an almost threefold efficiency increase for one all-oxide inverter, while another is already close to its efficiency optimum.
  - Supports: The method is portable across complementary material pairs when the required maximum/minimum currents are available, but its payoff is device-specific.
  - Original: Table 1.
- **Low-temperature process evidence -> the devices are compatible in temperature budget, but flexible monolithic integration remains prospective.**
  - Selected evidence: Both device flows use a maximum reported temperature of 200 °C; the a-ISO TFT is fabricated on glass, while the SnO TFT is fabricated on thermally oxidized p+-Si and the two are interconnected for inverter measurements.
  - Supports: The study demonstrates a practical measurement implementation and a flexible-compatible thermal budget, not a completed common-substrate flexible CMOS process.
  - Original: Methods, "Device fabrication" and "Device characterisation."

Evidence boundary: The paper reports fitted curves and representative device/inverter measurements but no sample-count statistics or uncertainty intervals. `I_p` is derived from static output-current extrema rather than from direct transient-delay or ring-oscillator measurements. The projected >200-fold static-power reduction and million-gate implication in the conclusion are system-level estimates, not fabricated-processor results.

## Contributions

- Defines a dimensionless inverter-efficiency metric that exposes the tradeoff between switching-current capacity and static current in TFT logic.
- Derives a closed-form current-aware geometric optimum for oxide and organic complementary inverters, with the conventional silicon rule as a limiting case.
- Builds a fitted transistor/inverter model that jointly evaluates efficiency, voltage transfer behavior, and noise margins over geometry and supply voltage.
- Verifies the principal geometry trend using interconnected a-ISO/SnO CMOS inverter configurations.
- Shows how the sizing rule can reinterpret published all-oxide and organic inverters without changing their intrinsic materials.

## Limitations

- The physical demonstration is not a monolithically integrated flexible CMOS circuit: the n-type device is on glass, the p-type device is on p+-Si/SiO2, and a probe setup interconnects them.
- The efficiency metric uses static output-current extrema as a switching-frequency proxy; transient delay, oscillator frequency, energy per transition, and complete dynamic power are not directly measured.
- The closed-form optimum assumes maximum and minimum current scale linearly with geometry and therefore may shift under contact, short-channel, or geometry-dependent material effects.
- SnO hysteresis changes the measured currents; the paper applies a correction for model comparison and identifies stability and repeatability as unresolved requirements.
- Experimental validation covers one a-ISO/SnO technology pair and two completed inverter geometries, without reported device-population statistics or uncertainty estimates.
- The optimum depends on `V_DD`, process variation, the chosen minimum critical current, and whether static efficiency, noise margin, speed, or area is the primary objective.
- The processor-scale power and gate-count improvements are extrapolations from current levels and a previously published resistive-load processor, not end-to-end hardware validation.

## Key Takeaways

- Do not size asymmetric TFT CMOS by mobility alone when the weaker device's off-current scales with width.
- Measure both `I_max` and `I_min`; the useful geometry is set by their two-sided mismatch, not only by on-current.
- `I_p/I_stat` is most informative for static-power-dominated flexible logic and should not be confused with total energy efficiency.
- The analytical optimum is a starting point. Noise margin and absolute switching current can justify a larger device, but that increase should be deliberate.
- Geometry can materially improve current oxide CMOS using existing materials, yet device stability, process integration, and statistical reproducibility remain necessary for a flexible processor.

## Relation: Previous Work

- Title: The development of flexible integrated circuits based on thin-film transistors.
  Authors: Kris Myny.
  URL: https://doi.org/10.1038/s41928-017-0008-6
  - Type: survey
  - Status: not-ingested
  - Work summary: Reviews the technologies, process constraints, circuit styles, and scaling path for flexible TFT integrated circuits.
  - Role: Establishes why low-temperature TFT logic is attractive and why material performance and integration density remain limiting.
  - Limitation: A field-level review does not provide a device-pair-specific rule for balancing on-current and static leakage through geometry.
  - This paper: Adds an analytical and experimentally checked sizing criterion for complementary TFT inverters.
- Title: The Origin of the high off-state current in p-type Cu2O thin Film transistors.
  Authors: S. Han; A. J. Flewitt.
  URL: https://doi.org/10.1109/LED.2017.2748064
  - Type: uses
  - Status: not-ingested
  - Work summary: Investigates the physical origin of high off-state current in p-type Cu2O TFTs and its dependence on operating conditions.
  - Role: Supplies prior evidence that p-type oxide leakage can rise with drain voltage and geometry rather than remaining an ideal geometry-independent off-current.
  - Limitation: It diagnoses a device leakage mechanism but does not translate the effect into a complementary-inverter sizing objective.
  - This paper: Uses the leakage-scaling premise to derive a current-aware geometric optimum and test its circuit consequences in SnO/a-ISO inverters.

## Relation: Compared With

- Title: Complementary Oxide-Semiconductor-Based Circuits With n-Channel ZnO and p-Channel SnO Thin-Film Transistors.
  Authors: I-Chung Chiu; Yun-Shiuan Li; Min-Sheng Tu; I-Chun Cheng.
  URL: https://doi.org/10.1109/LED.2014.2364578
  - Type: compares-with
  - Status: not-ingested
  - Work summary: Demonstrates all-oxide complementary circuits using n-channel ZnO and p-channel SnO TFTs, including inverter and ring-oscillator operation.
  - Similarity: Both works target complementary oxide TFT logic and confront the current mismatch between n- and p-type devices.
  - Difference: Chiu et al. emphasize fabricated circuit operation; this paper uses the published current values as a case study for static-efficiency-aware geometry optimization.
  - When to use which: Use Chiu et al. for an earlier all-oxide circuit implementation and this paper for a general sizing calculation and its leakage/speed tradeoff.
- Title: Ultralow-power organic complementary circuits.
  Authors: Hagen Klauk; Ute Zschieschang; Jens Pflaum; Marcus Halik.
  URL: https://doi.org/10.1038/nature05533
  - Type: compares-with
  - Status: not-ingested
  - Work summary: Demonstrates low-voltage, ultralow-power complementary organic transistor circuits.
  - Similarity: Both works seek low-static-power complementary thin-film logic built from materially asymmetric transistor types.
  - Difference: The organic example has the stronger p-type device and therefore requires optimizing the n-type geometry; this paper's measured oxide example has the weaker, leakier p-type device.
  - When to use which: Use Klauk et al. for the organic device/circuit platform and this paper for the mirrored current-ratio rule that can resize either side of a complementary pair.
- Title: Subthreshold Schottky-barrier thin-film transistors with ultralow power and high intrinsic gain.
  Authors: Seungyeop Lee; Arokia Nathan.
  URL: https://doi.org/10.1126/science.aah5035
  - Type: compares-with
  - Status: not-ingested
  - Work summary: Uses subthreshold Schottky-barrier TFT operation to obtain very low current and high intrinsic gain in a zero-`V_GS` inverter configuration.
  - Similarity: Both approaches address low-power TFT inverter design and use non-silicon device characteristics deliberately.
  - Difference: The zero-`V_GS` approach achieves ultralow absolute power with `I_p/I_stat` near one and correspondingly low speed, whereas complementary sizing seeks lower static current without sacrificing the switching-current ratio to the same extent.
  - When to use which: Use the zero-`V_GS` design for ultralow-power, low-frequency operation; use the current paper's CMOS sizing method when flexible processor logic needs a much larger switching-to-static-current ratio.

## Source Notes

- Metadata sources: official Nature/Scientific Reports article page and official publisher PDF.
- Bibliographic verification: the official page reports publication on 27 September 2022 in Scientific Reports volume 12, article 16111, with the same five authors and DOI as the inspected source artifact.
- Source verification: inspected the complete 12-page main PDF provided by the user; it is byte-for-byte identical to the official Nature PDF by SHA-256 digest and direct comparison.
- Reading coverage: reviewed the full main text, equations, Figures 1-5, Table 1, Methods, data-availability statement, and Conclusions; the separately hosted Supplementary Information was not used.
- Data availability: the main article points to the Cambridge University Data Repository but does not identify a specific dataset record in its data-availability statement, so no dataset index was created.
- PDF policy: the source PDF was not copied into the repository, and no machine-specific path is recorded in this note.
