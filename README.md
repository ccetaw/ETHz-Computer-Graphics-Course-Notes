# ETHz-Computer-Graphics-Course-Notes
Personal notes for ETHz Computer Graphics HS2022 written in obsidian flavored but also github flavored markdown. You could also find pdf version but they might not be properly adjusted since I output them directly from my obsidian vault. Using obsidian and topaz theme is mostly recommended. 

The notes are mainly adapted from the course slides in a more organized way. Some topics that are not explained in details in the slides are extended in the notes. However not all topics are covered. Some topics are more related to computer vision and you could find them in my another repo. 

Materials and papers I used to write these notes are included in the repo and you could find the references in the notes. 

Additionally, you could find in this repo [Computer Graphics ETHz 2022 Rendering Project](https://github.com/ccetaw/ETHZ-Computer-Graphics-2022-Rendeing-Competition) the assignments of the course and all the source code. However, for you own good, please don't simply copy them to finish the assignments.

Hope you enjoy this computer graphics course!


## [Physics Basis](/notes_github/Physics%20Basis.md)
### Source of Light
#### Incandescence
#### Luminescence
### Quantifying Light(Radiometry)
#### Flux
#### Irradiance
#### Radiosity
#### Radiant density
#### Radiance

## [Shape Representation](/notes_github/Shape%20Representation.md)
### Parametric
#### Parametric Curves
#### Parametric Surfaces
#### Volumetric Density
### Implicit
#### Signed Distance Function
#### Procedural Implicits
### Discrete/Sampled
#### Point Set Surfaces
#### Polygonal Meshes

## [Appearance Modeling](/notes_github/Appearance%20Modeling.md)
### BRDF
#### Refection Equation
#### BRDF Properties
### Simple BRDF and BTDF Models
#### Lambertian
#### Ideal Specular Reflection
#### Ideal Specular Refraction

## [Ray Tracing](/notes_github/Ray%20Tracing.md)
### Ray Definition
#### Parametric Form
### Ray Generation
### Intersection
#### Intersection with Analytical Shapes
##### Spheres
##### Planes
##### Triangles
### Shading

## [Sampling](/notes_github/Sampling.md)
### Rejection Sampling
#### Disk
#### Sphere
#### Hemisphere
### Sampling Arbitrary Distributions
#### The Inversion Method
#### Sampling 2D Distributions
#### Area-Preserving Sampling
##### Uniform(Area-Preserving) Sampling of a Disk
##### Uniform(Area Preserving) Sampling a Unit Sphere/ Hemisphere/ Sphere Cap
##### Cosine-weighted Hemispherical Sampling
##### Beckmann Distribution

## [Monte Carlo Integration](/notes_github/Monte%20Carlo%20Integration.md)
### Monte Carlo Method
### Importance Sampling

## [Direct Illumination](/notes_github/Direct%20Illumination.md)
### Definition
### Formulation
#### Hemispherical Form
#### Surface Area Form
### Sampling Light Sources
#### Point Light
#### Spot Light
#### Directional Light
#### Quad/Area Light
#### Sphere Light
#### Mesh Light
### Importance Sampling
#### cos Term 
#### BRDF 
#### Incident Radiance
#### Combining Multiple Strategies
### Multiple Importance Sampling
#### Ways of Combination
##### Naïve combination of 2 sampling strategies
##### Weighted combination of 2 sampling strategies
##### Weighted combination of M sampling strategies
#### Choice of the Weights
#### One-Sample Model
### Environment Lighting

## [Path Tracing](/notes_github/Path%20Tracing.md)
### Light Paths
### Recursive Rendering Equation
### Solving the Rendering Equation
### Path Tracing
### Light Tracing
### Path Integral Framework
### Bidirectional Path Tracing

## [Photon Mapping](/notes_github/Photon%20Mapping.md)
### Photon Mapping
### Progressive Photon Mapping

## [Participating Media](/notes_github/Participating%20Media.md)
### Physics Background
#### Media
#### Phase Function
##### Isotropic Scattering
##### Henyey-Greenstein Phase Function
##### Schlick's Phase Function
##### Rayleigh Scattering
##### Lorenz-Mie Scattering
### Volume Rendering Equation
### Solving the Volume Rendering Equation
#### Single scattering
#### Homogeneous Media
#### Heterogeneous Media
##### Ray Marching
##### Delta Tracking
#### Volumetric Photon Mapping

## [Advanced Camera Models](/notes_github/Advanced%20Camera%20Models.md)
### Optics Recap
#### Paraxial Focusing
#### Lens
#### Aperture
#### Depth of Field
### Thin Lens
### Motion Blur
### Chromatic Aberration
### Typical Color Imaging Pipeline

## [Image-Based Rendering](/notes_github/Image-Based%20Rendering.md)
### Rendering with No Geometry
#### Plenoptic Modeling
#### Light Field Rendering
#### Unstructured Lumigraph
### Rendering with Implicit Geometry
#### View Interpolation
#### View Morphing
### Rendering with Explicit Geometry
#### Depth-based Rendering
#### View-Dependent Texture Mapping

## [Low Discrepancy Sampling](/notes_github/Low%20Discrepancy%20Sampling.md)
### Sampling and Reconstruction Theory
#### Ideal Sampling and Reconstruction
#### Aliasing
#### Source of Aliasing
#### Anti-aliasing
#### Evaluating Sampling Patterns: Discrepancy
### Low Discrepancy Sampling Methods
#### Stratified(Jittered) Sampling
#### Latin Hypercube(N-Rooks) Sampling
#### Multi-jittered Sampling
#### Hammersley and Halton Sequences
#### (0,2)-Sequences

## [Denoising](/notes_github/Denoising.md)
### Image-Based Denoising
#### Bilateral Filter
#### NL-Means
### [Adaptive Rendering with Non-Local Means Filtering](https://www.cs.umd.edu/~zwicker/publications/AdaptiveRenderingNLM-SIGA12.pdf)
### [Robust Denoising using Feature and Color Information](https://www.cs.umd.edu/~zwicker/publications/RDFC-CGF13.pdf)
### Data Driven Denoising

## [Inverse Rendering](/notes_github/Inverse%20Rendering.md)
### Photometric Stereo
#### Automatic Differentiation
### Volume Optimization
### BRDF Measurement
### Colorimetric Calibration
### Lighting Estimation

## [Accelerating Data Structures](/notes_github/Accelerating%20Data%20Structures.md)
### Spatial Decomposition
#### Uniform Grid
#### KD-Trees
#### Octrees
#### General BSP(Binary Space Partition) trees
### Object Decomposition
#### Bounding Volume Hierarchies
##### Ray-AABB Intersection