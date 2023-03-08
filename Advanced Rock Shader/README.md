# Advanced Rock
![Screenshot_3](https://user-images.githubusercontent.com/36862146/223722634-ae8d79bc-703d-4ea4-b9ba-4d11f355dc76.png)

## Description
Artists and LDs use assets (rocks, for example) to create environments. Scaling such objects introduces visible differences in Texel Density, which we will overcome with this setup

## Steps
### Texture prep
- These textures are a part of "Starter Content" folder in Unreal. First, export them as TGA (BC, Mask, Normal, Large Normal, Fine Normal)
- Base Color and Roughness are tiling, so we add Roughness as Alpha channel (_CR)
- Normal Map and Occlusion are baked, so we add Occlusion as Blue channel (_NO)
- Large and Fine Normals are fine as is
### Unreal Material
- _CR texture is Default, the rest are BC7 NonRGB
- We want the UV to scale along with the mesh. UV x ObjectScale does exactly that (can't mul vec2 and vec3)
![Screenshot_1](https://user-images.githubusercontent.com/36862146/223721555-3a67ba81-9912-4bf3-8794-9c933f3e8dda.png)

- _NO texture is being scaled to -1:1. Detail Normal Maps as well, but in order to combine, ADD in this case, the maps, we need to disregard the Z channel ie x0. I'm assuming that it's simply cheaper than Blend
- Adding _CR(A), Large(B), and Fine(B) together and Saturating gives us the Roughness output
![Screenshot_2](https://user-images.githubusercontent.com/36862146/223721564-ac3d2bf3-6708-4179-b9ff-9dac47ecd289.png)
