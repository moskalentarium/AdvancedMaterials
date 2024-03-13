# Water Shader

- [Part I: Gerstner Wave](#part-i-gerstner-wave)
- [Part II: Panning Normal Maps](#part-ii-panning-normal-maps)
- [Part III: Refraction and Depth](#part-iii-refraction-and-depth)
- [Part IV: Foam](#part-iv-foam)
- [Part IV: Foam](#part-v-caustics)

![Screenshot_3](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/58e0f05e-0036-4c0d-8c64-1b471d0b3cd6)

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

================================================================================
#### UPD Ben's version of the shader is inconsistent: the farther away we are, the more transparent the water gets. Also, getting the water below the Z floor makes it fully opaque. Here's the better version of the shader
--------------------------------------------------------------------------------

![Screenshot_2](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/717fc299-7290-43f8-ad24-8d7f51c72888)

================================================================================

Then, we need to make the water limit the visibility the further behind the water plane objects are. Here, we get a coefficient between the Scene Depth (behind the plane) and Pixel Depth (the plane). The deeper, the brighter values we get

![Screenshot_1](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/410d22e8-d7fb-4a45-8ec4-8fba61b8218e)

However, that's dependent on the distance from the camera, so we need to factor in how far away the camera is to keep the gradient consistent. We take the camera's Z position and adjust the gradient, then we offset the gradient away from the camera
NOTE: Don't let the camera go below the floor plane

![Screenshot_2](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/474bad0d-0f5d-48d5-b28b-c43c58556a40)

We multiply the result with the Depth Fade node to smooth out the opacity transition where the water meets opaque objects

![Screenshot_3](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/0701752d-c1c4-40ad-88dd-3fe704dd0e93)

Since we don't want to have any refraction close where the water surface touches opaque objects, we use a Lerp

![Screenshot_4](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/b532f4fe-b500-433e-b40e-b2e8f6e5bd3d)

## Part IV: Foam

The way we're going to generate foam around emerged objects is by sampling the depth and using it as a mask. This is cheap, but looks "unfinished"

![Screenshot_4](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/c55d071e-4cc7-48ea-8d6b-edc1251ee9f1)

Here we introduce a 4Way_Chaos function that is shipped with Unreal and moves the same texture in NE-SE-SW-NW directions. Then we get the Depth Mask (picture below), set our custom depth (above which depth to draw it), adjust the values a bit to get the sparse foam to be applied further/deeper than the dense foam. We mask the scrolling texture and combine the result to become a white mask

![Screenshot_1](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/98a33c62-4763-417c-a56a-81cad4cffdb5)
![Screenshot_3](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/983913e8-dd2c-4190-a547-0e00969d9804)

We use that mask to drive color and opacity

![Screenshot_2](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/d0b18288-5bc8-44de-a539-23ab12ca3ab1)

## Part V: Caustics

Caustics here are a simple Tri-Planar Decal setup that pans the Caustic mask with some UV distortion

![Screenshot_4](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/4afde594-0112-4d65-ade4-d193c8d2d34d)

The problem here is that the shader looks at the World Normal and gets all the Normal Map data, which has a lot of vector transitions. This creates hard seams on the sampled meshes

![Screenshot_5](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/8c89302e-db10-49a4-9965-a506ba5b6297)

One way to get around that is to get a derivative of the position. The cross product of DDY and DDX returns World Space Face Normal (not smooth, but not as detailed as texture normals)

![Screenshot_2](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/d332c867-ff69-4ea1-9c42-7b2e6d341709)

![Screenshot_1](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/f47f175a-1988-4c35-bd7e-4aa9efbb453b)

Additionally, the decal has a hard cut-off. It would be nice to create a "3D" fade mask that smooths it out

![Screenshot_6](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/59d52ad7-d556-486e-8491-ad553c5ea9aa)

![Screenshot_3](https://github.com/moskalentarium/AdvancedMaterials/assets/36862146/6cf1fe0d-b1d7-4a52-90e4-4015038b1a09)
