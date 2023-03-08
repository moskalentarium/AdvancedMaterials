# Snowy Rock with Material Functions
![Screenshot_1](https://user-images.githubusercontent.com/36862146/223756219-d98a9ed0-35c6-439a-8ed6-bd06180baf2c.png)
## Description
This advanced material is based on RockShader and SparklingSnow materials. This project aims to combine those materials by utilizing Material Functions. Outputs and Inputs can be sorted for convenience (less is top). Color is vec3, Texture is texture2D, Grayscale and numbers are Scalar. "Use Preview Value as Default" for inputs if those inputs left unplugged
## Steps
### Unreal Material
- We will create 4 Material Functions: RockBase, RockDetail, Snow, and SnowMask. Material Function include adjusted values only

![RockSnowy](https://user-images.githubusercontent.com/36862146/223756215-1182c330-ecbd-4fe2-a816-2ddd1f2e2dea.png)

- RockBase: combining nodes for convenience
	- ObjectScale to transform UVs of tiled _CR. RockScale parameter is exposed, so that we can control the Texel Density over all the rocks
	- TextureSamples are parameters
	- Outputs give us global UVs of sorts, Rock color, roughness, AO, and Normal

![RockBase](https://user-images.githubusercontent.com/36862146/223756208-6a873111-24dc-4db0-9fa0-bf70be5ddf12.png)

- RockDetail: combining nodes to re-use them conveniently
	- Through RockDetail we adjust Roughness and Normal of a rock by ADDing data from textures. We need Roughness, Normal, UV, Scale, and DetailTexture inputs
	- Pretty straightforward, just copied from RockShader. Outputting Roughness and Normal

![RockDetail](https://user-images.githubusercontent.com/36862146/223756214-37e13ba1-631f-4481-a4a9-03a8359a9c5f.png)

- SnowMask: Masking top (Z+) through a Lerp
	- Normal Input here is combined Rock Normal to introduce more detail. Coverage is a 0:1 Scalar

![SnowMask](https://user-images.githubusercontent.com/36862146/223756249-52f911fa-d0ce-4cab-a3cc-afbc947fff41.png)

- Snow: combining nodes from SparklingSnow for convenience
![Snow](https://user-images.githubusercontent.com/36862146/223756243-4bb5ee5b-70aa-4b1d-97ca-5582d937fc20.png)

	- Color. Pasted nodes, combining based on SnowMask via Lerp
	![Snow Color](https://user-images.githubusercontent.com/36862146/223756229-51319544-6e75-4d76-9a3d-cfd3f5b39ffa.png)
	
	- SnowMask, Normal, AO, and Roughness. Blending Snow Normals together, then Blending result with RockBase Normal Map - we don't want to inroduce Macro/Micro normals as they'd covered by the snow. Blue and Alpha Lerping with Rock data
	![Snow NOR](https://user-images.githubusercontent.com/36862146/223756239-7ac08af5-4ed5-4a90-a7ec-5d9af5aad6b6.png)
	
	- Emission. Pasted nodes, combining based on SnowMask via Multiply
	![Snow Emission](https://user-images.githubusercontent.com/36862146/223756235-8617237f-5c9e-4d69-ac75-193468cb756f.png)
	
### Final setup once again
![RockSnowy](https://user-images.githubusercontent.com/36862146/223756215-1182c330-ecbd-4fe2-a816-2ddd1f2e2dea.png)
