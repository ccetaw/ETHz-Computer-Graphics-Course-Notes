---
tags:
- CG
date: 13/10/2022
---

# Ray Tracing
## Ray Definition
Ray is an ideal model for light transportation with the following assumptions:
- Ray is geometric:
	- no diffraction, no polarization, no interference
- Ray travels in a straight line in a vacuum
	- no atmospheric scattering
	- no gravity effects
- Discrete-wavelength approximation
	- RGB
	- quantized approximation of certain effects (dispersion, fluorescence)
- Superposition
	- no non-linear reflecting materials

### Parametric Form
Ray can be expressed in parametric form 
$$
\underbrace{ \mathbf{r}(t) }_{ \text{ray} } = \underbrace{ \mathbf{o} }_{ \text{origin} } + t\underbrace{ \mathbf{d} }_{ \text{direction} }
$$
![[RT_1.png]]

### General Theory
| Type           | Presentation  |
| -------------- | ------------- |
| Light tracing  | ![[RT_2.png]] |
| Camera tracing | ![[RT_3.png]] |
We see the world because we see the light. To simulate the light transporting in the world especially those who arrive at the camera, we need an efficient way to model this. Instead of modeling all lights emitted from light sources, we only simulate those ==can== arrive at our camera. ==Because light path is inversible==, it absolutely legal to do that.

The general ray tracing pipeline is as follows
![[Ray Tracing.png]]

## Ray Generation
To project 3D objects onto a 2D image, we use [[Pinhole Camera|pinhole camera model]] or other [[Advanced Camera Models]]. A ray(or multiple rays) is generated through the image pixels. 

## Intersection
The next step we need to decide whether the ray intersects with other objects. We distinguish two cases: intersections with analytical shapes and intersections with meshes.

### Intersection with Analytical Shapes
Analytical shapes are defined by implicit surface. Usually we can derive the intersections from solving equations.
#### Spheres

![[RT_4.png]]
The implicit sphere equations is 
$$
\|\mathbf{x}-\underbrace{ \mathbf{c} }_{ \text{center} }\|^{2} = \underbrace{ r^{2} }_{ \text{radius} }
$$
Plug in the ray definition 
$$
\|\mathbf{o}+t\mathbf{d}-\mathbf{c}\|^{2} - r^{2} = 0
$$
which reduces to a quadratic equation on $t$
$$
At^{2} + Bt + C = 0
$$
where 
$$
\begin{align}
A & = \|\mathbf{d}\|^{2}  \\
B & = 2(\mathbf{o}-\mathbf{c})\cdot \mathbf{d}^{\top} \\
C & = (\mathbf{o}-\mathbf{c})^{2} - r^{2} \\
\Delta & = B^{2}-4AC
\end{align}
$$
We have the following cases
1. $\Delta < 0$: there's no intersection
2. $\Delta>0$: there exist intersections, solve for $t$. $t=t_{1}$ and $t=t_{2}$ with $t_{1} < t_{2}$:
	1. $t_{1}\in[t_{min},t_{max}]$: intersection point at $t_{1}$
	2. $t_{1}\notin [t_{min},t_{max}]$ and $t_{2}\in[t_{min},t_{max}]$: intersection point at $t_{2}$
	3. $t_{1}\notin [t_{min},t_{max}]$ and $t_{2}\notin[t_{min},t_{max}]$: no intersection

#### Planes

![[RT_5.png|300]]
Implicit plane equation:
$$
ax+by+cz+d = 0 \iff (\mathbf{x}-\underbrace{ \mathbf{p} }_{ \text{point on plane} })\cdot \underbrace{ \mathbf{n} }_{ \text{plane normal} } = 0
$$
Plug in ray definition we have 
$$
t = -\frac{(\mathbf{o}-\mathbf{p})\cdot \mathbf{n}}{\mathbf{d} \cdot \mathbf{n}}
$$

#### Triangles

![[RT_6.png]]
Barycentric coordinates:
$$
\mathbf{x} = s_{1}\mathbf{p}_{1} + s_{2}\mathbf{p}_{2}+s_{3}\mathbf{p}_{3}
$$
Point $\mathbf{x}$ is inside the triangle if 
$$
s_{1}+s_{2}+s_{3} = 1, \quad 0\leq s_{i} \leq 1
$$
Two way to calculate intersection:
1. Plug in the ray definition
   $$
\mathbf{o} + t\mathbf{d} = s_{1}\mathbf{p}_{1}+s_{2}\mathbf{p}_{2}+s_{3}\mathbf{p}_{3}
$$
2. Intersection with plane and test if inside the triangle

#### More
Find guidance on intersection on [Object/Object Intersection (realtimerendering.com)](http://www.realtimerendering.com/intersections.html)

## Shading
Shading is the process of determining color of primary ray (and thus corresponding pixel color) depending on hit point. 

Once an intersection is found, we need to evaluate the light-material interaction, i.e. what's explained in [[Appearance Modeling]]. Different shading schemes will be discussed later, for example, [[Direct Illumination]] and [[Path Tracing]]. The main problem is to determine the integral
$$
L_{r}(\mathbf{x}, \mathbf{w}_{r}) = \int _{H^{2}} f_{r}(\mathbf{x}, \mathbf{w}_{i}, \mathbf{w}_{r})L_{i}(\mathbf{x},\mathbf{w}_{i})\cos\theta_{i}  \, d\mathbf{w}_{i} 
$$
which will be illustrated in [[Monte Carlo Integration]].

**Precision Issues**
![[attachments/Ray Tracing-1.png]]
To solve the precision issues in intersection, we can add an intersection tolerance $\epsilon=1e-6$ to $t_{\text{min}}$ of the ray for example. 