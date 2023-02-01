---
tags:
- CG
date: 27/09/2022
---

# Shape Representation
We live in a 3D space and all objects are 3-dimensional. In computer graphics, 3D objects are modeled in different ways. Here below is a table containing existing models of 3D objects.

| Parametric                            | Implicit                                                        | Discrete/Sampled                   |
| ------------------------------------- | --------------------------------------------------------------- | ---------------------------------- |
| - Splines <br> - [Subdivision surface - Wikipedia](https://en.wikipedia.org/wiki/Subdivision_surface) | - Metaballs/Blobs <br> - Distance fields <br> - Procedural, CSG | - Meshes <br> - Point set surfaces |
| ![200](attachments/Shape%20Representation.png)    | ![200](attachments/Shape%20Representation-1.png)                            | ![200](attachments/Shape%20Representation-2.png)    |


## Parametric
Parametric description of a shape is a mapping from $X\subseteq \mathbb{R}^{m}$ to $Y \subseteq \mathbb{R}^{n}$. We are able to query any point on the shape by choosing a point in $X$. 

### Parametric Curves
Consider a function $s: X \to Y, X \subseteq \mathbb{R}^{m}, Y\subseteq \mathbb{R}^{n}$:
- Planar curve: $m=1, n=2$


$$
s(t) = (x(t), y(t))
$$


- Space curve: $m=1, n=3$,


$$
s(t) = (x(t), y(t), z(t))
$$


#### Examples
- Circle


$$
\begin{align}
\mathbf{p}(t) &: \mathbb{R} \to \mathbb{R}^{2}  \\
\mathbf{p}(t) &= r(\cos (t), \sin (t))
\end{align}
$$

- [Bézier curve - Wikipedia](https://en.wikipedia.org/wiki/B%C3%A9zier_curve)


$$
s(t) = \sum_{i=0}^{n} \mathbf{p}_{i} B_{i}^{n}(t)
$$

where $B_{i}^{n}=\begin{pmatrix}n \\ i\end{pmatrix}t^{i} (1-t)^{n-i}$ are basis functions.
![Bézier_3_big](attachments/B%C3%A9zier_3_big.gif)

### Parametric Surfaces
Surface: $m=2, n=3$

$$
s(u,v) = (x(u,v), y(u,v), z(u,v))
$$

![400](attachments/Shape%20Representation-3.png)

#### Examples
- Sphere


$$
\begin{aligned}
& s(u, v)=r(\cos (u) \cos (v), \sin (u) \cos (v), \sin (v)) \\
& (u, v) \in[0,2 \pi) \times[-\pi / 2, \pi / 2]
\end{aligned}
$$

- [Bézier surface - Wikipedia](https://en.wikipedia.org/wiki/B%C3%A9zier_surface)


$$
s(u, v)=\sum_{i=0}^m \sum_{j=0}^n \mathbf{p}_{i, j} B_i^m(u) B_j^n(v)
$$

![400](Shape%20Representation.png)

#### Tangents and Normal
The tangents and normal at a give point on the surface are 

$$
\begin{aligned}
& s_u=\frac{\partial s(u, v)}{\partial u} \\
& s_v=\frac{\partial s(u, v)}{\partial v} \\
& \mathbf{n}=\frac{s_u \times s_v}{\left\|s_u \times s_v\right\|}
\end{aligned}
$$

Please refer to any calculus book for proof. 

**Pros**:  

- Easy to generate points on the curve/surface
- Analytic formulas for derivatives

**Cons**:

- Hard to determine inside/outside
- Hard to determine if a point is on the curve/surface

### Volumetric Density
This function gives the density of any point in the space: $m=3, n=1$:
![Shape Representation-4](attachments/Shape%20Representation-4.png)
You can not really say this representation is really a shape, but it's really useful later in volumetric rendering([Participating Media](Participating%20Media.md)).

**Pros**:  

- Easy to generate points on the curve/surface
- Analytic formulas for derivatives

**Cons**:  

- Hard to determine inside/outside
- Hard to determine if a point is on the curve/surface


## Implicit 

### [Signed Distance Function](https://en.wikipedia.org/wiki/Signed_distance_function)
A surface is represented as the kernel/zero level set of a scalar function $f: \mathbb{R}^{m} \to \mathbb{R}$
- Curve in 2D:      $S = \{ x \in \mathbb{R}^{2}| f(x) = 0\}$
- Surface in 3D:   $S = \{ x \in \mathbb{R}^{3} | f(x) = 0\}$

![Shape Representation-6](attachments/Shape%20Representation-6.png)

The normal vector to the surface(curve) is given by the gradient of the implicit function

$$
\nabla f(x, y, z)=\left(\frac{\partial f}{\partial x}, \frac{\partial f}{\partial y}, \frac{\partial f}{\partial z}\right)^\top
$$

#### Boolean Set Operations
- Union


$$
 \bigcup_{i}  f_{i}(x) = \min f_{i}(x)
$$

- Intersection


$$
\bigcap_i f_i(x)=\max f_i(x)
$$

- Subtraction


$$
h = \max (f, -g)
$$

![300](attachments/Shape%20Representation-7.png)

![300](attachments/Shape%20Representation-5.png)

#### Smooth Set Operations
In many cases, smooth blending is desired [(PDF) Blending Operations for the Functionally Based Constructive Geometry (researchgate.net)](https://www.researchgate.net/publication/2256770_Blending_Operations_for_the_Functionally_Based_Constructive_Geometry)  

$$
\begin{aligned}
& f \cup g=\frac{1}{1+\alpha}\left(f+g-\sqrt{f^2+g^2-2 \alpha f g}\right) \\
& f \cap g=\frac{1}{1+\alpha}\left(f+g+\sqrt{f^2+g^2-2 \alpha f g}\right)
\end{aligned}
$$

![400](attachments/Shape%20Representation-8.png)

For $\alpha =1$, this is equivalent to $\min$ and $\max$:

$$
\begin{aligned}
& \lim _{\alpha \rightarrow 1} f \cup g=\frac{1}{2}\left(f+g-\sqrt{(f-g)^2}\right)=\frac{f+g}{2}-\frac{|f-g|}{2}=\min (f, g) \\
& \lim _{\alpha \rightarrow 1} f \cap g=\frac{1}{2}\left(f+g+\sqrt{(f-g)^2}\right)=\frac{f+g}{2}+\frac{|f-g|}{2}=\max (f, g)
\end{aligned}
$$

#### Metaballs / Blobs
An analytical sphere in 3D can be implicitly represented by 

$$
f(\mathbf{p}) = \|\mathbf{p}\|^{2} - r^{2}
$$

which is equivalent to 

$$
f(\mathbf{p}) = e^{ -\|\mathbf{p}\|^{2}/r^{2} }
$$

at $e^{-1}$. 

With smooth fall-off functions, adding implicit functions generates a blend:

$$
f(\mathbf{p})=e^{-\left\|\mathbf{p}-\mathbf{p}_1\right\|^2}+e^{-\left\|\mathbf{p}-\mathbf{p}_2\right\|^2}
$$

![Shape Representation-10](attachments/Shape%20Representation-10.png)

==Set operations by simple addition/subtraction==.

### Procedural Implicits
CSG ([Constructive solid geometry - Wikipedia](https://en.wikipedia.org/wiki/Constructive_solid_geometry))

![400](attachments/Shape%20Representation-9.png)

**Pros**:

- Easy to determine inside/outside
- Easy to determine if a point is on the curve/surface

**Cons**:

- Hard to generate points on the curve/surface
- Does not lend itself to (real-time) rendering

## Discrete/Sampled

### Point Set Surfaces
This notion is not explained in details in the lecture for now. Please refer to [points_set_vis01.pdf (tau.ac.il)](http://www.cs.tau.ac.il/~dcor/online_papers/papers/points_set_vis01.pdf)

### Polygonal Meshes
Polygonal Meshes are the most used representations in CG. It’s a boundary representation of an object
- Piecewise linear approximation
	- Error is $O(h^{2})$ where $h$ is the sampling difference.
- Arbitrary topology
- Piecewise smooth surfaces
- Efficient rendering

#### Polygon

![300](attachments/Shape%20Representation-11.png)

- vertices: $v_{0},v_{1},\dots$
- edges: $(v_{0},v_{1}),\dots, (v_{n-2}, v_{n-1})$
- closed: $v_{0}=v_{n-1}$
- Planar: all vertices on a plane
- Simple: not self-intersecting

#### Polygonal mesh

![300](attachments/Shape%20Representation-12.png)

- A finite set $M$ of closed simple polygons $Q_{i}$ is a polygonal mesh.
- The intersection of two polygons in $M$ is either empty, a vertex or and edge.
$$
M = <\underset{ \text{vertices} }{ V }, \underset{ \text{edges} }{ E }, \underset{ \text{faces} }{ F }>
$$
- Every edge belongs to at least one polygon.
- Each $Q_{i}$ defines a face of the polygonal mesh

**Vertex degree or valence**: number of incident edges
**Boundary**: the set of all edges that belong to only on polygon:
**Triangle meshes**:
- connectivity: vertices, edges, triangles
- geometry: vertex positions
- cannot easily remove triangles(create holes) 

#### Manifold
A surface is a closed 2-manifold if it is everywhere locally homeomorphic to a disk
- at most 2 faces sharing an edge
	- Boundary edges have on incident face
	- Inner edges have two incident faces
- If closed and not intersecting, a manifold divides the space into inside and outside(watertight)
- A closed manifold polygonal mesh is called polyhedron 

A polygonal mesh is manifold if
- every vertex has 1 connected ring or half ring of faces around it
- every edge is shared by at most faces

#### Global topology of meshes
- Genus: $\frac{1}{2}\times$ the maximal number of closed paths that do not disconnect the graph
	- Informally, the number of handles (donut holes)
![Shape Representation-13](attachments/Shape%20Representation-13.png)

**Euler-Poincaré Formula**
The sum

$$
\chi(M)=v-e+f
$$

is *constant* for a a given surface topology, no matter which (manifold) mesh we choose
– $v$ = number of vertices 
– $e$ = number of edges 
– $f$ = number of faces

#### Triangulation
Polygonal mesh where every face is a triangle
- Simplifies data structures
- Simplifies algorithms
- Simplifies rendering
- Any polygon can be triangulated

