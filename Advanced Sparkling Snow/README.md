# Advanced Sparkling Snow
![Screenshot_1](https://user-images.githubusercontent.com/36862146/223499025-cfb1b012-ef96-41d1-bd9c-e0a0d4f31ecd.png)

## Steps
### Texture prep
- Originally it's a standard 2K texture pack from Megascans, which we are optimizing
- Almost no detail in the BC, just eyedrop to get the color. It's going to be a simple vec3 in Unreal Material
- Normal Map has 2 scales of detail: snowflake noise and wind blown layered snow. We're using a small portion of this map to represent the fine details in 512x512px, then scaling the Normal Map down to a 1K texture. In result we have a 1K + 512 textures, instead of 2K or even 4K textures
- Ambient Occlusion goes into Blue channel of the Normal Map, Roughness goes into the Alpha channel - NOR texture
- Create a new 256x256 texture filled with 127,127,127. Filter, Noise, Add Noise, 25% Gaussian Monochromatic. Image, Adjustments, Levels, Right hadle to the right, make the white pixels pop. Scale it up to 512x512 with Nearest Neighbour method

### Unreal Material
- Snow fine detail map is a NormalMap, check the Green channel to be lit from below
- Snow large detail is Default NonRGB, check the Green
- Sparkle texture comression is GrayscaleR8 (Only using the Red channel), NonRGB
- Sparkle setup is going to be masked by the Sun Light Vector (not to have sparkles where the sun can't hit). Additionally, sparkles jump, which we are driving with ScreenPosition
![Screenshot_2](https://user-images.githubusercontent.com/36862146/223499014-bfa48fa3-4282-4607-9fb3-5afe2c5eb8de.png)

- Large Normal detail needs to be scaled to -1:1 and blended with the Fine detail (you can scale it x12, even x16). O and R in _NOR are plugged in as well
- Color is almost white with extra "whiteness" on the edges
![Screenshot_3](https://user-images.githubusercontent.com/36862146/223499021-a77a44d9-17a6-4a10-b5dd-bbfe0a89685d.png)
