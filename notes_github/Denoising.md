---
tags:
- CG
date: 25/11/2022
---

# Denoising
Monte Carlo rendering often leads a lot of noise when we don't have many samples per pixel. 

## Image-Based Denoising
In the image-based denoising, we don't touch the rendering pipeline, but manipulate only on the rendered image. 
The main idea is to take advantage of the neighboring pixels in the meantime preserve the structure(not blurring).

### Bilateral Filter
#### Linear Filtering with Gaussian Blur(GB)
Convolution by a positive kernel is the basic operation in linear image filtering. It amounts to estimate at each position a local average of intensities and corresponds to low-pass filtering. The Gaussian blur(GB) operator is defined by:
$$
\text{GB}[I]_{\mathbf{p}} = \sum_{\mathbf{q}\in S} G_{\sigma}(\|\mathbf{p}-\mathbf{q}\|)I_{\mathbf{q}}
$$
where $G_{\sigma}(x)$ denotes the 2-dimensioanl Gaussian kernel:
$$
G_{\sigma}(x) = \frac{1}{2\pi\sigma^{2}}e^{ -x^{2}/2\sigma^{2} }
$$
Gaussian filtering is a weighted average of the intensity of the adjacent positions with a weight decreasing with the spatial distance to the center position $\mathbf{p}$.

#### Nonlinear Filtering with Bilateral Filter(BF)
Similarly to the Gaussian convolution, the bilateral filter is also defined as a weighted average of pixels. ==The difference is that the bilateral filter takes into account the variation of intensities to preserve edges.==

The rationale of bilateral filtering is that two pixels are close to each other not only if they occupy nearby spatial locations but also if they have some similarity in the photometric range.

The bilateral filter, denoted by $\text{BF}[\cdot]$ is defined by:
$$
\text{BF}[I]_{\mathbf{p}} = \frac{1}{W_{\mathbf{p}}}\sum_{\mathbf{q}\in S} G_{\sigma_{s}}(\|\mathbf{p}-\mathbf{q}\|)G_{\sigma_{r}}(I_{\mathbf{p}}-I_{\mathbf{q}})I_{\mathbf{q}}
$$
where $W_{\mathbf{p}}$ is a normalization factor:
$$
W_{\mathbf{p}} = \sum_{\mathbf{q}\in S} G_{\sigma_{s}}(\|\mathbf{p}-\mathbf{q}\|)G_{\sigma_{r}}(I_{\mathbf{p}}-I_{\mathbf{q}})
$$
Parameters $\sigma_{s}$ and $\sigma_{r}$ will measure the amount of filtering for the image $I$. $G_{\sigma_{s}}$ is a spatial Gaussian that decreases the influence of distant pixels, $G_{\sigma_{r}}$ a range Gaussian that decreases the influence of pixels $\mathbf{q}$ with an intensity value different from $I_{\mathbf{p}}$. Note that the term *range* qualifies quantities related to pixel values, by opposition to *space* which refers to pixel location.


