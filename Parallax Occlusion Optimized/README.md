# Parallax Occlusion Optimization
![Screenshot_1](https://user-images.githubusercontent.com/36862146/223395550-cae6af2a-db34-4b56-9bcf-44a8b2f8b1b5.png)

## Description
Parallax Occlusion Mapping gives depth, detail, and illusion of shape on a mesh with no geometry required. 
## Steps
### Texture prep
- Blur the Height texture a bit (8 px) to get rid of sharp egdes - Parallax gives best results. Scale the texture down to 256x256
- Pack a OHR/ORM/etc texture
- For Normal Map check the Green channel to be lit up from below, uncheck sRGB from the ORH/ORM

### Unreal Material
- Amount of Steps is the single most important factor when optimizing a Parallax Material. We need more steps when closer to the Mesh + when parallel to the mesh
- We're driving the Steps count with DotProduct and Distance from mesh. The DotProduct soulution is already baked into the ParallaxMapping node in Unreal
- The only note is the Ceil node makes the values flat as well as doesn't allow them to go below 1
![Screenshot_2](https://user-images.githubusercontent.com/36862146/223395559-0541a64e-0aea-4afd-bdce-5f4c233a1319.png)

### DotProduct setup
- DotProduct between Camera Vector and VertexNormalWS
- Abs, Saturate, OneMinus, and Power of 2
- Then we multiply it by MaxSteps, Ceil (to round to whole, never less than 1)
