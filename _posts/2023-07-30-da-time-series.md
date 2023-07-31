---
layout: post
title: "Basic data augmentation methods for time series"
date: 2023-07-30 09:00:00
description: 
tags: 
categories: 
---
In recent years, neural networks have shown promising results in tackling problems related to time series analysis: forecasting, classification, anomaly detection, and data imputation. Many of these achievements can be attributed to the availability of large-scale datasets. However, collecting such data can be challenging when dealing with time series.

Data augmentation emerges as a method to increase the quantity of available data. Thereby, enhance the generalizability of models and reduce overfitting. In this article, we will explore fundamental data augmentation methods tailored specifically for time series analysis.

*Throughout the article, I will use the terms "time series" and "signal" interchangeably.*

# Jitering

Jittering, a simple yet popular data augmentation (DA) technique, involves adding noise to time series data. The method assumes that the data contain inherent noise, which is often the case when dealing with sensor data.

<p style="align: left; text-align:center;">
    <img src="/assets/img/blog/jittering.png" alt width="60%"/>
    <div class="caption">Figure 1. An example of jittering i.e. adding noise to a signal </div>
</p>

By adding noise to each time step, new samples can be generated, making it an effective approach to augment the dataset.Typically, Gaussian noise is used, where the mean and standard deviation define the magnitude and shape of the distortion, respectively. Applying jittering to a time series results in a new sample

$$
x'(\epsilon) = \{x_1 + \epsilon_1, \dots, x_t + \epsilon_t, \dots, x_T + \epsilon_T \}
$$

where $$\epsilon \sim \mathcal{N}(0, \sigma^2)$$ represents the additive noise at each time step. Although, Gaussian noise is the most popular choice for a noise, other more complicated noise patterns such as spike, step-like trend and slope-like trend can also be employed. Overall, jittering is a straightforward and reliable method that often leads to improved model performance.

# Scaling
Scaling changes the magnitude of certain steps in the time series while maintaining the overall shape of the signal. The transformed time series can be represented as:

$$
x'(\alpha) = \{\alpha x_1, \dots, \alpha x_t, \dots, \alpha x_T \}
$$

where $$\alpha > 0$$ defines the scale of the change. This scaling parameter can be defined using a Gaussian distribution with a mean of 1 and a standard deviation of $$\sigma$$ -- $$\mathcal{N}(1, \sigma^2)$$. This method is analogous to the "resize" operation used in computer vision.

<p style="align: left; text-align:center;">
    <img src="/assets/img/blog/scaling.png" alt width="60%"/>
    <div class="caption">Figure 2. An example of signal scaling </div>
</p>

More advanced scaling method is magnitude warping. It involves applying variable scaling to different points of the signal. This technique uses a set of knots $$u = u_1,\dots,u_i$$ to determine where the scaling transformation will be applied. These knots represent specific points in the time series where the scaling occurs and their values are generated using a normal distribution. The magnitude of scaling between the knots is determined by performing cubic spline interpolation on the knot values S(x) $$S(x)$$. Mathematically, the magnitude warping can be expressed as follows:

$$
x'(\alpha) = \{\alpha_1 x_1, \dots, \alpha_t x_t,\dots, \alpha_T x_T \}
$$

where $$\alpha = \alpha_1,\dots,\alpha_i = S(x)$$.

<p style="align: left; text-align:center;">
    <img src="/assets/img/blog/mag_warping.png" alt width="60%"/>
    <div class="caption">Figure 3. An example of magnitude warping of a signal</div>
</p>

Another scaling technique is time warping. While the concept behind time warping is similar to magnitude warping, the main difference between the two lies in the way they modify the signal. Time warping adjusts the temporal dimension of the signal by stretching or shortening the time (upsampling or downsampling) instead of modifying the magnitude at each step.

<p style="align: left; text-align:center;">
    <img src="/assets/img/blog/time_warping.png" alt width="60%"/>
    <div class="caption">Figure 4. An example of time warping of a signal</div>
</p>
Scaling methods can be very useful for medical data as people are different; the same health features can expressed differently.

Scaling methods are indeed valuable in the context of medical data, especially due to the inherent variability among individuals. Applying scaling techniques can account for these differences and make models more robust.

# Time slicing window

In time series analysis, slicing involves selecting a portion of each data sample to generate a new, distinct sample. When a section of the original data is cropped, it becomes a new sample; however, unlike image processing, maintaining all the essential features of the original data becomes challenging. The new sample can be represented as:

$$
x'(W) = \{x_\phi,\dots,x_t,\dots x_{\phi + W} \}
$$

where $$W$$ denotes the size of the cropped window and $$\phi$$ represents the initial point from where the slicing is initiated. One of the most important drawbacks of slicing the signal is that it can lead to invalid synthetic samples because it can cut off important features of the data. As a result, careful consideration is necessary when using slicing as a data augmentation method to avoid losing vital information during the process.