![](attachments/Denoising-6.png#center%7Cbilateral%20filter%20weights%20of%20the%20central%20pixel)
- As the range parameter $\sigma_{r}$ increases, the bilateral filter becomes closer to Gaussian blur because the range Gaussian is flatter i.e., almost a constant over the intensity interval covered by the image.
- Increasing the spatial parameter $\sigma_{s}$ smooths larger features.

An important characteristic of bilateral filtering is that the weights are multiplied, which implies that as soon as one of the weight is close to $0$, no smoothing occurs.

### NL-Means

![](attachments/Denoising.png#center%7CDistances%20are%20computed%20by%20image%20patches%7C400)
The NL-Means filter computes the filtered value $\hat{u}(p)$ of a pixel $p$ in a color image $u=(u_{1},u_{2},u_{3})$ as a weighted average of pixels in the square neighborhood of size $(2r+1)\times(2r+1)$ centered at $p$
$$
\hat{u}_{i}(p) = \frac{1}{C(p)} \sum_{q\in N(p)} u_{i}(q)w(p,q)
$$
where 
- $N(p)$ is the square neighborhood centered on $p$
- $w(p,q)$ is the weight of contribution of $q$ to $p$
- $i$ is the index of color channel
- $C(p)$ is the normalization factor: $C(p) = \sum_{q \in N(p)} w(p,q)$

The weight $w(p,q)$ of a neighbor pixel $q$ is determined by the distance between a pair of small patches of size $(2f+1)\times(2f+1)$ centered at $p$ and $q$. ==Note that the distance here is a range distance, meaning we only care about pixel similarity.== 

The patch distance $d^{2}(P(p), P(q))$ is the average of the per-pixel and per color channel squared distances $d_{i}^{2}(p,q)$ over patches
$$
\begin{align}
d_{i}^{2}(p,q)  & = (u_{i}(p) - u_{i}(q))^{2} \\
d^{2}(P(p),P(q))  & = \frac{1}{3(2f+1)^{2}}\sum_{i=1}^{3}\sum_{n\in P(0)}d^{2}_{i}(p+n,q+n)
\end{align}
$$
where $P(p)$ is the patch centered on $p$, $P(0)$ represents the offsets to each pixel within a patch. When $f=1$, NL-Means reduces to bilateral filter. 

An important observation is that, because the input signal is noisy, the measured squared distances are biased. Therefore, the original NL-Means filter substracts the variance of the measured squared distances to cancel out the noise contribution from the patch distance. Assuming uniform pixel noise(every pixel value has the same variance) with variance $\sigma^{2}$ and uncorrelated pixels $p$ and $q$, the modified patch distance is 
$$
\max(0, d^{2}(P(p),P(q))-2\sigma^{2})
$$
The weight $w(p,q)$ of the contribution of pixel $p$ to $q$ is then obtained using an exponential kernel
$$
w(p,q) = e^{ -\frac{\max(0,d^{2}(P(p),P(q)) - 2\sigma^{2})}{k^{2}2\sigma^{2}} }
$$
where $k$ is a user specified damping factor that controls the strength of the filter. A lower $k$ value yields a more conservative filter. 

The patchwise extension produces slightly smoother outputs. In the patchwise implementation the final weight $W(p, q)$ for a pair of pixels is simply the average over all weights that involve these two pixels
$$
W(p,q) = \frac{1}{(2f+1)^{2}}\sum_{n\in P(0)} w(p+n, q+n)
$$

## [Adaptive Rendering with Non-Local Means Filtering](https://www.cs.umd.edu/~zwicker/publications/AdaptiveRenderingNLM-SIGA12.pdf)
The main idea of this paper is to adaptively alter the NL-Means filter and sampling during rendering. They use a variant of the NL-Means filter and use a dual buffer to estimate the variance of each pixel. They then estimate error in each iteration and give more samples to pixel with high error in the next iteration. 

### Non-uniform Variance NL-Means

![](attachments/Denoising-7.png#center%7COverview%20of%20the%20algorithm)
While the original NL-Means formulation assumes uniform noise over input images, Monte Carlo rendering generally leads to highly non-uniform noise patterns. The type of noise and its magnitude depend on the scene geometry, object materials, and light transport and lens effects.

Denote per pixel variance of color channel $i$ at pixel $p$ by $\text{Var}_{i}[p]$, replacing the uniform variance $\sigma$

$$
\tag{*}
d^{2}_{i}(p,q) = \frac{(u_{i}(p)-u_{i}(q))^{2} - \alpha(\text{Var}_{i}[p] + \text{Var}_{i}[q,p])}{\epsilon + k^{2}(\text{Var}_{i}[p] + \text{Var}_{i}[q])}
$$

^768c41

where $\alpha$ controls the strength of variance cancellation. In addition, define $\text{Var}_{i}[p,q] = \min(\text{Var}_{i}[p],\text{Var}_{i}[q])$ to clamp the variance at position $q$ to the variance at $p$. This ensures that the potentially large variance of brighter neighbors does not cancel out the measured squared difference, and it prevents bright regions from blurring into dark ones. 

#### Variance Estimation
Estimating the pixel variance $\text{Var}[p]$ is a key component of the algorithm (omit the index $i$ of the color channel for better readability from now on). Assuming that samples are drawn from a random distribution, we could estimate the pixel variance using the *empirical variance* of the samples contributing to the pixel, which we denote by $\Sigma[p]$. Random sampling, however, may lead to significantly higher variance than more sophisticated sampling strategies such as low-discrepancy sequences.

Dual-buffer supports low-discrepancy sampling. We obtain an initial estimate $\Delta[p]$ of the pixel variance $\text{Var}[p]$ using the squared difference between the two buffers, $\Delta[p] = (A(p) − B(p))^{2} /2$. This is an unbiased estimate, but it is rather noisy because it is based on just two samples, that is, the two buffers. We found that we can improve our results by applying an ==additional smoothing step to the initial variance estimates== $\Delta[p]$. 

#### Dual Buffer
In each iteration we sample $N$ samples in total and separate them evenly into two images, i.e. each image has $\frac{N}{2}$ samples. In the end we average the two images to get the final image. 

## [Robust Denoising using Feature and Color Information](https://www.cs.umd.edu/~zwicker/publications/RDFC-CGF13.pdf)
Filtering based on feature buffers, such as per pixel normal, texture, or depth, has proven extremely effective, in particular for images rendered with very few samples per pixel and high noise levels in the Monte Carlo output.

Denote feature buffers such as normals, textures, or depth, denoised and normalized to unit range, as $f_{j}$. The feature distance $\Phi_{j}^{2}(p,q)$ for feature $j$ between pixels $p$ and $q$ is based on the squared feature difference including variance cancellation similar to [](.md#^768c41%7Cequation%20(*)) 
$$
\Phi_{j}^{2}(p,q) = \frac{(f_{j}(p),f_{j}(q))^{2} - (\text{Var}_{j}[p] + \text{Var}_{j}[p,q])}{k_{f}^{2}\max(\tau,\text{Var}_{j}[p], \|\text{Grad}_{j}[p]\|^{2})}
$$
The distance is normalized by two factors:
- User parameter $k_{f}$ controls the sensitivity of the filter to feature differences
- $\max(\tau,\text{Var}_{j}[p], \|\text{Grad}_{j}[p]\|^{2})$ depends on the residual variance of the prefiltered feature denoted by $\text{Var}_{j}[p]$ and the squared gradient magnitude $\|\text{Grad}_{j}[p]\|$, thresholded to a minimum value $\tau$. This factor normalizes the feature distance relative to residual noise left in the filtered feature and the local feature contrast. measured by its gradient magnitude.

The overall distance $d_{f}(p,q)$ is obtained by taking the maximum distance over all $M$ features
$$
d_{f}^{2}(p,q) = \underset{ j \in [1,\dots,M] }{ \arg\max }\,\, \Phi^{2}_{j}(p,q)
$$
and the final feature weight $w_{f(p,q)}$ is obtained using an exponential kernel.

We determine the final filter weights by taking the minimum of the color and feature weights, that is
$$
w(p,q) = \min (w_{c}(p,q), w_{f}(p,q))
$$

|                           |                           |                           |                           |
| ------------------------- | ------------------------- | ------------------------- | ------------------------- |
| ![150](attachments/Denoising-2.png%5C) | ![150](attachments/Denoising-3.png%5C) | ![150](attachments/Denoising-4.png%5C) | ![150](attachments/Denoising-5.png%5C) |

## Data Driven Denoising
Please refer to 
[Kernel-Predicting Convolutional Networks for Denoising Monte Carlo Renderings-Paper22.pdf (disneyresearch.com)](https://la.disneyresearch.com/wp-content/uploads/Kernel-Predicting-Convolutional-Networks-for-Denoising-Monte-Carlo-Renderings-Paper33.pdf)
[Denoising with Kernel Prediction and Asymmetric Loss Functions (pixar.com)](https://graphics.pixar.com/library/MLDenoising2018/paper.pdf)

## References
[A Gentle Introduction to Bilateral Filtering and its Applications](https://people.csail.mit.edu/sparis/bf_course/course_notes.pdf)
[Robust Denoising using Feature and Color Information](https://www.cs.umd.edu/~zwicker/publications/RDFC-CGF13.pdf)
[Adaptive Rendering with Non-Local Means Filtering (umd.edu)](https://www.cs.umd.edu/~zwicker/publications/AdaptiveRenderingNLM-SIGA12.pdf)
[IPOL Journal · Non-Local Means Denoising](https://www.ipol.im/pub/art/2011/bcm_nlm/?utm_source=doi)