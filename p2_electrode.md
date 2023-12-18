---
title: Optimization of Ion Confinement in Surface-Electrode Traps for High-Coherence Quantum Information Processing & Laser Fiber Noise Cancelation
author: |
	| Andrea Miramontes Serrano, Nelson Ooi^[Co-corresponding authors. School of Electrical and Computer Engineering, College of Engineering, Cornell University, Ithaca, New York, USA. Respective emails: am2389@cornell.edu, no83@cornell.edu]
	|
	| Advised by Professor Karan Mehta
date: \today
abstract: Trapped-ion quantum information processing devices rely on optimized ion confinement to enable high-coherence operations. Surface-electrode ion traps are commonly used to confine ion qubits in space, using a combination of radio frequency (RF) and static (DC) electric potentials. A key aspect of optimal ion control is in determining appropriate electrode potentials for stable ion confinement. Here, we present techniques based on Allcock et. al to characterize surface-electrode Paul traps and generate corresponding optimal basis potential sets, paving the way for in- situ experiments. We also compare the efficacy of different numerical methods in performing these characterization tasks. Separately, we describe the implementation of a laser fiber noise cancelation system to create narrow-linewidth lasers for high-coherence addressing.
geometry: margin=1in
fontsize: 12pt
csl: american-physics-society.csl
bibliography: electrode.bib
link-citations: true
colorlinks: true
linkcolor: blue
numbersections: true
---

# Introduction

Trapped-ion architectures are a leading approach for implementing quantum information processors. [@harty_high-fidelity_2014; @wang_single_2021; @brown_single-qubit-gate_2011] Such architectures allow information to be encoded in ion energy levels, and require stable ion confinement to permit processing tasks. A common method of ion trapping is using surface-electrode Paul traps: these ion traps utilize oscillating (radio frequency) and static electric fields to create confining potentials. To minimize ion qubit loss and decoherence – which corresponds to irrecoverable information loss – it is therefore crucial to generate stable and deep electric potentials using trap electrodes.

Separately, we also describe the design and implementation of a laser fiber noise cancelation system. This system is used in our experimental setup to eliminate phase noise accrued in a 729nm laser beam sent through fiber optics cables for ion addressing. The elimination of phase noise is crucial for ensuring that a narrow-linewidth beam in delivered to the ion qubit for high-coherence addressing.

# Ion Confinement in a Surface-Electrode Paul Trap

## Background on Electric Potentials for Ion Confinement

Paul traps provide two sets of confining potentials: a DC electric potential and a potential oscillating at $\omega_{rf}$, typically near radio frequency. In a Taylor expansion of the confining potential near the center (null) of an ideal Paul trap, the leading terms will be quadratic in the distance from the center $r_i$,[@leupold] and the potentials at time $t$ is given by 
$$\Phi_{DC}(r) = \frac{1}{2}\left(u_x x^2 + u_y y^2 + u_z z^2\right), \ \Phi_{RF}(r, t) = \frac{1}{2}\left(v_x x^2 + v_y y^2 + v_z z^2\right)\cos(\omega_{rf} t)$$
The overall potential due to the superposed DC potential and oscillating potential is then given by
$$\Phi (\mathbf{r}, t) = \frac{1}{2} \sum_i u_i r_i^2 + \frac{1}{2} \sum_i v_i r_i^2 \cos (\omega_{rf} t)$$
It is necessary to apply both static potentials and pseudopotentials simultaneously, as it is not possible to ensure 3D confinement by exclusively using DC potentials, due to constraints imposed by the Laplace equation.
$$\nabla^2 \Phi = \frac{\partial^2 \Phi}{\partial x^2} + \frac{\partial^2 \Phi}{\partial y^2} + \frac{\partial^2 \Phi}{\partial z^2} = 0$$
It follows from the Laplace equation that if the quadratic expansion of the DC potential is confining in two of the three cardinal directions (have positive Taylor coefficients), then the third dimension must be anti-confining (have a negative Taylor coefficient). This imposes the following constraint on the expanded coefficient terns:
$$\sum_i u_i = \sum_i v_i = 0$$
Therefore, the pseudopotential term can be viewed as providing a saddle potential which rapidly oscillates, changing anti-confining and positively confining directions many times a second. The ion's inertia causes it to remain relatively close to the null-point of the overall potential.

## Derivation of Ion Equations of Motion

