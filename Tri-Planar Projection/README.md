# A to Z Tri-Planar Projection
## Using position data

### Local Position mask
Allows us to get XYZ components of a mesh in local space - LocalPosition node. In order to get data im meters, we Mulx0.01

![Screenshot_2](https://user-images.githubusercontent.com/36862146/224490595-43933787-3f5a-4a58-aa93-1fa3ecf3162d.png)

![Screenshot_1](https://user-images.githubusercontent.com/36862146/224490596-a1e909a1-7af0-4954-83e9-a5363b8ec10d.png)

### Object Position driver
Changes in ObjectPosition are being rendered as a Black&White color change

![Screenshot_4](https://user-images.githubusercontent.com/36862146/224491016-ffb61088-f801-4bb0-abc1-e239bfa6bc48.png)

![Screenshot_3](https://user-images.githubusercontent.com/36862146/224491018-36118b8b-67c5-49c2-9610-d0b5947698a5.png)

### Texture Projection
Object's Absolute World Position dictates the way a texture is mapped. The object moves, the texture stays at the same place in World Space. The coordinates are fixed, object is moving through the texture. Node preview acts in the same way

![Screenshot_6](https://user-images.githubusercontent.com/36862146/224491266-042bc955-bee1-411f-b224-12216b73930e.png)

![Screenshot_5](https://user-images.githubusercontent.com/36862146/224491270-93658592-a9a2-4ca7-aa39-63557f212c15.png)

## Creating Directional Masks
![Screenshot_3](https://user-images.githubusercontent.com/36862146/224492249-36ad14d1-777e-4393-acd0-7ac206902d62.png)

Taking the VertexNormal in World Space, getting rid of the negative values, and controlling the sharpness

![Screenshot_1](https://user-images.githubusercontent.com/36862146/224491944-6213f905-f40f-402b-bdcc-508b51bedf07.png)

Dot Product with (1, 1, 1) gives us a Black&White mask, which we then Divide to make the mask come right to the edges

![Screenshot_2](https://user-images.githubusercontent.com/36862146/224492009-f9740b2b-fb57-41c3-bb1a-150e188a5487.png)
![Screenshot_1](https://user-images.githubusercontent.com/36862146/224492557-c3d96294-d550-4ae0-a06c-1dc0b18af90a.png)

SplitComponent to break the masks into their individual directions

![Screenshot_4](https://user-images.githubusercontent.com/36862146/224492416-8c300f67-293e-4012-bdae-ae05477fcf1b.png)

## First iteration of Tri-Planar Projection

### Cheap Tri-Planar Projection
Since we need to project fron all 3 axes, we need to mask and combine the projections. This will result in warping, where the transition happens (UVs blend together). This is solved by increasing the Mask Sharpness and adding a Round node. That leaves us with a sharp transition or seams bettween the masks

![Screenshot_2](https://user-images.githubusercontent.com/36862146/224493337-32b73690-4a36-4cc5-929d-7c3f57cee9cf.png)

![Screenshot_3](https://user-images.githubusercontent.com/36862146/224493336-0514fe34-6a98-46a6-9a8d-477388f8ad7e.png)

### Seamless Transition
Here we're going to sample the textures first, then blend them together. Sharpness is back to 8, Round is deleted

![Screenshot_4](https://user-images.githubusercontent.com/36862146/224493814-6a77157e-0c8b-4277-a912-952f6787573e.png)

![Screenshot_5](https://user-images.githubusercontent.com/36862146/224493812-55d79414-ba3c-471e-bd84-640e18b71786.png)
