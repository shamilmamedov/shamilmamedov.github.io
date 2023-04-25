---
layout: post
title: "Learning latent dynamics from partial observations"
date: 2023-04-24 09:00:00
description: 
tags: 
categories: 
---

Imagine you're trying to predict the behavior of a complex system, like a weather pattern, a financial market or a robot. In an ideal world, you would have access to all of the relevant data needed to accurately model the system (full state measurement). However, in reality, we rarely have access to a full state only to partial observations (outputs) which might be a subset of states or some nonlinear transformation of states. This presents a challenge for machine learning algorithms that rely on full state measurement.

In this post, we'll explore several methods for learning system dynamics from partial observations. We'll discuss why this problem is important, and why it's relevant to real-world applications like predicting weather, stock prices or robot behavior. By the end of this post, you'll have a better understanding of the challenges involved in learning system dynamics from partial observations, and the methods that are currently being used to tackle this problem.

# Problem formulation
To understand how we can learn system dynamics from partial observations, we need to define the problem more precisely. Let's start with the ground truth nonlinear dynamics, which can be defined by the following difference equation:

$$
x_{k+1} = F\big(x_k, u_k, \theta_{r}\big)
$$

where $$x_k = x(t_k)$$ is the vector of states of the system at time $$t_k$$, $$u_k$$ is the vector of external inputs and $$\theta_r$$ the vector of true parameters of the system. Let $$y_k$$ represent noisy observations (measurement) of the system

$$
y_k = H\big(x_k\big) + \nu_k
$$

where $$\nu_k$$ is a noise.  As an example, imagine we want to learn a robot dynamics shown in Figure 1. Starting from an initial latent state $$x_0$$ we can apply a sequence of external control inputs (joint torques, to be more precise) and measure some variabls, for example, positions of some keypoints using a camera. 

<p style="align: left; text-align:center;">
    <img src="/assets/img/blog/panda-dynamics-example.png" alt width="90%"/>
    <div class="caption">Figure 1. Robot as an example of partially observable system</div>
</p>


Given these noisy measurements, we would like to learn an approximation of the original system dynamics $$z_k = \hat F(z_k, u_k, \theta_a)$$ with $$\hat F(\cdot)$$ being the approximation of the original state transition map $$F(\cdot)$$ and $$\theta_a$$ being the parameters of the approximate transition map. If we know the dimension of the original state $$x$$ and happy about the dimension size, then we can choose $$z$$ to be of the same size. Otherwise — if we don’t know the dimension or want to find a lower dimensional approximation of the map $$F(\cdot)$$ — we can choose the dimension of $$z$$ arbitrarily to a degree that it can accurately predict outputs $$y.$$ 

For the sake of simplicity, lets use recurrent neural networks (RNNs) for approximating the dynamics. Figure 2 shows schematically the working principle of the vanilla RNN. In a nutshell, RNN takes an input $$u_k$$ and state $$\hat z_k$$ and spits out the next state $$\hat z_{k+1}$$.

<p style="align: left; text-align:center;">
    <img src="/assets/img/blog/rnn-schematics.png" alt width="60%"/>
    <div class="caption">Figure 2. Working principle of an RNN</div>
</p>


To sum up, we are interested in learning the model of a complex dynamical system using partial observations. The model should accurately predict $$N$$ next states/observations of the system. In essence, the problem we trying to solve boils down to finding an initial state $$z_0$$ that will be used by RNN to propagate the dynamics and state-to-output transformation $$\hat H(\cdot)$$. Now, lets dive into several methods that have been proposed to solve the this problem.

# Autoencoders

A common approach in machine learning for solving such problems is autoencoders. Here our input feature vector $$h$$ for the encoder is a sequence of output and input measurements. For simplicity, let's choose $$h_k = \left(y_k; u_k\right)$$. The encoder—a multilayer perceptron (MLP)—takes the input vector $$h_k$$ and outputs the latent state $$z_k = \phi\left(h_k\right)$$. Given the latent state and a sequence of inputs $$U = [u_k\ u_{k+1} \dots u_{k+N}]$$, the RNN propagates the dynamics and returns a sequence of states $$\hat{z}_{k+1},\ \hat{z}_{k+2},\ \dots,\ \hat{z}_{k+N}$$. The decoder—another MLP—takes states and maps them into a sequence of outputs $$\hat{y}_{k+1},\ \hat{y}_{k+2},\ \dots,\ \hat{y}_{k+N}$$. 

<p style="align: left; text-align:center;">
    <img src="/assets/img/blog/autoencoder-approach.png" alt width="80%"/>
    <div class="caption">Figure 3. Autoencoder based latent dynamics learning</div>
</p>


The loss function of the whole end-to-end learning is a combination of several losses:

* encoder-decoder loss which measures how well the enocder-decoder pair work together

$$\mathcal{L}_1= \sum_{k=1}^{N} ||h_k - \psi(\phi(h_k)) ||_2^2$$


* state reconstruction loss which measures if encoded latent state matches the dynamics of the system

$$\mathcal{L}_2 = \sum_{j=1}^{N} ||z_{k+j} - \hat z_{k+j}||_2^2$$ 

* Output prediction loss which measures how well the dynamics approximates the true dynamics

$$\mathcal{L}_3 = \sum_{j=1}^N||h_{k+j} - \psi(\hat z_{k+j})||$$

One paper where such an approach has been used in combination with SINDY is [1]. Check it out for more detail.