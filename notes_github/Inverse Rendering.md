---
tags:
- CG
date: 26/01/2023
---

# Inverse Rendering

Inverse rendering problems could be divided into three types. 
- Given geometry, reflectance, and the recorded photograph, solves for the lighting in the scene
	- Inverse lighting
	- Re-lighting 
- Given geometry, lighting and photograph, solves for
	- texture
	- BRDF
- Given lighting, photograph, solves for
	- normals
	- depth map
	- reflectance

## Photometric Stereo
We want to measure the surface normal of an object in real life using the direct illumination rendering equation we have learned. 

$$
L_{o}(\mathbf{x},\vec{\omega}_{o}) = \int _{H^{2}}f_{r}(\mathbf{x},\vec{\omega}_{i},\vec{\omega}_{o})L_{i}(\mathbf{x},\vec{\omega}_{i})(\vec{\omega}_{i}\cdot \vec{n}) \, d\vec{\omega}_{i}
$$

Assume that we are using active illumination, which means:
- Incident radiance is known and controllable
- Indirect illumination is considered negligible
- Directional light is used
- Incident direction and radiance are controlled to probe properties of the material
thus we have 

$$L_{i}(\mathbf{x}, \vec{\omega}_{i}) = L_{i}(\vec{\omega}_{i})$$

Assume also 
- The directional light is delta $$L_{i}(\vec{\omega}_{i}) = \delta(\vec{\omega}_{i})$$
- The BRDF is Lambertian $$f_{r} = \frac{\rho_{d}}{\pi}$$
- We are using orthographic camera
- We have a single view of the object

Then the rendering equation reduces to 

$$
I = \frac{\rho_{d}}{\pi}(\vec{n}\cdot \vec{\omega}_{i})
$$


which is a linear system equivalent to 
$$
A_{n\times 3}\mathbf{x}_{3\times 1} = \mathbf{b}_{n\times 1}
$$

where

$$
A_{n\times 3} = \begin{bmatrix}
\vec{\omega}_{i,1}^{\top} \\
\vdots \\
\vec{\omega}_{i,n}^{\top}
\end{bmatrix}
,\quad
\mathbf{b} = \begin{bmatrix}
I_{1} \\
\vdots \\
I_{n}
\end{bmatrix}
, \quad
\mathbf{x} = \frac{\rho_{d}}{\pi}\vec{n}
$$

We need at least $3$ lighting conditions to solve the system because we have $3$ unknowns: $\vec{n}=(\theta,\phi)$ and $\rho_{d}$. The solution is thus

$$
\vec{n} = \frac{\mathbf{x}}{\|\mathbf{x}\|}, \quad \rho _{d} = \pi \|\mathbf{x}\|
$$

Suppose now we don't assume Lambertian reflectance anymore, but use [](Microfacet%20Theory.md#Blinn-Phong%7CBlinn-Phong%20model) instead, i.e.

$$
f_{r}(\boldsymbol{\omega}_{o}, \boldsymbol{\omega}_{i}) = \frac{e+2}{2\pi}(\boldsymbol{\omega}_{h}\cdot \mathbf{n})^{e}
$$

with 

$$
\boldsymbol{\omega}_{h} = \frac{\boldsymbol{\omega}_{i}+\boldsymbol{\omega}_{o}}{\|\boldsymbol{\omega}_{i} + \boldsymbol{\omega}_{o}\|}
$$

The system is then no longer linear w.r.t. $\vec{n}$ and we have no closed-form solution. However, we could still use gradient descent to find a minimal. 
![Inverse Rendering](attachments/Inverse%20Rendering.png)

Taking the derivative of $L_{o}$ w.r.t. $\vec{n}$, we could see the system is extremely complicated 

$$
\frac{\partial L_o}{\partial \vec{n}}=\int_{H^2} \frac{e+2}{2 \pi} L_i\left(\vec{\omega}_i\right)\left(e\left(\vec{\omega}_h \cdot \vec{n}\right)^{e-1} \vec{\omega}_h\left(\vec{\omega}_i \cdot \vec{n}\right)+\left(\vec{\omega}_h \cdot \vec{n}\right)^e \vec{\omega}_i\right) d \vec{\omega}_i
$$

We could still make it by 
- symbolic differentiation
- finite differences
- automatic differentiation

### Automatic Differentiation

| Mode     | Representation                                                                              | Formula                                                                                                                                                                                                                                                                                                                                                              | Pros/ Cons                                                                                                                                            |
| -------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| Forward  | ![150](attachments/Inverse%20Rendering-1.png) ![200](attachments/Inverse%20Rendering-2.png) | $$\begin{aligned}& \tilde{a}=\frac{\mathrm{d} a}{\mathrm{~d} a}=1 \\& \tilde{b}=\frac{\mathrm{d} b}{\mathrm{~d} a}=\frac{\partial f(a)}{\partial a} \tilde{a} \\& \tilde{c}=\frac{\mathrm{d} c}{\mathrm{~d} a}=\frac{\partial g(b)}{\partial b} \tilde{b} \\& \tilde{d}=\frac{\mathrm{d} d}{\mathrm{~d} a}=\frac{\partial h(c)}{\partial c} \tilde{c}\end{aligned}$$ | - Easy to implement <br> - Supports multiple outputs ![250](attachments/Inverse%20Rendering-6.png)<br> - Does not supports multiple inputs: expensive |
| Backward | ![150](attachments/Inverse%20Rendering-4.png) ![200](attachments/Inverse%20Rendering-5.png) | $$\begin{aligned}& \hat{d}=\frac{\mathrm{d} d}{\mathrm{~d} d}=1 \\& \hat{c}=\frac{\mathrm{d} d}{\mathrm{~d} c}=\hat{d} \frac{\partial h(c)}{\partial c} \\& \hat{b}=\frac{\mathrm{d} d}{\mathrm{~d} b}=\hat{c} \frac{\partial g(b)}{\partial b} \\& \hat{a}=\frac{\mathrm{d} d}{\mathrm{~d} a}=\hat{b} \frac{\partial f(a)}{\partial a}\end{aligned}$$               | - Supports multiple inputs ![250](attachments/Inverse%20Rendering-7.png) <br> - Supports only a single output ![250](attachments/Inverse%20Rendering-8.png) <br> - Store the whole graph and meta-information per node                                                                                                                                                    |

