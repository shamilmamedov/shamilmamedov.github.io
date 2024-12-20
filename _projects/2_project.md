---
layout: page
title: safe imitation learning 
description: safe imitation of a nonlinear model predictive control for flexible robots
img: assets/img/publication_preview/sanmpc_preview.gif
importance: 1
category: work
---


# Introduction
Controlling flexible robots is difficult primarily because they are infinite dimensional and underactuated. 
Existing model-based methods -- linear quadratic regulator [?] and model predictive controller [?] -- employ linearized models. 
More advanced and promising approach is nonlinear model predictive control (NMPC). The major limitation of NMPC preventing its widespread use is computational burden of solving nonlinear optimization problem. In this work we propose a method that attempts to bring NMPC to industry.

# Method
In this work we first formulate NMPC denoted as $$\pi_{NMPC}(x)$$ and show that it can successfully used for controlling flexible robot.   Then, we propose to approximate NMPC  using imitation learning algorithm called DAgger offline $$\pi_{NN}(x)$$. To guarantee safety of the approximated NMPC law, we propose to filter the output of $$\pi_{NN}(x)$$ with the safety filter. 

<p style="align: left; text-align:center;">
    <img src="/assets/img/projects/method.png" alt width="100%"/>
    <div class="caption">Figure 1. Proposed method for safe approximation of NMPC</div>
</p>

# Results

<!-- <div style="display: flex; justify-content: center; gap: 10px; align-items: center;">
    <div style="text-align: center;">
        <img src="/assets/img/projects/nmpc_vs_sanmpc_rollout_1.gif" alt="Task 1" style="width: 100%;" />
        <div class="caption">Caption 1</div>
    </div>
    <div style="text-align: center;">
        <img src="/assets/img/projects/nmpc_vs_sanmpc_rollout_1.gif" alt="Task 2" style="width: 100%;" />
        <div class="caption">Caption 2</div>
    </div>
    <div style="text-align: center;">
        <img src="/assets/img/projects/nmpc_vs_sanmpc_rollout_1.gif" alt="Task 3" style="width: 100%;" />
        <div class="caption">Caption 3</div>
    </div>
</div> -->

<p style="align: left; text-align:center;">
    <img src="/assets/img/projects/nmpc_vs_sanmpc_rollout_1.gif" alt width="70%"/>
    <div class="caption"> </div>
</p>

<p style="align: left; text-align:center;">
    <img src="/assets/img/projects/nmpc_vs_sanmpc_rollout_2.gif" alt width="70%"/>
    <div class="caption"> </div>
</p>

<p style="align: left; text-align:center;">
    <img src="/assets/img/projects/nmpc_vs_sanmpc_rollout_3.gif" alt width="70%"/>
    <div class="caption"> </div>
</p>