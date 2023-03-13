# Toon shader and Outline
![Screenshot_3](https://user-images.githubusercontent.com/36862146/224748226-4075e8fb-b590-4b20-a37c-20396ab9a231.png)

## Steps
### Texture prep
64x64 texture. Select a horizontal area and draw your custom gradients. You can switch between them later on

![ToonShadingVariation](https://user-images.githubusercontent.com/36862146/224748267-38f5d4f8-831f-42f3-aa20-1bc362a5b84e.png)

### Unreal Material
VertexNormal can be substituted for a Normal Map texture here. AtmoshphereSunLightVector describes where the Sun is shining from inthe scene. The gradient has to be remapped and turned into UV coordinate data. We pick the gradient with the exposed parameter. The texture is UserInterface2D NonRGB. You can replace vec3 with a Diffuse texture here

![Screenshot_1](https://user-images.githubusercontent.com/36862146/224748208-28ce1af4-0516-4005-b61c-d3c7bcae08c1.png)
![Screenshot_2](https://user-images.githubusercontent.com/36862146/224748223-eb2bde44-c714-43d6-a718-5d4746d29e06.png)

But why settle there, when you can have full control over the shadow as well as avoid weird artifacts on lower poly models and having to sample a texture? Same thing: find DotProduct and mask it with If nodes. Incredible!

![Screenshot_10](https://user-images.githubusercontent.com/36862146/224748256-9a148a87-299f-4ec1-a873-8557a59dfb7e.png)
![Screenshot_11](https://user-images.githubusercontent.com/36862146/224748261-7298ad23-0139-457e-8112-7495f72be357.png)

There are 3 methods to render outlines: directly in the shader (cheapest, though with issues), Hull Mesh Outline, and in Post process
First, we use DotProduct to find what normals look at the camera / away, checking threshold (0.15) and mapping based on that. If you don't have rounded corners, you're in trouble. Fresnel just doesn't cut it

![Screenshot_4](https://user-images.githubusercontent.com/36862146/224748229-eeca0957-da5f-4a65-88bc-b08b6fd4695a.png)
![Screenshot_5](https://user-images.githubusercontent.com/36862146/224748233-545bb117-ba35-4e91-984c-c3e3b1c16ebd.png)
![Screenshot_6](https://user-images.githubusercontent.com/36862146/224748235-ffb29d07-0974-4bd1-9841-49c8a1021066.png)
![Screenshot_7](https://user-images.githubusercontent.com/36862146/224748240-02031569-3cf3-483a-bca6-c40a7618e987.png)

Second, Hull Mesh Outline by duplicating the amount of polygons. Duplicate the mesh, set this Masked Unlit Two-sided material and you're good to go

![Screenshot_8](https://user-images.githubusercontent.com/36862146/224748245-7334c410-4e09-41cc-95da-47382a8c829c.png)
![Screenshot_9](https://user-images.githubusercontent.com/36862146/224748251-51934da4-071b-4643-83ad-52d4ea0cf991.png)

Third, through Post Process, which is covered in my other series
