# Advanced Sand Material + Optimization
![Screenshot_4](https://user-images.githubusercontent.com/36862146/223107817-8364931d-d075-4b7a-876a-db5ba92db0ea.png)

## Steps
### Texture prep
- Get sand textures (example is from Megascans)
- AO and Roughness are basically flat color, we can disredard those
- Base Color is one shade, there's no Hue variation. Thus, we only need the Grayscale version
- In Photoshop go Image - Adjustments - Desaturate. Image - Adjustments - Levels and move side hadndles to bring the center one in the middle of the spike

![image](https://user-images.githubusercontent.com/36862146/223110390-36054e52-c7fe-4e18-be79-cf04614a58cf.png)

- Open the Normal Map, paste the Grayscale BC into the Blue Channel, Export
- Create a New File in Photoshop of a smaller size (2K sand, 1K file). Copy and Paste random selections from the Sand Grayscale (they have to be random, but at least 1K) into the Red and Green channels of the new file. Leave the Blue channel White - CustomNoiseNormal
### Unreal Material

![image](https://user-images.githubusercontent.com/36862146/223111983-0c22dc50-5c0d-4605-af35-d84d6771f78e.png)
- Import both textures into Unreal. Megascan normal has to be BC7 NonRGB, Custom Normal is Normalmap NonRGB
- Tiling value of 1 = 1 meter. Here we have to x0.1 to make it 10x10 meters
- Next, we append R and G, x2, -1 (bringing it to -1:1 range), and DeriveNormalZ
- The individual sand grain texture is 1K = 1 meter => 1/1048 of a meter is a pixel = 1 mm = pixel. Texture x2 makes finer
- Custom Normal Red x0.5 + 0.5 (0:1 range), Power 0.2 into Roughness
- BlendAngleCorrectedNormals to combine Normals
- You can use Gaussian Blur x256 on BC to get the correct Hue of the sand
- BlendOverlay with that Vec3 into Base Color of the shader
