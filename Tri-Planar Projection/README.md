# A to Z Tri-Planar Projection
## Using position data

### Local Position mask
Allows us to get XYZ components of a mesh in local space - LocalPosition node. In order to get data in meters, we Mulx0.01

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
Since we need to project from all 3 axes, we need to mask and combine the projections. This will result in warping, where the transition happens (UVs blend together). This is solved by increasing the Mask Sharpness and adding a Round node. That leaves us with a sharp transition or seams between the masks

![Screenshot_2](https://user-images.githubusercontent.com/36862146/224493337-32b73690-4a36-4cc5-929d-7c3f57cee9cf.png)

![Screenshot_3](https://user-images.githubusercontent.com/36862146/224493336-0514fe34-6a98-46a6-9a8d-477388f8ad7e.png)

### Seamless Transition
Here we're going to sample the textures first, then blend them together. Sharpness is back to 8, Round is deleted

![Screenshot_4](https://user-images.githubusercontent.com/36862146/224493814-6a77157e-0c8b-4277-a912-952f6787573e.png)

![Screenshot_5](https://user-images.githubusercontent.com/36862146/224493812-55d79414-ba3c-471e-bd84-640e18b71786.png)

## Second iteration of Tri-Planar Projection
NOTE: Issues N1 and N2 are irrelevant in case of projecting a texture without attributes that are required to be projected with 100% accuracy. Don't bother with the math if directional, numerical, and symmetrical aspects aren't crucial
### Issue N1: upside-down textures
We can see that the numbers on X and Y are upside-down. Fixing the B component fixes it

![Screenshot_1](https://user-images.githubusercontent.com/36862146/224494152-f5c1832e-5c78-4a13-afa5-70c7e3d96edc.png)
![Screenshot_2](https://user-images.githubusercontent.com/36862146/224494363-3d97f37d-eb6a-4086-9161-fa8c4a03eb30.png)

### Issue N2: Numbers are inverted
We project the texture through the mesh, so it's inverted in the other side. Sign node returns 1 or -1 depending on the sign (+/-) of the input. So I'm checking which direction the normal is facing, then depending on a facing value I'm flipping/not flipping the projection

![Screenshot_3](https://user-images.githubusercontent.com/36862146/224495054-d946f484-ca0d-4be6-b182-f71e88ae0907.png)

