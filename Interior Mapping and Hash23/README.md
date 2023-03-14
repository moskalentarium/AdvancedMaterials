# Interior Mapping and Hash23

Interior mapping is a way to make simple shapes appear to have interior details

## Creating Cubemaps
Here's a good illustration what a cubemap is: six sides of a cube, six images, that represent different sides of an environment. It's a shortcut - if I pre-render my environment into a cubemap, then when I want ot sample it, I don't have to render my entire environment, just the cubemap

## Capture a Cubemap
I could set 6 cameras, FOV of 90*, aspect ratio to square, and I would capture the images. Either way, I simply need 6 images, that are properly alligned. There's an issue with Unreal and Cubemaps: they have to be in .dds format, which Photoshop can't export to. We will need the NVIDIA Texture Tools plug-in for Photoshop (32bits/Channel, 8.8.8.8 ARGB 32 bpp, unsigned) and arrange the textures as such:

So open up your array of 6 textures in Photoshop, Save as... .DDS, Format "32bits/Channel, 8.8.8.8 ARGB 32 bpp, unsigned", Texture type "Cube map", Generate MipMaps OFF. Upon importing it to Unreal, check that it is indeed a CubeMap. How do you like them apples?

## Interior Mapping
We have a simple cube and we want to make it look like it has an interior. We'll achieve that with combination of TexCoord on the surface as well as the CameraVector. First, we need to adjust the UVs a little, so that we can combine them with the CameraVector. Frac will prevent stepping outside of 0:1 range, Mul and Subtract will expand, move to the center, and flip the UVs on the Y

CameraVector is originally in WS, we need it to be in TS (relative to the surface), so we Transform it as well as flipping the X axis as it was giving us incorrect results

Combining the CameraVector with TexCoord will map the texture as if it's mapped inside the cube. Now, this is what Divide output shows (coordinates are projected from the camera), 

This is what Subtract shows,

This is what Min-Min show

Mul combines the original CameraVector with the projection, adding the TexCoord to it maps the texture on the "inside" of the cube. We just need to swizzle the channels, so that they are rotated properly and we're done!