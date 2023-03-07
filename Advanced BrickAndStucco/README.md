# Advanced Brick and Stucco material
![Screenshot_1](https://user-images.githubusercontent.com/36862146/223380115-1b2b72d5-0286-4888-a84f-775aea2757c4.png)

## Steps
### Texture prep
- Megascan textures come unpacked. Simply pack a OcclusionHeightRoughness textures for Brick and Stucco
- Check Green Channel - it should look like it's lit up from below
- Uncheck sRGB for the OHR textures
### Unreal Material
- Brick texture is 2x2m, Stucco is .35x.35m, thus having to adjust for that
- Use Brick OHR Green channel as an input for the masking Lerp
- 
![Screenshot_2](https://user-images.githubusercontent.com/36862146/223380451-e032b210-1daa-4513-8f66-c6c94eced32f.png)

- Continuing from Lerp we create a high contrast mask. Mask (G) drives the main Lerps between Brick and Stucco, Saturated Subtraction drives the Transition Color

![Screenshot_3](https://user-images.githubusercontent.com/36862146/223380599-a1167657-f5bd-4d3a-859e-857281aab70b.png)

- Base Color setup

![Screenshot_4](https://user-images.githubusercontent.com/36862146/223382273-3d577831-e7c0-48ae-a974-0f77748a32b0.png)

- We want some of the Brick Normals to be seen through Stucco. BlendAngleCorrectedNormals into a 0.8 Lerp into the main Lerp

![Screenshot_5](https://user-images.githubusercontent.com/36862146/223382293-e73ec8d2-a754-4fec-9e59-cfa7ec7af188.png)

- OHR setup

![Screenshot_6](https://user-images.githubusercontent.com/36862146/223382304-df256e88-7ffc-4b98-aede-6dc87c28ca90.png)

- Finished Material

![Screenshot_7](https://user-images.githubusercontent.com/36862146/223383295-dac55cce-d206-4319-adbc-c041580d4e13.png)
