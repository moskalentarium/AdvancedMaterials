# Toon shader and Outline

## Steps
### Texture prep
64x64 texture. Select a horizontal area and draw your custom gradients. You can switch between them later on
### Unreal Material
VertexNormal can be substituted for a Normal Map texture here. AtmoshphereSunLightVector describes where the Sun is shining from inthe scene. The gradient has to be remapped and turned into UV coordinate data. We pick the gradient with the exposed parameter. The texture is UserInterface2D NonRGB. You can replace vec3 with a Diffuse texture here


But why settle there, when you can have full constol over the shadow as well as avoid weird artifacts on lower poly models? Same thing: find DotProduct and mask it with If nodes. Incredible!


There are 3 methods to render outlines: directly in the shader (cheapest, though with issues), 
First, we use DotProduct to find what normals look at the camera / away, checking threshold (0.15) and mapping based on that. If you don't have rounded corners, you're in trouble. Fresnel just doesn't cut it



Second, Hull Mesh Outline by duplicating the amount of polygons. Duplicate the mesh, set this Masked Unlit Two-sided material and you're good to go
Third, through Post Process, which is covered in my other series