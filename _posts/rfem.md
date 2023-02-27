---
layout: post
title: Modeling deformable objects
date: 2023-02-27 17:05:00
description: 
tags: 
categories: 
---

# Introduction
The robotics community has shown significant interest in deformable object manipulation in recent years, with workshops hosted at ICRA and IROS in 2022. Both model-based and model-free approaches rely on accurate simulators. The finite element method (FEM), a powerful tool from continuum mechanics, can be computationally expensive. The rigid finite element method (RFEM) is a simpler and faster approach that can leverage existing rigid-body dynamics tools.

While not a new method, the RFEM — also known as the extended-flexible joint method or pseudo-rigid body method — remains a valuable modeling technique in robotics. This post explores its basic principles, limitations, and trade-offs, and provides a practical tutorial on simulating simple deformable linear object in Python using the Pinocchio library.

Whether you're a seasoned researcher or new to the field, this post offers valuable insights and practical guidance on using the RFEM to model deformable linear objects.

# Basics of RFEM

RFEM is a simple technique used to analyze deformable objects by breaking them down into smaller parts called rigid finite elements (rfes) and connecting them with spring-damping elements (sde). The technique uses generalised coordinates based on the elements' displacements to describe the position of the system. 

To help illustrate this concept, let's consider a simple example of a cylindrical rod attached to a motor, as shown in Figure 1. This system can be thought of as an elastic pendulum or a deformable linear object that is manipulated by a robot arm. Let's limit our discussion to only dominant bending flexibility and describe the displacements of each rfe relative to the preceding element.


<p style="align: left; text-align:center;">
    <img src="/assets/img/blog/RFEM_orig.png" alt width="60%"/>
    <div class="caption">Figure 1. Deformable linear object toy setup</div>
</p>

The RFEM discretizes in two stages:

1. Primary division: dividing the deformable object of length $L$ into relatively simple elements with finite dimension $\Delta l$, their spring and damping features concentrate at one point (the sde is obtained in this way) (Figure 2).
2. Secondary division: isolating rfes between sdes from the primary division, we obtain a system of rfes connected by sdes (Figure 3).

After discretization, we can treat the system as a serial chain of rigid bodies and derive its dynamics using the Lagrange or the Newton-Euler methods. To illustrate the general form of the RFEM dynamics, let's briefly go through the dynamics derivation using the Lagrange method. Let $q = [q_a\ q_p]^T$ denote the vector of joint angles, with $q_a$ being the active joint angle and $q_p$ being the passive joint angles of sdes. Using generalized coordinates, we can write down the energy functions used in the Lagrangian method

$$
K = \frac{1}{2}\dot q^T M(q) \dot q,\ P = \frac{1}{2} q^T K q + \sum_{i=0}^{n_s + 1} m_i g_0 p_{C_i}, \ D = \frac{1}{2}\dot q^T D \dot q.
$$

Here $n_s$ is the number of segments, $M(q)$ is the inertia matrix, $K$ and $D$ are the constant diagonal stiffness and damping matrices, respectively; $m_i$ and $p_{C_i}$ are $i-$the rfes mass and the center of mass, respectively; $g_0 = [0\ 0\ -9.81]^T$ is the gravity acceleration vector. Applying the Lagrange equations 

$$
\frac{d}{dt}\left(\frac{\partial \mathcal{L}}{\partial \dot q} \right) - \frac{\partial \mathcal{L}}{\partial q} = - \frac{\partial D}{\partial \dot q}
$$

with $\mathcal{L} = K - P$ being the Lagrangian, we get the final expression for the RFEM dynamics:

$$
M(q) \ddot{q} + C(q, \dot{q}) \dot{q} + K q + D \dot{q} +  g(q) = B \tau
$$

Comparing the final expression for RFEM dynamics to classical rigid-body manipulator dynamics, we see two new terms: $Kq$ representing linear spring forces and $D\dot q$ representing linear damper forces. Despite these new terms, the difference between RFEM dynamics and classical rigid-body dynamics is minor. Therefore, we can adapt existing efficient rigid-body algorithms to compute RFEM dynamics, and there is no need to implement new software.

To convert the model to state-space form for simulation and control purposes, we define a state vector $x=[q\ \dot q]^T$ and input $u:= \tau$:

$$
\dot x =  f(x, u) = 
\begin{bmatrix} 
\dot q \\
M(q)^{-1}
(B\tau - C(q, \dot{q}) \dot{q} - K q - D \dot{q} -  g(q))
\end{bmatrix} .
$$

# Parameters of rfes and sdes
The mass $m_i$, first moment of inertia $h=m_i p_{C_i}$, and second moments of inertia $I_i$ expressed in the frame located at the center of mass of an rfe are the fundamental parameters of the rfe. In the case of homogenous (constant density $\rho$) objects with constant cross-section, these inertial parameters can be calculated using standard formulas. However, for deformable objects with varying cross-sections and more complex shapes, CAD software is required.

Linear spring and damping parameters are the basic parameters of the sde. The stiffness $k_i$ of the spring is a three-dimensional diagonal matrix, while for the planar case, $k_i$ is a scalar. Similarly, the damping parameters $d_i$ also follow the same rule.

The coefficients of stiffness and damping of the sde are based on the elasticity features of a beam segment with length $\Delta l$, such that the real segment of the beam will deform in the same way and with the same velocities of deformation as the equivalent sde under the same load. For homogenous materials of a specific constant cross-section, there are readily available formulas to calculate these coefficients. However, for more complex shapes, CAD software must be utilized.

# Tutorial: implementing the RFEM with Pinocchio
