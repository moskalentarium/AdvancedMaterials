# LatLong Projection
Latitude and Longitude projections, for example HDRI maps, captures of an environment, are projected in LatLong format. Here we're going to use am image like this directly in a shader

## DDX and DDY
These nodes provide provide a fast method to find the slope or difference between values. DDX for finding difference in X, DDY in Y. Usually finding a slope (slope or gradient of a line is a number that describes both the direction and the steepness of the line) requires 2 data points. Subtracting one value from another gives the difference or the slope, but these nodes take advantage of a feature on a graphics chip that allows us to find a slope with a single value. Graphics hardware renders pixels in 2x2 blocks, we can use the DDX node to ask to see the data in the pixel immediately to the right of our current pixel (same with DDY)

We can use DDX and DDY to create normals for the faces of a model. This might be useful for foliage, where if you bent the Vertex Normals up on your foliage cards (to get better lighting), but you still need accurate face normals to fade out the cards of the foliage, when they're edge on to the camera (check the Foliage collection)
Here we're going to calculate our own normals using DDX and DDY. Result of DDX is a horizontal vector that is parallel with the surface of the model, result of DDY is a vertical vector that is also parallel with the surface. CrossProduct gives us a vector that's perpendicular to both vectors - a WorldSpaceNormal

![Screenshot_1](https://user-images.githubusercontent.com/36862146/224826437-9c37d7ae-c886-4242-99aa-abbebdeb9c5c.png)
![Screenshot_2](https://user-images.githubusercontent.com/36862146/224826431-e9c1bb03-0e98-4392-813b-8f0fb34369ab.png)

## LatLong Projection
NOTE: Make sure to convert your HDRI into a PNG, so that Unreal treats it as a texture, not a CubeMap. Otherwise you aren't able to use vec2 as UV input

We can use the PixelNormal to project/map a texture onto an object with LongLatToUV node. However, we get a weir seam right in me middle because the coordinates jump instantly from 0 to 1 and the sampler can't figure out which MipMap to use. By switching the MipValueNode to Derivative for the TextureSample, we get access to DDX and DDY sockets that solve it for us

![Screenshot_4](https://user-images.githubusercontent.com/36862146/224826397-e25800d5-3e37-4721-afc5-59513a72b3ab.png)
![Screenshot_3](https://user-images.githubusercontent.com/36862146/224826394-10a3997c-02c5-4504-8a9f-7f99557f95ab.png)
![Screenshot_6](https://user-images.githubusercontent.com/36862146/224826402-2a6b705e-88ee-43b1-a924-09420d7645e1.png)
![Screenshot_5](https://user-images.githubusercontent.com/36862146/224826399-e7f3319c-5509-4c82-b358-503b3720f74e.png)

## LongLatToUV breakdown
Incoming data is vec3, so we generate the U coordinate out of the R and G values and V coordinate out of the B value. Not Normalizing the input and using fast versions of Arctan and Arccos saves us some performance!

![Screenshot_7](https://user-images.githubusercontent.com/36862146/224826405-5616b907-9de6-46e9-8c70-a74b2518e87a.png)
![Screenshot_8](https://user-images.githubusercontent.com/36862146/224826409-fd019c3c-f0ff-42b6-8ee7-d49bf791d5e7.png)

## Convert Bump map to Normal with DDX and DDY
You can use the node PerturbNormalLQ and Transform WS to TS and you're good!
