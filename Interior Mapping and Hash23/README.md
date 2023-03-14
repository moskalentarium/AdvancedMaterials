# Interior Mapping and Hash23

Interior mapping is a way to make simple shapes appear to have interior details

## Creating Cubemaps
Here's a good illustration what a cubemap is: six sides of a cube, six images, that represent different sides of an environment. It's a shortcut - if I pre-render my environment into a cubemap, then when I want ot sample it, I don't have to render my entire environment, just the cubemap

![Screenshot_1](https://user-images.githubusercontent.com/36862146/225050722-f4922041-777e-47ca-b9f5-519c662b3aa6.png)

## Capture a Cubemap
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

Combining the CameraVector with TexCoord will map the texture as if it's mapped inside the cube. Now, this is what Divide output shows (coordinates are projected from the camera), 

![CameraVectorDivide](https://user-images.githubusercontent.com/36862146/225050756-ab8d355d-e62d-43e3-b805-f7e1c1acb92f.png)

This is what Subtract shows,

![CameraVectorSubtract](https://user-images.githubusercontent.com/36862146/225050750-9acbf739-7006-4d79-aaf4-502730521853.png)

This is what Min-Min shows

![CameraVectorMinMin](https://user-images.githubusercontent.com/36862146/225050742-2103f995-b5d0-4866-b3b0-74693f1e20b2.png)

Mul combines the original CameraVector with the projection, adding the TexCoord to it maps the texture on the "inside" of the cube. We just need to swizzle the channels, so that they are rotated properly and we're done!

![CameraVectorMul](https://user-images.githubusercontent.com/36862146/225050728-a3aebd09-0d44-4911-8a92-ce3cab93af34.png)
![Screenshot_11](https://user-images.githubusercontent.com/36862146/225050725-1e795bb5-1f5c-4359-9ae4-7bb248df9fde.png)
![Screenshot_10](https://user-images.githubusercontent.com/36862146/225050733-a7f52eba-8da7-4dea-8aa5-437d1d21af3d.png)
