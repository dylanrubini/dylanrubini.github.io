---
layout: page
title: Can we bring multiphysics modelling to the design level?
description: Accelerating reacting flow computations using ML
img: assets/img/auto.jpg
importance: 1
category: work
related_publications: false
---


## Executive Summary

The role of the chemical industry in modern society is rapidly evolving; on the one hand, it must develop novel technologies based on the new requirements for the energy transition, and on the other hand, it must decarbonise, transform and modernise an existing asset base of chemical process. These existing assets, including hydrocarbon cracking units, hydrogen production plants, ammonia production facilities, etc., rely on fossil-fuel-combustion-based furnaces to supply energy to the chemical process. Radically new technologies are necessary to decarbonise these processes efficiently while maintaining profitability. On top of this, the energy transition unlocks a wide variety of opportunities for the industry, such as carbon capture, electrochemical process (*e.g.,* electrolysers), synthesising sustainable feedstocks & fuels, and many more. Given the urgent climate emergency, these developments need to be accelerated.

However, a key bottleneck in designing, developing and optimising these new technologies and chemical processes is aerochemically coupled numerical simulations using detailed, comprehensive (and accurate) chemical kinetic models. Large kinetic models are often required for accuracy (consisting of $$\mathcal{O}(1000)$$ species and $$\mathcal{O}(10000)$$ reactions). But the price paid is an acute increase in computational overhead, as CPU time scales cubically with the number of species $$\mathcal{O}(N_{\mathrm{species}}^3)$$. Therefore, this places a heavy burden on accurate, but time-consuming and costly, physical experiments in order to reduce uncertainty and risk. 

To address this, this work proposes a new methodology to mitigate this computational bottleneck. We will demonstrate that it is now possible to accelerate reacting flow numerical simulations by two to three orders of magnitude, while accurately capturing the response dynamics of a *detailed* chemical kinetic model and maintaining a high-fidelity 3-D viscous aerothermal fluid flow model. This new methodology is an automated approach that leverages machine learning to uncover preexisting low-dimensional latent spaces hidden within the governing physics. 

In this report, this methodology is explained in the context of a new turbomachinery concept that can decarbonise endothermic chemical reaction processes such as steam cracking. This is an ideal test bed because (1) the flowfield is highly complex and there are unique aerochemical interactions, and (2) this is the first time turbomachines have had to be designed to control and optimise chemical reaction dynamics along the bladed flow path; therefore, there is limited experimental data to guide the design process. By accelerating reacting flow simulations (100 to 1000 times) using this new methodology, it will be possible to perform aerodynamic optimisation early in the design cycle to maximise reaction efficiency and minimise coke formation rate (which are key design metrics of the process). To put into context the importance of tuning the chemical reaction through careful aerodynamic design, for a typical hydrocarbon cracking plant (with capacity = 1 million tonnes per year), a 10% improvement in reaction efficiency can increase annual profits for the plant operator by 90 -- 110 million euros (as of 2024).

To further advance this methodology, we intend to generalise and modularise this numerical tool into a user-friendly platform that can accelerate chemical kinetic modelling for a wide variety of industry-relevant chemical reaction processes ranging from hydrogen production and synthetic fuel production to electrochemical reactions. A critical hurdle in generalising this methodology is the incorporation of catalytic surface reactions. Since catalysts dominate the chemical industry, *e.g.,* for synthetic fuel production, hydrogen production, etc., accelerating catalytic reactive flow simulations would be a game changer. A numerical tool that can accurately but highly efficiently predict yield and coking distributions in chemical processes will help produce more cost-effective designs, reduce dependence on costly physical experiments, and ultimately contribute to accelerating the net-zero transition.

## Introduction and Motivation

Decarbonising high-temperature industrial heat within fossil-fuel-fired furnaces is the ``grand challenge'' of energy engineering, and is a critical hurdle in the development of a net-zero society. Greenhouse gas emissions from high-temperature heat ($$T > 400 ^{\circ}$$C) alone account for more than 10% of the global total. For low-temperature heat, various mature low-carbon technologies already exist. However, high-temperature sectors have been plagued with techno-economic hurdles. To address this, this figure showcases

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/rdroverall.jpg" title="The New Turbo-Reactor" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Electrified turbo-reactor could decarbonise many hard-to-abate industrial processes
</div>

the revolutionary new turbomachine called the turbo-reactor proposed as an efficient, effective and financially attractive solution for decarbonising over 40 hard-to-abate sectors. By replacing the surface heat exchange energy transfer mechanism within a furnace with direct mechanical energy transfer to the fluid within the turbo-reactor, the power density can increase by 50 to 500 times, while simultaneously improving the quality of the process. This is enabled by a new turbomachinery principle, in which all mechanical energy transferred to the fluid is converted into kinetic energy and subsequently into internal energy of the fluid, rather than pressure. 