Using the Taylor expansion above, equations of motion can be defined for a confined ion. Starting with the classical equation of motion where $F_{net, i}$ is the net force experienced by the ion in the direction of $r_i$, m is the mass of the ion, and ${r}_i$ is the displacement of the ion from the potential null along a specific cardinal axis, this gives
$$
F_{net, i} = m \ddot{r_i}
$$
By Coulomb's Law, the $F_{net, i} = -e \frac{\partial \Phi}{\partial t}$. Rewriting, one gets
$$\frac{\partial^2 r_i}{\partial t^2} = -\frac{e}{m} \frac{\partial \Phi}{\partial t} = -\frac{e}{m} \left(u_i r_i + v_i r_i \cos (\omega_{rf} t)\right)$$
and substituting with the dimensionless parameters
$$\tau = \frac{\omega_0 t}{2} + \frac{\pi}{2}, \ a_i = \frac{4eu_i}{m\omega_{rf}^2}, \ q_i = \frac{2ev_i}{m\omega_{rf}^2}$$
We can rewrite the equation of motion as 
$$\frac{\partial^2 r_i}{\partial t^2} + \left(a_i - 2q_i \cos(2\tau)\right)r_i = 0$$
which is the standard form of the Mathieu equation.[@leupold] Analysis of this equation reveals that there are stable solutions, and these in turn define the design of the surface-electrode traps. We do not go into the specifics of the derivation for stable solutions here, but refer readers to [@mclachlan, @leupold] for the complete steps.

In the first stability regime, the position of the ion in time along each axis can be modeled using the equation
$$r_i (t) \propto \cos(\omega_i t) (1+ q_i/2 \cos ( \omega_{rf} t))$$
where $\omega_i = \frac{\beta_i \omega_{r f}}{2} = \sqrt{a_i + \frac{q_i^2}{2}} = \sqrt{ \frac{e u_i}{m} +\frac{e^2 v_i^2 }{2m^2 \omega_{r f}^2} }$. For a linear, symmetric Paul trap whose trap axis lies along the $x$-axis, $q_x = 0, q_y = -q_z$ and the oscillating potential confines only in the $y z$-plane. The above equations indicate that the ion undergoes harmonic motion at frequency $\omega_i$ in the first-order approximation due to the potential curvature – called _secular motion_. This is subject to a modulation oscillating at $\omega_{rf}$, called *micromotion*.

Secular motion is crucial as it is often used to induce coupling between the spin and motional modes to mediate transfer of quantum information. Micromotion, on the other hand, is often undesirable as it can introduce Doppler shifts that greatly alter the excitation spectrum of the ion. [@cite_nist] This significantly can affect confinement of the ion, in addition to reducing the effectiveness of laser cooling, which leads to limited coherence during laser addressing.

Micromotion can be characterized into two components: *intrinsic* and *excess micromotion*. *Intrinsic micromotion* arises from the ion's periodic displacement due to its secular motion, leading to the modulation at the $\omega_{rf}$ drive frequency: i.e. the second term in the equation of ion motion. It is generally not possible to correct for this, even in ideal infinite-length Paul traps. *Excess micromotion* can arise from stray E-fields that displace the ion from the RF potential null, resulting in additional driven motion at the trap frequency. [@cite_nature] Another possible source is if the RF electrodes oscillate with a relative phase difference. [@cite_nist] These can be compensated for by the application of correcting DC voltages.

Therefore, an applied DC potential can serve two key purposes: (1) to provide ion confinement along the trap axis by complementing the oscillating RF potential, and (2) to compensate for excess micromotion that affects qubit confinement and coherence. In the following sections, we describe methods to generate a set of DC potentials to be applied to electrodes in a surface-electrode Paul trap, in order to generate confining potentials that fulfill the two key uses above.

## Numerical Fitting Method for Optimizing Surface Electrode Trap Potential Sets

The numerical fitting method we use is based on a method described by Allcock et. al. The surface electrode trap we use as a basis for modeling is shown in Fig. \ref{trap_plan}. Specific to this trap, the frequency of the oscillating potential is set to 30 MHz, which corresponds to a 50 µm ion height above the trap surface. The location at the center of the trap in the $xy$-plane and 50µm above the trap surface is chosen as the origin of the coordinate system. The trap axis lies parallel the $x$-axis, and the $yz$-plane is as defined in Fig. \ref{trap_plan}.

Each of the 20 numbered electrodes are independently controllable and can have DC voltages applied to generate a 3D DC potential in the volume above the trap. Working backwards from the desired 3D confining potential around the ion, our goal is to identify the appropriate DC voltages that result in this chosen spatial potential distribution.

![Plan view of surface-electrode trap, with numbered DC electrodes and RF electrodes labeled. \label{trap_plan}](trap_plan.png){width=70%}

First, a unit voltage (1V) is applied to each of the 20 electrodes in turn, while all others are held at ground (0V). In each iteration for electrode $n$, the DC potential around the ion position is numerically computed. We then perform a least-squares fit using a quadratic function on this DC potential over a small region ($\pm 10 \mu m$) around the ion's position. This fit allows us to ignore higher order contributions to the spatial potential distribution, and utilize fast numerical solvers in subsequent steps.

