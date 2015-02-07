# Metal_VertexStreaming

This is a conversion to Swift of Apple's WWDC 2014 example MetalVertexStreaming.

This sample shows how to stream vertex data between 3 command buffers using one block of memory shared by both the CPU and GPU. The original data is copied back into the Metal shared CPU/GPU buffer every frame and modified directly in the buffer to animate the triangle. 

![](https://raw.githubusercontent.com/Jamnitzer/Metal_VertexStreaming/master/screen.png)




