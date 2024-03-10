# Water Shader

- [Part I: Gerstner Wave](#part-i-gerstner-wave)
- [Part II: Panning Normal Maps](#part-ii-panning-normal-maps)
- [Part III: Refraction and Depth](#part-iii-refraction-and-depth)

## Part I: Gerstner Wave

![Screenshot_1](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/1c7e4bf6-205d-4579-88ab-0fb57f00adcb)

This setup here is a simple Gerstner Wave that moves along a Direction vector in a sine-like pattern

![Screenshot_13](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/6a3ff8d1-4940-4723-962d-dc45ac6e2722)

This is the complete Gerstner Wave with the Absolute World Position that excludes offsets and the gravitational force set to 980m/s

![Screenshot_15](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/1fa60acb-63e1-47f4-90c7-09babad9b0eb)

To make the waves sharper, we subtract a cosine of the function from the result (eats away from the smoothness of the peaks)

![Screenshot_14](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/fde71515-3edd-470a-917d-7d43dcac9b0c)

Next up we derive Normal data from the sine and cosine function outputs

![Screenshot_18](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/7b3e0d96-1c4b-43f4-813b-f2fa7fec09f7)

Here are the Master Material values that seem to work OK. You should add a Vertex Interpolator node before the Shader's Normal input to move ~50 instructions from PS to VS

![Screenshot_17](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/37b71684-3a1b-4245-84a0-c1e0e3e94b02)

## Part II: Panning Normal Maps

![Screenshot_5](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/e6c897cf-2d91-4aca-a545-43a9f1531697)

Adding some panning Normal Maps could be as easy as this

![Screenshot_2](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/3698c8ef-6a77-4fc5-8109-c6f34e218ff4)

If you only use a Normal Map Texture (and there are no more textures to pack them together), then you can reduce your instruction count by moving some of the shader math from under the hood to the shader itself. If a texture's compression is set to Normal Map, then Unreal moves the RED channel to the Alpha channel, leaves GREEN as is, ignores the BLUE channel, expands the data from [0:1] to [-1:1], and derives normal Z. Why do that math every time you read a Normal map, if you can combine them first and then do the math once after?

In Photoshop, we swizzle the channels to be R-Empty, G-Green, B-Empty, A-Red, set Texture Compression to Default, No sRGB (Format should become DXT5). Then we do the "Normal Map Math", ADD data to "blend", MUL data to "flatten", and DeriveNormalZ ONCE, not thrice. This brings the instruction count down by 18

![Screenshot_3](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/055987d6-c3c2-4753-aa72-bd2dbc82d8c9)

Here's the final optimized Part II shader
NOTE that the Normals look nicer when unoptimized

![Screenshot_4](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/4914243e-dec3-4071-84bb-53c1f89e968b)

## Part III: Refraction and Depth

First, we set Shader Parameters as follows: Translucent, Two-Sided ON, Cast Ray Traced Shadows OFF, Screen Space Reflections ON (OFF for optimization), Lighting Mode to Surface Translucency Volume, Refraction Method to Pixel Normal Offset

Then, we need to make the water limit the visibility the further behind the water plane objects are. Here, we get a coefficient between the Scene Depth (behind the plane) and Pixel Depth (the plane). The deeper, the brighter values we get

![Screenshot_1](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/410d22e8-d7fb-4a45-8ec4-8fba61b8218e)

However, that's dependent on the distance from the camera, so we need to factor in how far away the camera is to keep the gradient consistent. We take the camera's Z position and adjust the gradient, then we offset the gradient away from the camera
NOTE: Don't let the camera go below the floor plane

![Screenshot_2](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/474bad0d-0f5d-48d5-b28b-c43c58556a40)

We multiply the result with the Depth Fade node to smooth out the opacity transition where the water meets opaque objects

![Screenshot_3](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/0701752d-c1c4-40ad-88dd-3fe704dd0e93)

Since we don't want to have any refraction close where the water surface touches opaque objects, we use a Lerp

![Screenshot_4](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/b532f4fe-b500-433e-b40e-b2e8f6e5bd3d)

