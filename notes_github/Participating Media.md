---
tags:
- CG
date: 04/11/2022
---

# Participating Media

## Physics Background
Light is affected as it passes through participating media—large numbers of very small particles distributed throughout a region of 3D space. Volume scattering models are based on the assumption that there are so many particles that scattering is best modeled as a probabilistic process, rather than directly accounting for individual interactions with particles.

### Media
There are three main processes that affect the distribution of radiance in an environment with participating media:
- Absorption: the reduction in radiance due to the conversion of light to another form of energy, such as heat
- Emission: radiance that is added to the environment from luminous particles
- Scattering: radiance heading in one direction that is scattered to other directions due to collisions with particles

| Description                                                                               | Representation                      | Formula                                                                                         |
| ----------------------------------------------------------------------------------------- | ----------------------------------- | ----------------------------------------------------------------------------------------------- |
| Absorption: <br> absorption coefficient $\sigma_{a}(\mathbf{x})$ in $[m^{-1}]$ | ![400](attachments/Participating%20Media-2.png) | $$\small dL(\mathbf{x},\vec{\omega}) = -\sigma_{a}(\mathbf{x})L(\mathbf{x},\vec{\omega})dz$$    |
| Emission                                                                                  | ![400](attachments/Participating%20Media-5.png) | $$\small dL(\mathbf{x},\vec{\omega}) = \sigma_{a}(\mathbf{x})L_{e}(\mathbf{x},\vec{\omega})dz$$ |
| In-scattering: <br> scattering coefficient $\sigma_{s}(\mathbf{x})$ in $[m^{-1}]$         | ![400](attachments/Participating%20Media-4.png) | $$\small dL(\mathbf{x},\vec{\omega}) = \sigma_{s}(\mathbf{x})L_{s}(\mathbf{x},\vec{\omega})dz$$ |
| Out-scattering <br> scattering coefficient $\sigma_{s}(\mathbf{x})$ in $[m^{-1}]$         | ![400](attachments/Participating%20Media-3.png) | $$\small dL(\mathbf{x},\vec{\omega}) = -\sigma_{s}(\mathbf{x})L(\mathbf{x},\vec{\omega})dz$$    |

Combining the four components of outgoing ray, we have the *radiative transport equation*

$$
\begin{aligned}
d L(\mathbf{x}, \vec{\omega})=&-\sigma_a(\mathbf{x}) L(\mathbf{x}, \vec{\omega}) d z &-\sigma_s(\mathbf{x}) L(\mathbf{x}, \vec{\omega}) dz \\
& +\sigma_a(\mathbf{x}) L_e(\mathbf{x}, \vec{\omega}) d z &+\sigma_s(\mathbf{x}) L_s(\mathbf{x}, \vec{\omega}) d z
\end{aligned}
$$

^3f7db4

We can still simplify the notation by denoting 

$$
\sigma_{t}(\mathbf{x}) = \sigma_{s}(\mathbf{x}) + \sigma_{a}(\mathbf{x})
$$

to be the extinction coefficient.

Consider from now on only the extinction and solve for

$$
dL(\mathbf{x},\vec{\omega}) = -\sigma_{t}(\mathbf{x})L(\mathbf{x},\vec{\omega})
$$

which gives

$$
\large
\frac{L_{z}}{L_{y}} = e^{ -\int_{\mathbf{y}}^{\mathbf{z}} \sigma_{t}(\mathbf{x}) \, d\mathbf{x}  }
$$

Define the *transmittance* to be 

$$
T_{r}(\mathbf{x},\mathbf{y}) = \frac{L(\mathbf{y})}{L(\mathbf{x})}
$$

which expresses the remaining radiance after traveling a finite distance through a medium.

Homogeneous and heterogeneous media are distinguished by the constancy of the extinction coefficient $\sigma_{t}$ distributed in the space:
- Homogeneous media ![400](attachments/Participating%20Media.png)
- 
$$
T_{r}(\mathbf{x},\mathbf{y}) = e^{ -\sigma_{t}\|\mathbf{x}-\mathbf{y}\| } \quad(\text{Beer-Lambert Law})
$$

