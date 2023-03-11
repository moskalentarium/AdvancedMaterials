# Advanced Volumetric Ice
![Screenshot_1](https://user-images.githubusercontent.com/36862146/224298260-45a9d05d-76f0-4149-94e6-6f128ebb7a63.png)

## Description
Here are a couple of unique things about this shader. First, it doesn't use refraction, it offsets SceneColor to create that effect. Next, it simulates Volumetric properties, like textures are pushed in
## Steps
### Unreal Material
- Simple Ice Roughness texture from cc0textures set to Grayscale. Normal Map is the baked map for that rock
- Shader is Translucent, Surface TranslucencyVolume
![Screenshot_5](https://user-images.githubusercontent.com/36862146/224298259-114d4929-a741-4ce6-9d32-5b3c942a3db4.png)

- As I said, it uses the SceneColor to simulate refraction. To achieve that we also need to know the normals in ViewSpace and the ScreenPosition. Color then is Multiplied by the BackgroundTint and Lerped with InternalVolumetricTint via a mask
![Screenshot_2](https://user-images.githubusercontent.com/36862146/224298248-dae5b4fc-e465-4232-a0ae-8ff3295ca383.png)

- Here we will try to achieve an effect of the textures being inside a Mesh, while being projected from the camera view. CustomReflectionVector uses a normal map to generate a reflection vector independent of the default reflection vector and the normal's input on the base shader. Then we Mask vec3 to 2xVec2 
![Screenshot_4](https://user-images.githubusercontent.com/36862146/224300788-94f037b6-36cf-4324-8c4c-87b8c0036556.png)

- This is the RG Mask, that changes based on Camera input

![Screenshot_7](https://user-images.githubusercontent.com/36862146/224303497-aed231b9-cc32-44a0-aac5-cb37a1a6ffd6.png)

- Next up, we use Depth to determine how deep to drive the texture in, 2048 is the texture size
![Screenshot_3](https://user-images.githubusercontent.com/36862146/224300784-fe024c7c-fa20-41a2-9eb3-991d1afc9a77.png)

- This iis the Multiply node before we Add the TextureCoord node, that changes based on Camera input

![Screenshot_8](https://user-images.githubusercontent.com/36862146/224303492-a5982e5c-6d55-4294-8f48-b09ff3700e12.png)


### Comments
Here's what people say about TAA issues
![Screenshot_6](https://user-images.githubusercontent.com/36862146/224300777-076bb625-c254-4f93-b02b-c33c8c225931.png)
