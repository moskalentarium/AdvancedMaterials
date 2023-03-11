# Advanced Glass
![Screenshot_9](https://user-images.githubusercontent.com/36862146/224485657-31476537-eb19-4f93-97e0-ae2712be63b4.png)

![Screenshot_8](https://user-images.githubusercontent.com/36862146/224485656-df892c1c-8fb0-4c19-9101-6dc2d40a9122.png)

## Ben Claward's Glass
![Screenshot_2](https://user-images.githubusercontent.com/36862146/224483469-29ff70a5-95d9-45c5-8fa1-ddcdc5d41e04.png)
![Screenshot_3](https://user-images.githubusercontent.com/36862146/224483473-f4486d8e-7827-404b-bfa1-aef5505bbf27.png)

Translucent, Surface TranslucencyVolume(most of the cost). Using value of 10 to push the Specular and make it extra reflective. 1.5 is the IOR of glass. SpiralBlur creates a frost effect on the glass. 64 steps would be your limit here. Unplug Emissive and plug 0.35 to opacity to create clear glass

![Screenshot_1](https://user-images.githubusercontent.com/36862146/224483476-1c7a3110-9f15-40f4-8ec1-5a2377265b43.png)

## Drawcall's Glass
### Additive
![Screenshot_5](https://user-images.githubusercontent.com/36862146/224484482-b63e6f5b-9bc3-454f-9ed3-ccafcf1879e9.png)

Additive Blend mode (you can go as fas as using Unlit Shading Model). We feed reflection vector into HDRI Texture Sample, adding brightness, tint, and opacity. Quite cheap

![Screenshot_4](https://user-images.githubusercontent.com/36862146/224484488-8b48aeb5-3a15-4235-8639-963a76ac9723.png)

### Translucent/Additive with refraction
![Screenshot_5](https://user-images.githubusercontent.com/36862146/224485127-7138cb80-37a1-4a85-af2d-293fe9e6488d.png)

Translucent or Additive didn't seem to change much, but Refraction adds complexity

![Screenshot_6](https://user-images.githubusercontent.com/36862146/224485126-237a6a83-0873-4b5e-a9ff-a1bc5b0b7617.png)

### Modulate Unlit
![Screenshot_11](https://user-images.githubusercontent.com/36862146/224485654-f270a6c9-4044-497e-96db-ce20162428a0.png)

This one would be the cheapest, though not producing the best results

![Screenshot_10](https://user-images.githubusercontent.com/36862146/224485659-28befc11-d73a-4de3-bd8a-0bb3ad7f0a6f.png)