<p style="align: left; text-align:center;">
    <img src="/assets/img/blog/window_slicing.png" alt width="60%"/>
    <div class="caption">Figure 5. An example of window slicing</div>
</p>


# Rotation
Rotation can be applied to multivariate time series data by utilizing a rotation matrix with a specified angle. In univariate time series, the rotation is simply achieved by flipping the data. In the case of multivariate time series, a new rotated sample can be represented as follows:

$$
x'(R) = \{R x_1, \dots, Rx_t, \dots, Rx_T  \}
$$

where $$R$$ is the rotation matrix used to twist the data. The rotation angle $$\theta$$ can be randomly sampled from a normal distribution. However, this algorithm is not commonly employed in time series analysis as rotating a time series sample may result in the loss of important signal features.

<p style="align: left; text-align:center;">
    <img src="/assets/img/blog/rotation.png" alt width="60%"/>
    <div class="caption">Figure 6. An example of rotation applied to univariate signal</div>
</p>

Be aware that applying rotation can sometimes worsen the model performance as it might change the physics of data. For example, in physical systems applying rotation might change the gravity vector yielding physically inconsistent new sample — objects instead of falling under gravity might levitate or move randomly.

It is crucial to be cautious when applying rotation to time series data, as it may negatively impact the model's performance by introducing random features. For instance, in physical systems, applying rotation might lead to changes in the gravity vector, resulting in physically inconsistent new samples. Objects that should fall under gravity might appear to levitate or move randomly, introducing unrealistic behavior.

# Permutation

Shuffling different time slices of data in order to perform DA is a method that generates new data patterns.  The main problem of applying permutation is that it does not preserve time dependencies; thus, it can lead to invalid samples. Mathematically, new permuted sample can be defined as

$$
x'(w) = \{x_i,\dots,x_{i+w}, \dots, x_j, \dots, x_{j+w}, \dots, x_k, \dots, x_{k+w} \}
$$

where $$i,j,k$$ represents the first index slice of each window, so that each is selected exactly once, and $$w$$ denotes the window size.
<p style="align: left; text-align:center;">
    <img src="/assets/img/blog/permutation.png" alt width="60%"/>
    <div class="caption">Figure 7. An example of signal permutation: shuffling different window slices </div>
</p>


# Channel permutation

Changing the position of different channels in multi-dimensional data is a common practice. In computer vision, it is quite popular to swap the RGB channels to perform DA. With respect to time series, channel permutation can be applied as long as each channel of the data is still valid. The channel permutation algorithm, for multidimensional data such as $$x=\{\{x_{11},\dots,x_{1T}\}, \dots, \{x_{c1},\dots,x_{cT}\} \}$$ where $$c$$ is the number of channels, is given by

$$
x=\{\{x_{\sigma(1)1},\dots,x_{\sigma(1)T}\}, \dots, \{x_{\sigma(c)1},\dots,x_{\sigma(c)T}\} \}
$$

where $$\sigma: \{1,\dots,c \} \rightarrow \{1,\dots,c\}$$ is the used permutation of the channels.

<p style="align: left; text-align:center;">
    <img src="/assets/img/blog/channel_permutation.png" alt width="60%"/>
    <div class="caption">Figure 8. An example of channel permutation</div>
</p>


# Wrap up

In time series analysis, high-quality data is often limited. One simple approach to address this issue is by generating new data through the augmentation of existing samples.

In this article, we have introduced you to fundamental methods for augmenting time series data. Some of these techniques resemble those commonly used in computer vision, while others are specifically tailored for time series analysis,  such as time and magnitude warping.

The most straightforward and safe data augmentation method is **jittering**, which likely explains its popularity. However, you need to be careful when employing other augmentation methods; domain knowledge might be very useful for selecting the appropriate augmentation method. For instance, **rotation** may seem like a harmless augmentation, but in physical systems, it alters the gravity vector. As a result, the augmented dataset could negatively impact a model trained to predict the motion of objects affected by gravity.

**Scaling methods** might prove effective in medical applications, whereas **permutation** methods may be beneficial for classification tasks but might not significantly improve regression models. In the next article, we will explore more advanced augmentation techniques, which involve discovering hidden models that generated the existing data and subsequently using these models to generate new data. Yes, we are referring to variational autoencoders and generative adversarial networks.

# Libraries
1. [Tsaug](https://github.com/arundo/tsaug) — python library for time series data augmentation
2. [tsai](https://timeseriesai.github.io/tsai/) — popular python library for time series  modeling; it has a module for data augmentation

# References
1. Images are taken from [Talavera, Edgar, et al. "Data augmentation techniques in time series domain: A survey and taxonomy." *arXiv preprint arXiv:2206.13508* (2022).](https://arxiv.org/abs/2206.13508)

2. [Wen, Qingsong, et al. "Time series data augmentation for deep learning: A survey." *arXiv preprint arXiv:2002.12478* (2020).](https://arxiv.org/abs/2002.12478)