---
tags:
- CG
date: 11/11/2022
---

# Advanced Camera Models
## Optics Recap
### Paraxial Focusing
![Advanced Camera Models-7](attachments/Advanced%20Camera%20Models-7.png)  

Snell's law of refraction states that:
$$n\sin i = n'\sin i'$$
With paraxial approximation:
$$ni \simeq n'i'$$
which leads to
$$\frac{n}{z} + \frac{n}{r} \simeq \frac{n'}{r} -\frac{n'}{z'}$$

### Lens

![Advanced Camera Models-5](attachments/Advanced%20Camera%20Models-5.png)  

Consider 2 spherical interfaces air-glass-air with thin lens assumption: $d\to 0$.

$$\frac{1}{s_{o}}+\frac{1}{s-i} = (n-1)\left( \frac{1}{R_{1}}-\frac{1}{R_{2}} \right)$$

By Gaussian lens formula, the focal length is defined to satisfy
$$\frac{1}{s_{o}}+\frac{1}{s_{i}} = \frac{1}{f}$$

Move sensor relatively to the lens to focus on different distances. At $s_{o} = s_{i}=2f$, we have 1:1 imaging. And it's possible to focus on objects closer than focal length.

The power of a lens is defined as 
$$P= \frac{1}{f}$$
Combining lenses would be 
$$\frac{1}{f_{\text{total}}} = \frac{1}{f_{1}}+\frac{1}{f_{2}}$$
and 
$$P_{\text{total}} = P_{1} + P_{2}$$

### Aperture
![Advanced Camera Models-6](attachments/Advanced%20Camera%20Models-6.png)
Aperture is referred to the lens diaphragm opening inside a photographic lens.
It regulates the amount of light that passes through onto the sensor.

[Aperture number](https://en.wikipedia.org/wiki/F-number), aka f-number is 

$$N = \frac{f}{D}$$

where $f$ is the focal length and $D$ is the diameter of the entrance pupil(effective aperture).

### Depth of Field
Depth of field is the distance between the nearest and the farthest objects that are in acceptably sharp focus
$$
\text{DoF} = \frac{2\mu^{2}Nc}{f^{2}}
$$
where
- $c$ the given circle of confusion
- $f$ the focal length
- $N$ the $F$-number
- $u$ the distance to subject

## Thin Lens
![Advanced Camera Models-9](attachments/Advanced%20Camera%20Models-9.png)  

A thin lens camera is specified by its aperture diameter and focal length. ![Advanced Camera Models-10](attachments/Advanced%20Camera%20Models-10.png)  

Instead of generate a ray originated at the camera center, we sample a point on the lens and connect it to the point on the plane of focus where the ray without lens will hit. 

We could also have non-disk aperture, for example, a heart or polygon shape to have beautiful bokeh.

## Motion Blur
![Advanced Camera Models-8](attachments/Advanced%20Camera%20Models-8.png)  

When rays are generated, we give it a random time and integrate over time. The motion could be modeled either by moving camera or moving objects. It's easier to move the camera because the timeline of the ray is easily tracked. 

## Chromatic Aberration
To mimic real-life imperfections in the lens system, the thinlens camera also supports a heuristic to produce chromatic aberration in the image.

![Advanced Camera Models-12](attachments/Advanced%20Camera%20Models-12.png)

Essentially, ==the aberration effect works by considering three apertures instead of just one: A red, a green and a blue aperture.== In the center of the image, the apertures are perfectly on top of each other and together produce a white aperture. Towards the borders of the image however, the apertures shift with respect to each other. Where they overlap, the aperture is still white, but at the borders of the aperture kernel the r/g/b apertures separate and produce colored fringing. This is of course very approximate, but it mimics the coarse behavior of real-life chromatic aberration.
![](attachments/Advanced%20Camera%20Models-11.png#center%7CIllustration%20of%20chromatic%20aperture%20shift)

For sampling, a random aperture is selected and sampled to produce the ray. The ray throughput is then computed by evaluating all three apertures at the sampled aperture point. If there is little aberration, rays will still have a monochromatic throughput of 1. If there is a lot of aberration however, more rays will be generated that only have non-zero throughput in one of the color channels. This produces colored noise and leads to longer render times, and large aberration is generally discouraged.

## Typical Color Imaging Pipeline
![Advanced Camera Models](attachments/Advanced%20Camera%20Models.png)

### 1. The Raw Image
This is the unprocessed image produced by the sensor which is still in its [Bayer pattern format](https://en.wikipedia.org/wiki/Bayer_filter)

Camera RGB sensitivity is sensor specific
![Advanced Camera Models-1](attachments/Advanced%20Camera%20Models-1.png)
Raw-RGB represents the physical world's spectral power distributions "projected" onto the sensor's spectral filters.

### 2. ISO Gain and Raw Image Processing
The sensor signal is amplified and digitized. In the camera the ISO setting specifies the gain to signal.

Other raw image processing tasks are also executed:
- Black light subtraction
- Defective pixel mask
- Lens filed correction ![Advanced Camera Models-2](attachments/Advanced%20Camera%20Models-2.png)
	- Provide a spatially varying correction
	- Compensate lens distortion
	- Correct uneven light fall

### 3. RGB Demosaicing
A Bayer filter mosaic is a color filter applied on the sensor of cameras. The objective of demoisaicing is to produce a full RGB image from the incomplete color samples.
![Advanced Camera Models-3](attachments/Advanced%20Camera%20Models-3.png)
The filtering is done by linear interpolation:
![Advanced Camera Models-4](attachments/Advanced%20Camera%20Models-4.png)

### 4. Noise Reduction
All sensors have noise. The smaller the sensor the more aggressive the noise reduction. There are many different strategies but not explained here.

### 5. White balance and color transform
The objective is to converts the sensor raw-RGB values to a device independent color space. 

#### White Balance
The ability of the human visual system to adapt to scene illumination is called color constancy or chromatic adaptation. Image sensors, however, don't have this ability. There are several solutions;
 - User can manually set the white balance
 - Camera specific white-balance matrices for common illuminations
 - Auto white balance: often refer to AWB as "illumination estimation"

#### Color Transform
Use a device independent color space such as CIE XYZ. For electronic devices
- compute mappings of their device specific values to the corresponding CIE XYZ values
- a canonical space to match between devices

### 6. Color Manipulation
Cameras apply this “manipulation" to make the images look good. It is also possible to select various photo-finishing styles. Smartphones often compute this per-image and may even take into account geographical region,

### 7. sRGB Mapping
sRGB is a color space defined to be used by printers and monitors. It assumes
- A certain viewing condition
- Gamma correction

## Reference
[Final Project Report – Benedikt Bitterli (noobody.org)](http://noobody.org/is-report/simple.html)