- Heterogeneous media  ![Participating Media-1](attachments/Participating%20Media-1.png) 
- 
$$
T_{r}(\mathbf{x},\mathbf{y}) = e^{ -\int _{0}^{\|\mathbf{x}-\mathbf{y}\|}\sigma_{t}(t) \, dt  }
$$

![](attachments/Participating%20Media-6.png#center%7CTransmittance%20along%20a%20direction%7C400)

Transmittance is multiplicative

$$
T_{r}(\mathbf{x},\mathbf{z}) = T_{r}(\mathbf{x},\mathbf{y})T_{r}(\mathbf{y},\mathbf{z})
$$

### Phase Function
Phase function describes the distribution of scattered light. It's an analog of BRDF but for scattering in media. It's probability density function such that it integrates to unity

$$
\int _{S^{2}}f_{p}(\mathbf{x},\vec{\omega}_{i},\vec{\omega}_{o}) \, dx = 1
$$

#### Isotropic Scattering
The rays are scattered uniformly

$$
f_{p}(\vec{\omega}_{i}, \vec{\omega}_{o}) = \frac{1}{4\pi}
$$

![](attachments/Participating%20Media-16.png#center%7CIsotropic%20scattering%7C400)

#### Henyey-Greenstein Phase Function

$$
f_{p_{\text{HG}}}(\theta) = \frac{1}{4\pi} \frac{1-g^{2}}{(1+g^{2}-2g\cos\theta)^{3/2}}
$$

where $\theta = \arccos(-\vec{\omega}\cdot \vec{\omega}')$
![600](attachments/Participating%20Media-7.png)
![Participating Media-8](attachments/Participating%20Media-8.png)

When
- $g=0$ -> isotropic scattering
- $g>0$ -> forward scattering
- $g<0$ -> backward scattering

#### Schlick's Phase Function

$$
f_{p_{\text{Schlick}}}(\theta) = \frac{1}{4\pi} \frac{1-k^{2}}{(1-k\cos\theta)^{2}}
$$

where $k = 1.55g - 0.55g^{3}$.

This is a cheap approximation of HG.

#### Rayleigh Scattering

$$
f_{p_{\text{Rayleigh}}}(\theta) = \frac{3}{16\pi}(1+\cos ^{2}\theta)
$$

![400](attachments/Participating%20Media-17.png)
Rayleigh scattering is an approximation of Maxwell equations for tiny scatterers that are typically smaller than 1/10 of wavelength of visible light. It's used for atmospheric scattering, gasses, transparent solids.

Rayleigh scattering also defines the scattering coefficient:

$$
\sigma_{s_{\text{Rayleigh}}}(\lambda,d,\eta,\rho) = \rho\frac{ 2\pi^{5}d^{6}}{3\lambda^{4}}\left( \frac{\eta^{2}-1}{\eta^{2}+2} \right)^{2}
$$

![600](attachments/Participating%20Media-9.png)
where
- $\lambda$ is the wavelength
- $d$ is the diameter of scatterers
- $\eta$ is the index of refraction
- $\rho$ is the density of scatterers

#### Lorenz-Mie Scattering
If the diameter of scatterers is on the order of light wavelength, we cannot neglect the wave nature of light. The solution is too complex and is not demonstrated here.

| Phase Function    | Examples                        | 
| ----------------- | ------------------------------- |
| Isotropic         | ![400](attachments/Participating%20Media-10.png) |
| Henyey-Greenstein | ![400](attachments/Participating%20Media-11.png) |
| Lorenz-Mie        | ![400](attachments/Participating%20Media-12.png) |

## Volume Rendering Equation
Integrating spatially the [](.md#^3f7db4%7Cradiative%20transport%20equation), we have the volume rendering equation

$$
\begin{aligned}
L(\mathbf{x}, \vec{\omega}) &=
\underbrace{ T_r\left(\mathbf{x}, \mathbf{x}_z\right) L\left(\mathbf{x}_z, \vec{\omega}\right) }_{ \text{Reduced(background) surface radiance} } \\
&+\underbrace{ \int_0^z T_r\left(\mathbf{x}, \mathbf{x}_t\right) \sigma_a\left(\mathbf{x}_t\right) L_e\left(\mathbf{x}_t, \vec{\omega}\right) d t }_{ \text{Accumulated emitted radiance} } \\
&+\underbrace{ \int_0^z T_r\left(\mathbf{x}, \mathbf{x}_t\right) \sigma_s\left(\mathbf{x}_t\right) \int_{S^2} f_p\left(\mathbf{x}_t, \vec{\omega}^{\prime}, \vec{\omega}\right) L_i\left(\mathbf{x}_t, \vec{\omega}^{\prime}\right) d \vec{\omega}^{\prime} d t }_{ \text{Accumulated in-scattered radiance} }
\end{aligned}
$$

| Term                                  | Explanation                                 |
| ------------------------------------- | ------------------------------------------- |
| Reduced (background) surface radiance | ![Participating Media-18](attachments/Participating%20Media-18.png) |
| Accumulated emitted radiance          | ![Participating Media-19](attachments/Participating%20Media-19.png) |
| Accumulated in-scattered radiance     | ![Participating Media-20](attachments/Participating%20Media-20.png) |

## Solving the Volume Rendering Equation

### Single scattering
This is the analog to [direct illumination](Direct%20Illumination.md) . We only consider rays either directly from light source or after just a single scattering

$$
L(\mathbf{x}, \vec{\omega})=\int_0^z T_r\left(\mathbf{x}, \mathbf{x}_t\right) \sigma_s\left(\mathbf{x}_t\right) \int_{S^2} f_p\left(\mathbf{x}_t, \vec{\omega}^{\prime}, \vec{\omega}\right) T_r\left(\mathbf{x}_t, \mathbf{x}_e\right) L_e\left(\mathbf{x}_e,-\vec{\omega}^{\prime}\right) d \vec{\omega}^{\prime} d t
$$

![600](attachments/Participating%20Media-13.png)

Analytic solution could be found if we assume:
- Homogeneous medium
- Point or spot light
- Relatively simple phase function
- No occlusion

$$
L(\mathbf{x}, \vec{\omega})=\frac{\Phi}{4 \pi} \frac{1}{4 \pi} \sigma_s \int_0^z \frac{e^{-\sigma_t\left\|\mathbf{x}-\mathbf{x}_t\right\|} e^{-\sigma_t\left\|\mathbf{x}_{\mathrm{t}}-\mathbf{x}_{\mathrm{e}}\right\|}}{\left\|\mathbf{x}_{\mathbf{t}}-\mathbf{x}_{\mathrm{e}}\right\|^2} d t
$$

Numerical solution can be obtained by *Ray-Marching*, which is essentially doing a approximation with Riemann sum

$$
L(\mathbf{x}, \vec{\omega}) \approx \sum_{i=1}^N T_r\left(\mathbf{x}, \mathbf{x}_{t, i}\right) \sigma_s\left(\mathbf{x}_{t, i}\right) L_s\left(\mathbf{x}_{t, i}, \vec{\omega}\right) \Delta t
$$

with homogeneous volume 
![Participating Media-21](attachments/Participating%20Media-21.png)

$$
T_{r}(\mathbf{x},\mathbf{x}_{t,i}) = e^{-\sigma_{t}\|\mathbf{x}-\mathbf{x}_{t,i}\|}
$$

and with heterogeneous volume
![Participating Media-22](attachments/Participating%20Media-22.png)

$$
T_{r}(\mathbf{x},\mathbf{x}_{t,i}) = T_{r}(\mathbf{x},\mathbf{x}_{t,i-1}) e^{-\sigma_{t}(\mathbf{x}_{t,i})}\Delta t
$$

### Homogeneous Media
For homogeneous media, based on the absorption coefficient, scattering coefficient, and extinction coefficient, we could determine 
- The albedo is $\alpha = \sigma_{s}/\sigma_{t}$
- The mean free path is $1/\sigma_{t}$

We could use *volumetric path tracing* to render scene containing homogeneous media. It's very similar to path tracing, except that we now have media vertex in our sampled path.

![](attachments/Participating%20Media-23.png#center%7CPath%20sampling)

#### Free Path Sampling
==Transmittance is the probability that a photon will make it beyond distance $t$:==

$$
T_{r}(t) = \frac{L_{t}}{L_{0}}
$$

In homogeneous media $T_{t}(t) = e^{ -\sigma_{t}t }$, thus the pdf of the next position is

$$
p(t)= \frac{e^{ -\sigma_{t}t }}{\int _{0}^{\infty} e^{ -\sigma_{t}s } \, ds } = \sigma_{t} e^{ -\sigma_{t}t }
$$

the cdf is 

$$
P(t) = \int _{0}^{t}\sigma_{t}e^{ -\sigma_{t}s } \, ds = 1 - e^{ -\sigma_{t}t } 
$$

and the inverted cdf is

$$
P^{-1}(\xi) = - \frac{\ln(1-\xi)}{\sigma_{t}}
$$

So we could sample the next vertex position using

$$
t = - \frac{\ln(1-\xi)}{\sigma_{t}}
$$

If we detect a surface hit before reaching $t$, we use the maximum distance $s$ and go to a surface interaction
![500](attachments/Participating%20Media-15.png)
else we make a media interaction. 

Note that at each path vertex we could use MIS sampling to get better results. 

#### Media Interaction
At a media interaction vertex, we sample the next ray by sampling the phase function. For Henyey-Greenstein, we use the inversion method:

$$
\begin{align}
\cos\theta  & = \frac{1}{2g}\left(  1 + g^{2} - \left(  \frac{1-g^{2}}{1-g+2g\xi} \right)^{2} \right) \\
\phi  & = 2\pi \zeta
\end{align}
$$

#### Surface Interaction
The surface interaction is the same as path tracing and is not explained here. 

### Heterogeneous Media
For heterogenous media, there's usually no closed form solution for the transmittance. Several other methods exist.

#### Ray Marching
Assume constant extinction(transmittance) along each step
- March until $T_{r}(\mathbf{x}_{0}, \mathbf{x}_{i}) < \xi$
- Use closed-form solution within the last segment
![](attachments/Participating%20Media-24.png#center%7CRay%20Marching)
**Issues**:
- The integral is biased since $\mathbb{E}[e^{x}]\ne^{ e^{\mathbb{E}[x]} }$
	- the exponentiation skews the noise distribution and the estimation error no longer averages to 0 in the limit
- Sensitive to resolution of heterogeneous volume
	- To keep bias low, we need to march on the order of the highest frequency (Nyquyist-Shannon theorem)

#### Delta Tracking
The general idea of *delta tracking* is to add a fictitious volume to the heterogeneous volume so that the combined volume is homogeneous. We the same [](.md#Free%20Path%20Sampling%7Cfree%20path%20sampling) as in homogeneous sampling but probabilistically reject/accept collisions based on local concentrations of real vs. fictitious volumes. 

![](attachments/Participating%20Media-25.png#center%7C*Particles%20are%20used%20only%20for%20illustration,%20the%20two%20volumes%20are%20modeled%20using%20their%20extinction%20coefficients)
The majorant extinction 

$$
\overline{\sigma}_{t} = \text{real}+ \text{fictitious extinction}
$$

The probability of accepting a collision (real collision) is 

$$
P_{r}  = \frac{\sigma_{t}(x)}{\overline{\sigma_{t}}}
$$

An explanation is given below

| Interaction                                      | Probabilistic Decsision                          |
| ------------------------------------------------ | ------------------------------------------------ |
| ![300](attachments/Participating%20Media-26.png) | ![300](attachments/Participating%20Media-27.png) |
| ![300](attachments/Participating%20Media-28.png) | ![300](attachments/Participating%20Media-29.png) |
| ![300](attachments/Participating%20Media-30.png) | ![300](attachments/Participating%20Media-31.png) |
| ![300](attachments/Participating%20Media-32.png) | ![300](attachments/Participating%20Media-33.png) |
| ![300](attachments/Participating%20Media-34.png) | ![300](attachments/Participating%20Media-35.png)                                                 |

It's easy to see that the importance of the tightness of the majorant $\overline{\sigma}_{t}$. A tighter majorant will make the process more efficient. We could use a grid, octree or kd-tree to subdivide space and precompute localized (tighter) majorants. 

Delta tracking could also be used to estimate the transmittance $T_{r}(\mathbf{x},\mathbf{y})$:
1. Sample free path from $\mathbf{x}$ toward $\mathbf{y}$
2. If a real collision occurs before $\mathbf{y}$, then $T_{r}(\mathbf{x},\mathbf{y}) = 0$
3. else 
4. To get a finer estimate, sample multiple free paths and count the relative number that made it beyond $\mathbf{y}$

### Volumetric Photon Mapping
The idea of volumetric photon mapping is the same as the [Photon Mapping](Photon%20Mapping.md) we have seen. 

#### Deposit Photons
This time we could deposit photons either on diffuse surface or in the scattering volume. The positions of the photons are exactly the path vertices we have talked about in [](.md#Free%20Path%20Sampling%7Cvolumetric%20path%20tracing).

#### Rendering
![Participating Media-36](attachments/Participating%20Media-36.png)

$$
\begin{array}{r}
L(\mathbf{x}, \vec{\omega})=\int_0^z T_r\left(\mathbf{x}, \mathbf{x}_t\right) \sigma_s\left(\mathbf{x}_t\right) L_s\left(\mathbf{x}_t, \vec{\omega}\right) d t+T_r\left(\mathbf{x}, \mathbf{x}_z\right) L\left(\mathbf{x}_z, \vec{\omega}\right) \\
L_s\left(\mathbf{x}_t, \vec{\omega}\right)=\int_{S^2} f_p\left(\mathbf{x}, \vec{\omega}^{\prime}, \vec{\omega}\right) L_i\left(\mathbf{x}_t, \vec{\omega}^{\prime}\right) d \vec{\omega}^{\prime}
\end{array}
$$

The term $L_{s}(\mathbf{x}_{t},\vec{\omega})$ could be approximated by 

$$
L_{s}(\mathbf{x}_{t},\vec{\omega}) \simeq \sum_{i=1}^{k} f_{p}(\mathbf{x}_{t}, \vec{\omega}_{i}, \vec{\omega}) \frac{\Phi_{i}(\mathbf{x}_{t}, \vec{\omega}_{i})}{\frac{4}{3}\pi r(\mathbf{x}_{t})^{3}}
$$

and $L(\mathbf{x},\vec{\omega})$ could be approximated by ray marching

$$
L(\mathbf{x}, \vec{\omega})=\sum_{i=1}^N T_r\left(\mathbf{x}, \mathbf{x}_{t, i}\right) \sigma_s\left(\mathbf{x}_{t, i}\right) L_s\left(\mathbf{x}_{t, i}, \vec{\omega}\right) \Delta t+T_r\left(\mathbf{x}, \mathbf{x}_z\right) L\left(\mathbf{x}_z, \vec{\omega}\right)
$$

However, VPM faces the problem of inefficient usage of photons [Efficient simulation of light transport in scenes with participating media using photon maps | Proceedings of the 25th annual conference on Computer graphics and interactive techniques (acm.org)](https://dl.acm.org/doi/10.1145/280814.280925)
![Participating Media-37](attachments/Participating%20Media-37.png)
which could be resolved using Beam Radiance Estimate. [The Beam Radiance Estimate for Volumetric Photon Mapping)](https://cs.dartmouth.edu/wjarosz/publications/jarosz08beam-tech.pdf)

#### Beam Radiance Estimate

![Participating Media-38](attachments/Participating%20Media-38.png)

$$
L_m(\mathbf{x}, \vec{\omega}) \approx \sum_{i=1}^M T_r\left(\mathbf{x}, \mathbf{x}_i\right) \sigma_s\left(\mathbf{x}_i\right) f_p\left(\mathbf{x}_i, \vec{\omega}_i, \vec{\omega}\right) \frac{\Phi_i}{\pi r^2}
$$

where $\mathbf{x}_{i}$ is the orthogonal projection of $i$-th photon onto the ray.

> Algorithm:
> 1. Shoot photons from light sources ![350](attachments/Participating%20Media-39.png)
> 2. Construct a balanced kD-tree for the photons ![350](attachments/Participating%20Media-40.png)
> 3. Assign a radius for each photon computed from the local density of photons ![350](attachments/Participating%20Media-41.png)
> 4. Create a bounding-box hierarchy over photon discs/spheres ![350](attachments/Participating%20Media-42.png)
> 5. Render: For each ray through the medium, accumulate all photon discs that intersect ray ![Participating Media-43](attachments/Participating%20Media-43.png)
