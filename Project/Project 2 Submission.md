---
categories: productdesign_tutorial
layout: apd_default
title:  Analytics - ANSYS DOE and Design Optimization Tutorial
published: true
---
# ANSYS DOE and Design Optimization Tutorial
by Yi Ren and Aditya Vipradas 


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