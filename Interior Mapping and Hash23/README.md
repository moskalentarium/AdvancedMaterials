# Interior Mapping and Hash23

Interior mapping is a way to make simple shapes appear to have interior details

## Creating Cubemaps
Here's a good illustration what a cubemap is: six sides of a cube, six images, that represent different sides of an environment. It's a shortcut - if I pre-render my environment into a cubemap, then when I want ot sample it, I don't have to render my entire environment, just the cubemap

![Screenshot_1](https://user-images.githubusercontent.com/36862146/225050722-f4922041-777e-47ca-b9f5-519c662b3aa6.png)

### Capture a Cubemap
I could set 6 cameras, FOV of 90*, aspect ratio to square, and I would capture the images. Either way, I simply need 6 images, that are properly alligned. There's an issue with Unreal and Cubemaps: they have to be in .dds format, which Photoshop can't export to. We will need the NVIDIA Texture Tools plug-in for Photoshop (32bits/Channel, 8.8.8.8 ARGB 32 bpp, unsigned) and arrange the textures as such:

![CubeMapNvidiaLayout](https://user-images.githubusercontent.com/36862146/225050716-482477a1-c76b-45ef-9c13-73a8c61b27cd.png)

So open up your array of 6 textures in Photoshop, Save as... .DDS, Format "32bits/Channel, 8.8.8.8 ARGB 32 bpp, unsigned", Texture type "Cube map", Generate MipMaps OFF. Upon importing it to Unreal, check that it is indeed a CubeMap. How do you like them apples?

![Screenshot_2](https://user-images.githubusercontent.com/36862146/225050704-71599367-777b-487d-89f9-84e6f5ee8527.png)
![Screenshot_4](https://user-images.githubusercontent.com/36862146/225050780-c2e1ab23-a7dd-4ca8-a01a-ac6ccd142c34.png)

![Screenshot_3](https://user-images.githubusercontent.com/36862146/225050689-2a8d285d-da69-49c8-87a1-f38d9639e7b1.png)

## Interior Mapping
![Screenshot_9](https://user-images.githubusercontent.com/36862146/225050737-fbdc08a4-aaca-4098-a368-5e1663231f8d.png)
![Screenshot_10](https://user-images.githubusercontent.com/36862146/225050733-a7f52eba-8da7-4dea-8aa5-437d1d21af3d.png)

We have a simple cube and we want to make it look like it has an interior. We'll achieve that with combination of TexCoord on the surface as well as the CameraVector. First, we need to adjust the UVs a little, so that we can combine them with the CameraVector. Frac will prevent stepping outside of 0:1 range, Mul and Subtract will expand, move to the center, and flip the UVs on the Y

![Screenshot_5](https://user-images.githubusercontent.com/36862146/225050776-817b8c85-7abc-4c5a-adae-c046e3c8a37d.png)
![Screenshot_8](https://user-images.githubusercontent.com/36862146/225050760-6dca58a6-4879-4134-8b7a-192e6d12f17d.png)

CameraVector is originally in WS, we need it to be in TS (relative to the surface), so we Transform it as well as flipping the X axis as it was giving us incorrect results

![Screenshot_6](https://user-images.githubusercontent.com/36862146/225050768-5d0c23fb-aad7-4976-b552-77172ce44db4.png)
![Screenshot_7](https://user-images.githubusercontent.com/36862146/225050762-77eeb50f-93ca-42ab-bea2-c807099a425a.png)

Combining the CameraVector with TexCoord will map the texture as if it's mapped inside the cube. 

![Screenshot_1](https://user-images.githubusercontent.com/36862146/225295098-5b2ed2fa-a2db-443a-a391-b1081714cb66.png)

Now, this is what Divide output shows (coordinates are projected from the camera), 

![CameraVectorDivide](https://user-images.githubusercontent.com/36862146/225050756-ab8d355d-e62d-43e3-b805-f7e1c1acb92f.png)

This is what Subtract shows,

![CameraVectorSubtract](https://user-images.githubusercontent.com/36862146/225050750-9acbf739-7006-4d79-aaf4-502730521853.png)

This is what Min-Min shows

![CameraVectorMinMin](https://user-images.githubusercontent.com/36862146/225050742-2103f995-b5d0-4866-b3b0-74693f1e20b2.png)

Mul combines the original CameraVector with the projection, adding the TexCoord to it maps the texture on the "inside" of the cube. We just need to swizzle the channels, so that they are rotated properly and we're done!

![CameraVectorMul](https://user-images.githubusercontent.com/36862146/225050728-a3aebd09-0d44-4911-8a92-ce3cab93af34.png)
![Screenshot_11](https://user-images.githubusercontent.com/36862146/225050725-1e795bb5-1f5c-4359-9ae4-7bb248df9fde.png)
![Screenshot_10](https://user-images.githubusercontent.com/36862146/225050733-a7f52eba-8da7-4dea-8aa5-437d1d21af3d.png)

## Noise Hash23
### Noise to randomize the rooms
Right now our shader has only one room, so we need a parameter to control the anount of rooms. Simply Mul TexCoord by a vec2! Though every single room looks exactly the same, which we are going to fix

### MF_Hash23
Hash23 becuase we're inputting a vec2 and generating a vec3. For this Material Function we scrambleb up the UV into 3 vec3 (XYX, YXX, XYY), got a DotProduct with 3 random numbers ((127.1, 311.7, 74.7), (269.5, 183.3, 246.1), (113.5, 271.9, 124.6)), which made a new vec3. We scramble it again with 43758.5453123 and Frac

![Screenshot_1](https://user-images.githubusercontent.com/36862146/225069115-63a7004d-d6bc-47b4-ba41-5e1431171bac.png)

So if we plug our UVs in, we get a random number/color in every room. In order to make this data a bit more useful instead of a random color, we want each room to have 0 or 1 in each of the RGB values. So Round is going to do just that

![Screenshot_2](https://user-images.githubusercontent.com/36862146/225069119-90c2ef99-6839-4cf5-94a4-dc27f9d1121b.png)
![Screenshot_3](https://user-images.githubusercontent.com/36862146/225069125-61ab5a31-11c5-4f75-9a7b-d834ba69a108.png)

We're going to use that random data to: (1) choose a random side/wall to be the back and (2) whether or not to mirror the wall, so that it's showing the inverse of what it is. We Mul the result of that with the swizzled RGB right before the TextureSample to get this

![Screenshot_4](https://user-images.githubusercontent.com/36862146/225069128-7793d5fa-20b1-4523-93ed-28a185f3984c.png)
![Screenshot_5](https://user-images.githubusercontent.com/36862146/225069130-29eec8e8-3691-413c-a4d8-86cbcb064d4f.png)

And to finish it up we plug the Blue channel from Hash23 into a Lerp to interpolate between the green textures as well!

![Screenshot_7](https://user-images.githubusercontent.com/36862146/225069105-b3bfe34e-1213-4b16-aee1-ed058ef65f07.png)
![Screenshot_6](https://user-images.githubusercontent.com/36862146/225069135-a12c25c3-57e8-4596-a124-39bcc4f873e0.png)

NOTE: The full material setups are below!
## Interior Mapping polish
To give this material a more practical use, we should convert it into a Material Function as well as create a set of textures for interior and exterior of this "building". MF_ is as easy as copy and paste the setup without the TextureSample, add a vec2 input for the size of the grid, a vec2 with DefaultValue checked for both, and a second Output node capturing Round of Hash23

### MF_InteriorCubeMapping
![CubeMapping3](https://user-images.githubusercontent.com/36862146/225293600-b9e31822-ed00-4328-8cc4-4610a3faeb41.png)
![CubeMapping1](https://user-images.githubusercontent.com/36862146/225293589-5840c599-cd19-4dee-bbe0-b2e0dbb88d76.png)
![CubeMapping2](https://user-images.githubusercontent.com/36862146/225293596-d3187d98-56f3-4066-b9c3-579bdb253d42.png)

### M_InteriorCubeMapping
![M_Mapping](https://user-images.githubusercontent.com/36862146/225293609-645aadbf-df75-4028-b63f-618c80b7b193.png)

When creating a texture, make sure that Y resolution = 6x X resolution, while Y in at least 512px. Here I has to adjust the texture UVs. In order to to dim the windows down, we Mulx.5, then we use fresnel to control darkness of the windows that are at a steeper angle. Same darkening is applied to the interior, as well as random cells are getting lit up

![Screenshot_3](https://user-images.githubusercontent.com/36862146/225293583-a5e6d7e7-676f-4204-ae98-984fd35cac8f.png)

## Object Space Interior Mapping
So previously, the interior of a building is dependent on UVs, now we make it dependent on Object Space Position (used when UVs aren't really well laid out or create an interior with a roof). M_InteriorMapping, MF_InteriorCube, and Hash will have to be updated

### Hash33

![Hash33](https://user-images.githubusercontent.com/36862146/225293607-5abd7cf9-7f56-4a0e-9cd3-71d63c89ad61.png)

### MF_InteriorCubeObject

![CubeMappingObject1](https://user-images.githubusercontent.com/36862146/225293602-70b75ec0-b320-4b8d-a18b-dca20f427b20.png)
![CubeMappingObject2](https://user-images.githubusercontent.com/36862146/225293605-f9e08b4a-96e6-44d9-aca2-e09063576e73.png)

### M_InteriorMappingObject

![M_Object](https://user-images.githubusercontent.com/36862146/225293576-7d638670-ddfb-4d07-a3f8-af62aa4a0cc8.png)

### Result
![Screenshot_5](https://user-images.githubusercontent.com/36862146/225293588-d931ecde-5900-4272-b337-137e7a87a573.png)
