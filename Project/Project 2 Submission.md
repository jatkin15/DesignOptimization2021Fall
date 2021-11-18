
# ANSYS DOE and Design Optimization 

## Table of Contents
1. [Introduction](#introduction)
2. [A Running Example](#example)
    1.  [Description of the brake design problem](#brake)
    2.  [Static structural setup](#structural)
    3.  [Modal setup](#modal)
    4.  [Transient thermal setup](#thermal)
    5.  [Transfering geometry and model across analysis modules](#transfer)
    6.  [Define input parameters](#input)
3. [Background Knowledge](#background)
4. [Design of Experiments](#doe)
5. [Sensitivity Analysis](#sensitivity)
6. [Optimization](#optimization)
7. [Checklist](#checklist)

***

## Introduction <a name="introduction"></a>




## Things to check during your analysis and optimization <a name="checklist"></a>
* What are your design variables, constraints, and objectives?

Design variables are dimentions of the brake drum near the break pads. They are all real numbers, so the domain is defined over $\mathbf{R}^n$, with $n$ being the number of dimentions. They are constrained by the physical limits of the size of break pads, but these are subject to minimum and maximum bounds which are linear. For each dimention $n$, we have $2n$ inequality constraints and they form a convex set (TODO: More info about this). The objectives are minimizing the volume, temperature generation by friction, and maximizing the natural frequency to avoid resonace that could lead to failure. 

* What are the potential trade-offs between your objectives?

The objectives are correlated strongly. Volume is inversely proportional to thermal mass so therefore minimizing volume increases max temperature. The volume and the natural frequency are related and one would expect increasing the volume would increase the natural frequency. Decreasing volume would also cause the brake drum to fail sooner. 

* Are your variables continuous? Or are they discrete/integer?

All continuous.

* Do you have analytical objective/constraint functions? And are they differentiable?

The objective function is not entirely analytical but knowing the governing equations can give insight into expected trends. The constraints for the bounded dimentions are differntiable, but they are also constant and therefore have a zero derivative. 


* Based on the above answers, what optimization methods will you choose?

TODO


* Perform a sensitivity analysis and comment on the importance of your variables? 

Also, do you observe monotonicity (i.e., the objective always goes up or down with a variable)?
TODO: This whole thing but also we expect monotonicity due to knowing the equations that govern these types of interactions.


* Compare your optimal design against the initial one (e.g., see
the following comparison on the brake disc design) AND comment on whether the optimal design is reasonable.

<img src="/Project/tutorial_ansys/prepostopt.png" alt="Drawing" style="height: 400px;"/> 

[agdb]: /Project/tutorial_ansys/brake.agdb
[ansys]: https://www.scribd.com/document/355011614/Design-Exploration-Users-Guide
[igs]: /Project/tutorial_ansys/brake_Geom.igs
[stp]: /Project/tutorial_ansys/brake_Geom.stp
[report]: http://designinformaticslab.github.io/_teaching//designopt/projects/2016/desopt_2016_04.pdf
[boreview]: https://arxiv.org/pdf/1012.2599.pdf?bcsi_scan_dd0fad490e5fad80=fwQqmV5CfHDAMm8dFLewPK+h1WGiAAAAkj1aUQ%3D%3D&bcsi_scan_filename=1012.2599.pdf&utm_content=buffered388&utm_medium=social&utm_source=plus.google.com&utm_campaign=buffer


# Introduction

### Description of the brake design problem <a name="brake"></a>
To summarize, the brake design problem has the following objectives:

* Design a brake disc for emergency braking conditions with minimal volume
* Minimize the maximum stress in the brake disc
* Maximize the first natural frequency of the brake disc
* Minimize the maximum temperature in the brake disc

The three subsystems are as follows:

* Structural Analysis: The brake disc has to sustain the pressure from the 
hydraulically actuated brake pads during sudden braking conditions. Stresses are induced 
due to friction between the brake pads and the disc. The disc also experiences centrifugal 
body forces due to its rotation. Resultant stresses generated due these forces can lead to
material failure. Therefore, it is of prime importance to make sure that the stresses in 
the disc are minimized. 

* Modal Analysis: Free modal analysis is performed to ensure that the 
disc's first natural frequency is higher than the engine firing frequency. This guarantees 
that the disc does not experience failure due to resonance.

* Thermal Analysis: Braking in a vehicle takes place due to friction 
between the brake pads and the rotor disc. This leads to heat flux generation in the disc 
which consequently results in increase in its temperature and thermal stresses. Emergency 
braking conditions induce high temperatures that damage the contact surfaces. It is therefore 
essential to minimize the temperature to prevent disc wear and tear. 

The following sections describes how these analysis are set up.

### Disclaimer
The computer used to run this simulation was not the most powerful meaning it was difficult to generate very high quality results before the deadline of this project. Futhermore I have never used ANSYS or any FEA/FEM package prior to this project which made debugging time consuming. I want to update the results section further after running a very long simulation with high numbers of samples, but the reported results are the best I was able to generate. 

# Theoretical Analysis

### Formulation

We can formally define the multi-objective optimization problem as 

$$
\begin{aligned}
\min_{x} \quad & \{f_i(x)\}, i \in (1, 2, ..., n)\\
\text{s.t.} \quad &  a_l - x \leq 0\\
\quad &  x - a_u \leq 0\\
\end{aligned}
$$

Where
* $f_i(x)$ are the different objective functions that correspond to maximum stress, natural frequency (here it was made negative to change maximization problem into minimization), and maximum temperature. These functions are not closed form and the result of the ANSYS simulation.
* $x$ is the design inputs written as a vector. They correspond to the brake thickness, inner diameter, and outer diameter which are all continuous quantities. 
* $a_l$ and $a_u$ are the upper and lower bounds for the inputs to keep the problem realstic and feasible. 

### Design Variables and Constraints

In vector form the design variables can be thought of as a vector $x \in \mathbb{R}^3$. The constraints on the design variables in this problem are only constant upper and lower bounds, so the problem has only linear constraints which are both analytical and differentiable. When these constraints are applied to the full set of $\mathbb{R}^3$ they reduce the space into a rectangular region which forms a convex set. The problem itself if not convex due to the non-analytical objective functions discussed later.

### Expected Properties and Trade-offs of Objective Functions

The objective functions $f_i(\cdot)$ do not have closed form representations and therefore cannot be differentiated analytically. Automatic differention methods may be possible but it is unlikely to be computationally feasible. Even though the objective functions are not closed form and must be approximated, we can use theoretical governing equations based to extract extpected relationships between the input part dimentions and the outputs we are trying to minimize. 

* Stress

    It follows that reducing the brake's outer and inner diameters can reduce the area of contact between the brake pad and the drum. Naturally reducing the area of an applied pressure while holding other parameters constant would reduce the net force from a constant applied pressure on the drum and therefore reduce the maximum stress from the brakes. Note that reducing this applied force from the brake pads could negativelly affect driving performance because it would make it more difficult for the car to stop. Centerfugal sources of stress would suggest increasing the outer diameter would increase the maximum stress but these terms are likely small compared to the braking forces. 
    $$P = F / A(x)$$


* Natural Frequency

    For simple second-order systems, the natural frequency is given by 
    $$
    \omega_0 = \sqrt{k / m(x)}
    $$
    The mass of a rigid body is a function of its density and dimensions. Increasing the part dimensions, $x$, would then increase the system's mass and therefore decrease the natural frequency. This system is more complex than a second-order system so this equation does not directly apply to this case, but this relationship can still give insights to expected correlations.  


* Temperature

    For a simple thermal system, thermal mass relates incoming thermal energy to temperature change and is
    $$
    C_{th} = m(x) c_P
    $$
    with $c_P being the specific heat capacity of the material, and the relationship between thermal energy and temperature change is
    $$
    Q = C_{th}(x) \Delta T \implies T_f = Q / C_{th}(x) + T_0
    $$
    It then follows that increasing the part dimensions would increase the mass which would then increase the amount of energy needed to cause a change in temperature, thus reducing the total gain in temperature over a fixed time period. 

The estimated relationships between the design variables and objective functions do not suggest discontinuities or singularities over the range of realistic values. All the underlying govering physics equations are smooth and therefore we should expect the non-analytical objective functions to be well approximated by a smooth response surface. 


# ANSYS Details



### Setup

The setup and simulation of the problem in ANSYS was formed by closely following the instructions given in the assignment. 

<img src="/Project/img/ansys_structure.png" alt="structure" style="height: 400px;"/> 

### Departures from the Given Problem Statement

The stated goal of minimizing volume along with maximum stress, maximum temperature, and (negative) natural frequency is partially redunant due to correlations in these output variables. Minimizing the temperature will naturally reduce volume and maximizing the natural frequency also naturally reduces the volume. Therefore, volume was not directly included as a part of the analysis to increase computation time and interpretability of results. 

Also in the main analysis, the inner and outer diameters were kept constant initially and only thickness was optimized mostly for computational purposes. This was kept into later testing due to time constraints and issues with the automatically generated geometry failing to update properly and thus stopping a simulation. This change is major but can be justified as adjusting the inner and outer diameter of the brake drum is also not entirely practical given the purpose of brake drums. The entire contact area of the drum and pad should be used in order to distribute wear evenly on the drum, so adjusting the drum and leaving the pad constant is not realistic. The thickness is a much easier parameter to adjust in a real setting, although I admit only considering thickness makes the results of this analysis less interesting. Some preliminary results that include varying the diameters are also included but they are not nearly as detailed as the thickness-only results. 

### Design of Experiments

* Discuss bounds for values and sampling.



### Response Surface

* Discuss 

### Optimization Method

The selected method for optimization was Multiobjective Genetic Algorithm (MOGA) due to the multiple objective functions. The problem could be reformed by incorporating all objectives except for one as constraints, but this would greatly complicate the constraint set. Another approach would be to define a scalar cost function that computes a weighted sum of the multiple cost functions, but this has issues as the weights can be set arbitrarily which can change results. 

Due to extremely long evaluation times, the optimization was performed with $50$ initial samples using the Optimial Space-Filling option and the number of samples per iteration was kept at $20$. The maximum iteration number was also kept to $40$, giving a total estimate of $830$ evaluations. 


# Results

### Overview

The optimization scheme returned three candidate thickness values for consideration which were all better than the initial design value, especially for stress. The value with the lowest maximum stress was for a thickness of 22.3 mm, only about a 3 mm change from the 25 mm starting value. With the change being this small, there is nearly no noticable change in the optimal value's maximum temperature and natural frequency compared to the initial value. However, the maximum stress for the optimal value was $1.0144\text{e+}007$ Pa compared to the initial value of $1.6267\text{e+}007$. The full output report is included in the folder with this workbook for more detailed outputs.

<img src="/Project/img/brake_side_comp.png" alt="side_comparison" style="height: 400px;"/> 

### Sensitivity Analysis

<img src="/Project/img/brake_sensitivity.png" alt="thickness_sensitivity" style="height: 400px;"/> 


<img src="/Project/img/brake_sensitivity_multi.png" alt="multi_sensitivity" style="height: 400px;"/> 

# Discussion

### Optimal Result


### Realism of Result



# Checklist

## Not Done

* Perform a sensitivity analysis and comment on the importance of your variables? Also, do you observe monotonicity (i.e., the objective always goes up or down with a variable)?

* Compare your optimal design against the initial one (e.g., see the following comparison on the brake disc design) AND comment on whether the optimal design is reasonable.

## Done

* What are your design variables, constraints, and objectives? Done.

* What are the potential trade-offs between your objectives? Mostly done.

* Are your variables continuous? Or are they discrete/integer? Done.

* Do you have analytical objective/constraint functions? And are they differentiable? Done.

* Based on the above answers, what optimization methods will you choose? Mostly done. (MOGA, need multi-objective)