## Volume Optimization
Recall that the [](Participating%20Media.md#Media%7Ctransmittance) of an volume is given by
 
$$
T_{r}(\mathbf{x},\mathbf{x}_{z}) = e^{ -\int _{\mathbf{x}}^{\mathbf{x}_{z}}\sigma(\mathbf{x}) \, d\mathbf{x}  } = \frac{L_{\text{gt}}(\mathbf{x},\omega)}{L_{o}(\mathbf{x}_{z},\mathbf{\omega})}
$$
which could be rearranged to 

$$
\int_{\mathbf{x}}^{\mathbf{x}_z} \sigma(x) d x=\underbrace{-\log \left(L_{\mathrm{gt}}(\mathbf{x}, \omega) / L_o\left(\mathbf{x}_z, \omega\right)\right)}_{=: b}
$$

The left side could be replaced by an numerical approximation (ray marching) which gives a linear system

$$
W_{\#\text{pixels}\times\#\text{voxels}}\cdot\sigma_{\#\text{voxels}} = b_{\#\text{pixels}}
$$

![Inverse Rendering-18](attachments/Inverse%20Rendering-18.png)

## BRDF Measurement
We could use a gonioreflectometer to measure a material's BRDF

| Gonioreflectometer                       | EPFL RGL                                  | MERL Database Setup |
| ---------------------------------------- | ----------------------------------------- | ------------------- |
| ![200](attachments/Inverse%20Rendering-9.png) | ![200](attachments/Inverse%20Rendering-10.png) | ![200](attachments/Inverse%20Rendering-11.png)                    |

## Colorimetric Calibration
### Color Spaces

| Color Space  | Spectrum | Formula |
| ------------ | -------- | ------- |
| CIE 1931 XYZ <br> - Y: Luminance <br> - XZ plane: Chromaticity | ![Inverse Rendering-12](attachments/Inverse%20Rendering-12.png)         | $$\begin{aligned}X & =\int_\lambda L_{e, \lambda}(\lambda) \bar{x}(\lambda) d \lambda \\Y & =\int_\lambda L_{e, \lambda}(\lambda) \bar{y}(\lambda) d \lambda \\Z & =\int_\lambda L_{e, \lambda}(\lambda) \bar{z}(\lambda) d \lambda\end{aligned}$$        |
| CIE 1931 RGB | ![Inverse Rendering-13](attachments/Inverse%20Rendering-13.png)         | $$\begin{aligned}R & =\int_\lambda L_{e, \lambda}(\lambda) \bar{r}(\lambda) d \lambda \\G & =\int_\lambda L_{e, \lambda}(\lambda) \bar{g}(\lambda) d \lambda \\B & =\int_\lambda L_{e, \lambda}(\lambda) \bar{b}(\lambda) d \lambda\end{aligned}$$        |

### Calibration
![Inverse Rendering-14](attachments/Inverse%20Rendering-14.png)
Like camera calibration, we could build a linear system based on pixel correspondences:

$$
A_{24\times 3}\mathbf{X}_{3\times 3} = \mathbf{b}_{24 \times 3}
$$

The transformation is then

$$
C_{RGB'2XYZ} = \mathbf{X}^{-1}
$$

In plus, any RGB color space may be obtained by a simple(known) linear transformation:

$$
C_{RGB'2sRGB} = C_{RGB'2XYZ}\cdot C_{XYZ_{2}sRGB}
$$

where the matrices could be found at [RGB/XYZ Matrices (brucelindbloom.com)](http://brucelindbloom.com/index.html?Eqn_RGB_XYZ_Matrix.html)

## Lighting Estimation
![](attachments/Inverse%20Rendering-15.png#center%7CPhoto%20of%20mirror%20sphere%20placed%20in%20capture%20volume)
Consider a specular perfect sphere under a spotlight and we want to estimate the incidence ray direction. Assume that we are using orthographic camera and 

$$
\vec{\Omega}_{o} = (0,0,1)^{\top}
$$

By the [](Appearance%20Modeling.md#Ideal%20Specular%20Reflection%7Crule%20of%20reflection),

$$
\vec{\omega}_{i} = 2(\vec{\omega}_{o}\cdot \vec{n})\vec{n} - \vec{\omega}
$$

and the sphere normals by analytical sphere
![Inverse Rendering-16](attachments/Inverse%20Rendering-16.png)
we could have a reflection map. Besides we could use this map to recover the environment map
![Inverse Rendering-17](attachments/Inverse%20Rendering-17.png)

## Reference
[Physics-Based Differentiable Rendering: A Comprehensive Introduction (shuangz.com)](https://shuangz.com/courses/pbdr-course-sg20/downloads/pbdr-course-sg20-notes.pdf)
[Inverse Rendering for Computer Graphics (cornell.edu)](https://www.graphics.cornell.edu/pubs/1998/Mar98.pdf)
[Acquiring Material Models Using Inverse Rendering](https://cseweb.ucsd.edu/~ravir/39.pdf)