This research focuses on endothermic chemical reaction applications of the turbo-reactor (*e.g,* hydrocarbon cracking, as they pose a unique set of new requirements for the turbomachinery design. For the first time in turbomachinery, the working fluid chemically reacts along most of the bladed path. Therefore, to design the aerothermal bladed path to optimise the chemical reaction dynamics, an accelerated aerochemically coupled numerical solver is required. This work introduces a multi-fidelity, efficiently coupled aerochemistry model, called $$\texttt{ChemZIP}$$, guided by machine learning (ML) to routinely bring reacting flow simulations into the early design phases, rather than being ``one-off'' simulations.

## Data-Driven Efficiently Coupled Aerochemistry Flow Solver

### Importance

For applications of the turbo-reactor where the fluid undergoes a chemical reaction, in addition to power input requirements, the driving design metric is reaction efficiency rather than aerodynamic efficiency. The reaction efficiency is strongly affected by aerochemical interactions due to the complexity of the flow physics and the similarity of the characteristic fluid and chemical timescales. However, up to now, these rich multiphysics interactions have been ignored due to the prohibitive computational cost of fully-reacting flow simulations, making it challenging to perform design optimisation of the machine and thus meet reaction performance targets. To address this, the highly efficient multi-fidelity ML-assisted $$\texttt{ChemZIP}$$ methodology is introduced to mitigate the computational burden of aerochemically coupled computational fluid dynamics (CFD) without sacrificing accuracy. Consequently, for the first time in turbomachinery, aerochemically guided optimisation can be incorporated at an early stage of the design, potentially facilitating a step-change improvement in reaction performance. 

### Methodology

These figures outline the new multi-fidelity ML-assisted $$\texttt{ChemZIP}$$ methodology to accelerate multiphysics numerical analysis of the turbo-reactor.
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/training.JPG" title="Training database generation workflow" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Multi-fidelity ML-assisted ChemZIPtraining database generation workflow
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/auto.JPG" title="Autoencoder" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Multi-fidelity ML-assisted ChemZIP methodology overview
</div>

First, non-reacting CFD of the turbo-reactor establishes a realistic envelope of energy supply profiles into the chemical reaction. From this envelope, thousands of energy supply profiles are generated and applied to a detailed chemical kinetic model in a 1-D plug-flow reactor (PFR) environment. These randomly generated energy supply profiles (within a realistic envelope) lead to a broad spectrum of reaction perturbations for which the thermochemical state (*e.g.,* $$Y_k$$ & $$T$$) and response dynamics (*e.g.,* $$\dot{\omega}_k$$) can be recorded. This forms a large database for training the $$\texttt{ChemZIP}$$ model with over 5 million samples. By simplifying the fluid dynamics (*i.e.,* by using a PFR) while retaining detailed kinetics, this methodology provides an elegant solution to rapidly generate a training database that densely and widely covers the relevant region of the thermochemical space in only a few hours.

Subsequently, a ML-assisted $$\texttt{ChemZIP}$$ model is developed using an autoencoder neural network (NN). This NN architecture compresses the high-dimensional thermochemical space ($$\mathcal{O}(200 - 1000)$$ chemical species and $$\mathcal{O}(1000 - 10000)$$ reactions) into a substantially lower-dimensional latent space ($$\mathcal{O}\{3\}$$), where a reduced set of *meta* species $$Z_m$$ are mapped to their corresponding source terms $$\dot{\omega}_{Z_m}$$. These source terms represent the dynamic response in the latent space. In effect, the training procedure described above produces a ``custom compression algorithm'' that uncovers preexisting low-dimensional latent spaces hidden within the governing physics.

Coupling the $$\texttt{ChemZIP}$$ with a 3-D viscous CFD solver entails adding only three additional ``meta'' species scalar transport equations, where the dynamics are captured through source terms $$\dot{\omega}_{Z_m}$$ mapped by the pre-trained NN. A key feature of this methodology is that on the fluid flow modelling side, no new modelling assumptions are made, and its fidelity can range from RANS to high-fidelity LES modelling.

Once the CFD has converged, the high-dimensional physical space, consisting of $$\mathcal{O}\{200 - 1000\}$$ species mass fractions, can be reconstructed (*i.e.,* decompressed). This powerful methodology enables a speed-up of 100 to 1000 times relative to reacting flow CFD with detailed chemical kinetics, while maintaining high-fidelity aerothermal modelling and accurately capturing the dynamics of a detailed kinetic model. This will enable aerochemical optimisation to be brought to the design level for the first time. 


### 3-D Viscous Verification and Proof-of-Concept

To demonstrate the feasibility, success, and future potential of this new methodology, numerical predictions of the $$\texttt{ChemZIP}$$ are compared with an industry-standard 3-D viscous reacting flow CFD solver Fluent with a detailed chemical kinetic mechanism. A 5 m long heated square duct is used as a verification test case. Crucially, the figure below illustrates that the $$\texttt{ChemZIP}$$ can achieve a two-order-of-magnitude speedup relative to a commercial solver with detailed kinetics.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/speedup.JPG" title="Speedup" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Performance benchmarking of ChemZIP compared with a state-of-the-art solver
</div>

<!-- This sizeable computational acceleration is achievable whilst maintaining a sufficiently good level of accuracy such that reliable design optimisation can be performed in the future. Figure~\ref{fig:validation_yields} demonstrates that the \texttt{ChemZIP} can predict dry yields for both major and minor species within a few percent relative error compared to \textsc{Fluent}. Over a long distance of \SI{5}{\meter} the accumulation of errors for major and minor species is minimal, indicating robustness and ensuring accurate forecasts even over extended time intervals. Despite not being trained on any three-dimensional test cases (\textit{i.e.,} all of the training data is from 1-D PFR simulations), the \texttt{ChemZIP} accurately predicts gradients in the yield perpendicular to the flow direction due to 3-D temperature non-uniformities (due to heated walls) and mass diffusion. -->


<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/parity.JPG" title="Parity plot" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Parity plots against ground truth
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/integrated.JPG" title="Time integrated" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Time integrated deployment of the model model
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/yields.JPG" title="Yield contours" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Verification of yield contours
</div>