The quadratic fit for each  returns coefficients $\alpha_{ni}, \beta_{ni}$ that describe the potential as a function of distance from the normalized origin:
$$ \Phi_n = \alpha_{nx} x^2 + \beta_{nx} x + \alpha_{ny} y^2 + \beta_{ny} y + \alpha_{nz} z^2 + \beta_{nz} z $$

If we want to create a target spatial potential distribution that will be achieved with specific combinations of voltages applied to DC electrodes, we can then specify its spatial profile using target values of $\alpha_t, \beta_t$, as such:
$$ \Phi_{target} = \alpha_{tx} x^2 + \beta_{tx} x + \alpha_{ty} y^2 + \beta_{ty} y + \alpha_{tz} z^2 + \beta_{tz} z $$
We can achieve $\Phi_{target}$ through a linear combination of the contributions due to each individual electrode, where $V_i$ is the voltage applied to the $i$th electrode:
$$\Phi_{target} = \sum_i^n V_i \Phi_i$$
This can be viewed as a linear programming problem subject to specific constraints. A caveat is that with 20 independently-controllable electrodes, there are 20 degrees of freedom in total, but only 6 coefficients derived through the quadratic fit. As such, solving for the target directly results in an over-defined (overconstrained) problem which often does not yield any solution.

Hence, it is essential to collect the 20 electrodes into 6 or fewer groups before utilizing a linear solver. A group is defined when all electrodes within it are tied to the same voltage and controlled together as a single degree of freedom. Hence, the contribution of each group overall can be defined as the sum over each of that group's electrodes:
$$\Phi_{\text{Group j}} = \sum_{i\in \text{Group j}} \Phi_i$$
And the target potential is then given by:
$$\Phi_{target} = \sum_j V_j\Phi_{\text{Group j}}$$
Where $V_j$ is the voltage applied to all electrodes in the $j$th group.

It is often useful to utilize symmetries in the target spatial potential distribution to determine the assignment of individual electrodes to groups, and different groups can be chosen for each target potential. For example, the 'endcap' target potential enables confinement along the trap axis (satisfying goal 1 of the DC confining potential) and can be taken to be symmetric about the $yz$-plane. Therefore, the electrodes chosen for each group can also be symmetric about the $yz$-plane, for example, group 1 – electrodes 5, 6, 7, 15, 16, 17; group 2 – electrodes 4, 8, 14, 18; and so on. There are multiple electrode groupings that can achieve the target coefficients, and so it is also an area of study as to which electrode groupings provide the best ion confinement by increasing trap depth.

The set of voltages required to be applied to all groups to achieve a target spatial 'potential configuration' is referred to as a 'voltage basis' for that configuration.

With $n$ groups, the linear programming problem can be formulated as follows, where the $ti$ subscript refers to the target coefficients, and $V_j$ to the voltage applied to the $j$th electrode group:
$$ \left(\begin{matrix}
\alpha_{tx}\\
\beta_{tx}\\
\alpha_{ty}\\
\beta_{ty}\\
\alpha_{tz}\\
\beta_{tz} \end{matrix}\right)
=
\left(\begin{matrix}
\alpha_{1x} & \cdots &\alpha_{nx} \\
\beta_{1x} & \cdots & \beta_{nx}\\
\alpha_{1y} & \cdots &\alpha_{ny} \\
\beta_{1y} & \cdots & \beta_{ny}\\
\alpha_{1z} & \cdots &\alpha_{nz} \\
\beta_{1z} & \cdots & \beta_{nz}\\
\end{matrix}\right)
\left(\begin{matrix}
V_1\\
V_2\\
\vdots \\
V_n
\end{matrix}\right)$$
We solve for $V_1 ... V_n$ using a linear solver.

### Limitations and Future Work
This solution is only valid for solving static confinement of a single ion. To model ion shuttling behavior and multi-ion arrays, more complex techniques have to be used, and this will be the subject of following research.

## Method Implementation in Python

We implemented this above routine in Python. The NIST Electrode package was used to simulate the trap and obtain a numerical solution for the spatial potential distribution. Numerical manipulations were performed using the Numpy, Scipy, and Pandas packages.

The repository containing this implementation can be found at: https://github.com/PQEGroup/trap-sim.
### Approximations and Assumptions (Python)
The Python implementation assumes that the the gaps between electrodes (5µm for reference chip) are almost negligible as they are over 24x smaller than the electrode widths. This solution therefore ignores effects of the exposed dielectric under the electrodes and fringing electric fields arising from non-ideal tiling of the electrodes on the trap surface. To assess the validity of this assumption, we have also conducted a series of multi-physics numerical simulations in COMSOL to factor in these non-ideal effects.
### Debugging and Installation

In this
# Acknowledgements

The authors would like to thank Professor Karan Mehta for his comprehensive and insightful guidance and opportunity to work on this fall research project, colleagues in the Photonics and Quantum Electronics Group at Cornell for their supportive and earnest discussions, and the Engineering Learning Initiatives for enabling this research.