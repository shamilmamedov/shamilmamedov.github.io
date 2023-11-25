---
layout: page
title: learning deformable object dynamics
description: interpretible models for predicting deformable linear object dynamics
img: assets/img/publication_preview/dlo-excitation.gif
importance: 1
category: work
---




# Introduction
Modeling deformable objects poses great challenge for robotics community. Existing methods so far have mainly addressed quasi-static cases. In this work, our primary focus lies in modeling the highly nonlinear dynamics of deformable linear objects, including cables, ropes, and rods, based on limited observations. An illustrative example showcasing the typical motion patterns we seek to predict is presented above.

<p style="align: left; text-align:center;">
    <img src="/assets/img/projects/dlo-excitation.gif" alt width="60%"/>
    <div class="caption"> </div>
</p>

At a high level, our work addresses the following problem: *given measurements of positions, velocities and accelerations of one end of a deformable object, predict the position and velocity of the other end over time.*


# Proposed method
We propose an interpretible method that follows *encoder-dynamics-decoder* architecture. To be more precise:
- **encoder** reconstructs hidden states $$h_k$$ from partial observations $$y_k$$ and controlled inputs $$x_k$$;
- **dynamics** unrolls hidden states over time based on controller inputs $$x_k, x_{k+1},\dots,x_{k+N}$$;
- **decoder** maps hidden states and inputs into observartions.'

The proposed method named Finite Element Inspired Netowork (FEIN) is visualized below.

<p style="align: left; text-align:center;">
    <img src="/assets/img/projects/dlo-method.png" alt width="100%"/>
    <div class="caption"> </div>
</p>