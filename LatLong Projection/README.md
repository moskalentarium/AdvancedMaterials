# LatLong Projection
Latitude and Longitude projections, for example HDRI maps, captures of an environment, are projected in LatLong format. Here we're going to use am image like this directly in a shader

## DDX and DDY
These nodes provide provide a fast method to find the slope or difference between values. DDX for finding difference in X, DDY in Y. Usually finding a slope (slope or gradient of a line is a number that describes both the direction and the steepness of the line) requires 2 data points. Subtracting one value from another gives the difference or the slope, but these nodes take advantage of a feature on a graphics chip that allows us to find a slope with a single value. Graphics hardware renders pixels in 2x2 blocks, we can use the DDX node to ask to see the data in the pixel immediately to the right of our current pixel (same with DDY)

We can use DDX and DDY to create normals for the faces of a model. This might be useful for foliage, where if you bent the Vertex Normals up on your foliage cards (to get better lighting), but you still need accurate face normals to fade out the cards of the foliage, when they're edge on to the camera (check the Foliage collection)
Here we're going to calculate our own normals using DDX and DDY. Result of DDX is a horizontal vector that is parallel with the surface of the model, result of DDY is a vertical vector that is also parallel with the surface. CrossProduct gives us a vector that's perpendicular to both vectors - a WorldSpaceNormal




## LongLatToUV breakdown
Incoming data is vec3, so we generate the U coordinate out of the R and G values and V coordinate out of the B value. Not Normalizing the input and using fast versions of Arctan and Arccos saves us some performance!



## LatLong Projection
We can use the PixelNormal to project/map a texture onto an object with LongLatToUV node. However, we get a weir seam right in me middle because the coordinates jump instantly from 0 to 1 and the sampler can't figure out which MipMap to use. By switching the MipValueNode to Derivative for the TextureSample, we get access to DDX and DDY sockets that solve it for us



## Convert Bump map to Normal with DDX and DDY
You can use the node PerturbNormalLQ and Transform WS to TS and you're good!