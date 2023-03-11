# Advanced Metal and Brushed Metal Materials
## Advanced Metal
![Screenshot_3](https://user-images.githubusercontent.com/36862146/224475242-3a67df70-f97b-4d4f-b82e-a9b6377105e5.png)

## Steps
### Unreal Material
- Using Rock maps from the Start Content folder. Fresnel, Frac, and Rainbow texture create an iridescent effect, which is combined with a Normal Map

![Screenshot_1](https://user-images.githubusercontent.com/36862146/224475243-1a1b4ba3-ae79-431e-9158-c5d0f81d610a.png)

- You can find metal RGBs in the attached file. Remapping AO to 0.5:1 for a subtle, specular occlusion, effect

![Screenshot_2](https://user-images.githubusercontent.com/36862146/224475241-d71f43b2-a329-4003-ba33-d53daf284097.png)

## Brushed Metal
![Screenshot_2](https://user-images.githubusercontent.com/36862146/224481139-552c21e7-274d-49f7-a430-f01df6dfc3ff.png)

### Description
Brushed metals or other materials that have microscopic threads on the surface (vinyl, CD disk, knobs, water waves, hair, etc) have Anisotropic qualities. This means that
they alter the way the light reflects off of them - give it a direction. Waves on water stretch the sun perpendicular to the waves' position, circular threads on 
a vinyl record stretch the light perpendicular to its flow, ie towards the center, light reflects across the flow of hair. The way to give materials these properties 
is via Anisotropy and Tangent Shader inputs (https://youtu.be/6Q_c0zGDl_4). Works on Engine Scaalbility High+
## Steps
### Unreal Material
![Screenshot_1](https://user-images.githubusercontent.com/36862146/224481137-b387fc83-e6f2-4a6a-a644-a5764bc61b42.png)

- Anisotropy operates in -1:1 range. 1 would be along an axis, -1 would be perpendicular an axis. By default, Anisotropy stretches (+1) along R/X of the UV (-1 along G/Y)
- We can alter that with the Tangent socket. TangentX and TangentY represent U and V directions here. Meaning that X and Y here direct the flow, but not the strength (that is Anisotropy value). 
So 0.1:0 and 1:0 would give the same result - stretch along the X. 1:1 would make it a diagonal. 0:0 will give you incorrect results as it'd an Isotropic material, thus, you don't need
the Anisotropic socket
![Screenshot_3](https://user-images.githubusercontent.com/36862146/224481353-b149a79b-a2df-4638-9961-1ce3363d0f07.png)

- You can also use Tangent Map (set to NonRGB VectorDisplacementMap), which would specify exactly what directions to alter the reflection in. Since the compression 
is not NormalMap, don't forget to rescale the texture to -1:1 range (Sub -0.5, Mul (2, 2, 0)) and get rid of the Blue channel (tangent is vec2)

![ec84f2875ae4221a8c66ab29a64f021dca38850a](https://user-images.githubusercontent.com/36862146/224481140-1c7bc232-ba8b-4794-9b24-68344ab483c4.jpeg)

