# Advanced Materials
## The Graphics Pipeline

1. The data starts out on a CPU, where a 3D **program passes vertex data** through DirectX, OpenGL, etc. to the Graphics Driver
2. The **Graphic Driver** then **sends the data to the GPU's Front End**, which is a graphic chip designed for communication with the CPU
3. The data then gets sent to the **Programmable Vertex Processor** - where the **Vertex Shader** runs
    
    The vertex data consists of:
        - Vertex Positions on Object Space
        - UV coordinates
        - Vertex Colors
        - Normals

The most important thing that Vertex Processor does is **Transforming the Vertex Position form Object Space to Screen Projection Space, so it can be turned into triangles and drawn onto the screen**. Vertex Shader can also animate the positions of the verticves, scroll the UV coordinates, bend the Vertex Normals, and calculate lighting and store it in the Vertex Colors. 

    Most of the adjustments happen on per pixel basis, so if you have operations than can be moved from Pixel Shader to Vertex Shader, it can boost the performace

4. **Primitive Assembly**. The Graphics Chip **connects** all of the **vertices** together **to create triangles**. So **the result of the Vertex Shader** combined with **Primitive Assembly** is **triangles in Screen Projection Space**
5. Then, the data gets passed to the **Rasterization & Interpolation Unit**. The job of this hardware is to **convert the triangles into pixels** on the screen. The other thing that happens here is **Interpolation**: the **data** that was stored in each of the vertices is smoothtly **interpolated to the pixels** (Vertex Color, Normals, UV coordinates)
6. The interpolated data is sent to the **Programmable Pixel Processor**. This is where the **Pixel Shader** runs: it taked the incoming data from the **Vertex Shader and additional input data from the CPU to create the final results**. Traditionally, the output of the Pixel Processor has been the final colored pixel for the screen, and this then goes for...
7. ...**Raster Processing** before getting output to the screen
8. However, most modern game engines use Deffered Lighting, which means that the **Pixel Processor outputing Color, Roughness, Metal mask, Normal data** instead of just a color to add to the screen. This data is written **onto internal G-Buffer** and then the final **Lighting calculations happen** with the data from those buffers

## Moving math to Vertex Shader in Unreal
If you do have a lot of math that you're doing on Position, Normal, or UVs, you can benefit form using the VertexInterpolator node to force those calculations from PS to VS
