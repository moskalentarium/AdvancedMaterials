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

### Converting to Material Function
In order to use Tri-Planar Projection conveniently, we probably should create a Materail Function. Simply copy and paste the nodes and add 3 Inputs: Texture2D, Scale (Default 1,1,1), and Sharpness (Default 8)

![Screenshot_4](https://user-images.githubusercontent.com/36862146/224503375-344f0558-a52b-4ec2-9e56-eed9c9418e89.png)

![Screenshot_5](https://user-images.githubusercontent.com/36862146/224503628-35c6a216-1b39-4cc9-b307-073466c9ac5f.png)

## Third iteration of Tri-Planar Projection

### Blending Normals
If we were to take a look at the normals we are blending with the regular Tri-Planar Projection, we would see, that it's not what we want. We're projecting Normal Maps that are in Tangent Space, so they are relevant to the UV coordinates of the mesh, not World. So we're going to fix it by converting Normals to World and then back to Tangent Space

![Screenshot_6](https://user-images.githubusercontent.com/36862146/224504292-e26f50f9-1053-4c8e-a19e-763cde90316b.png)

First, we need to combine Normal Maps with WS Vertex Normals, then Swizzle/rearrange channels and, finally, add TransformVector node. This process includes isolating one direction, comparing it to the correctly mapped normals, and inverting whole/facing wrong way channels. Hope you love pasta

![Screenshot_7](https://user-images.githubusercontent.com/36862146/224506251-c32a3a9f-4660-4ead-a7b1-086a440d4242.png)

![Screenshot_8](https://user-images.githubusercontent.com/36862146/224506252-6ee54ed4-795f-4154-9e94-23330df4dfdf.png)

![Screenshot_9](https://user-images.githubusercontent.com/36862146/224506249-b2d61128-6653-4180-a9c4-4a2174589fa9.png)

Feel free to edit that into a Material Function to never see those connections again

![Screenshot_11](https://user-images.githubusercontent.com/36862146/224506895-d9dee6b6-c69a-4264-bf72-0b0bdc551947.png)

![Screenshot_10](https://user-images.githubusercontent.com/36862146/224506898-fd4607ea-fdf3-44fa-94c6-d036c1853b07.png)

