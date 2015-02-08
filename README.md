# Metal_VertexStreaming

This is a conversion to Swift of Apple's WWDC 2014 example MetalVertexStreaming.

This sample shows how to stream vertex data between 3 command buffers using one block of memory shared by both the CPU and GPU. The original data is copied back into the Metal shared CPU/GPU buffer every frame and modified directly in the buffer to animate the triangle. 

***

The key to this example is the staggered rendering and updating. 
When the Renderer is configured we create a buffer 3 times bigger
than needed for a single frame.

***
    func configure(view:TView)
    {
        //------------------------------------------------------------
        // MAKE THE BUFFER BIG ENOUGH FOR 3 RENDER FRAME CYCLES.
        //------------------------------------------------------------
        vertexBuffer = device!.newBufferWithLength( kMaxBufferBytesPerFrame, options: nil)
        vertexBuffer!.label = "Vertices"
    }
***


For each renderFrameCycle [0, 1, 2] copy our vertexData into a different part of the vertexBuffer.
This example uses 256 bytes for the our vertexData.
  
***
    func update(controller:TViewController)
    {
        //------------------------------------------------------------
        // this is pointer to start of our vertexBuffer.
        //------------------------------------------------------------
        var bufferPointer = vertexBuffer!.contents()  
        
        //------------------------------------------------------------
        // make a destination pointer by offsetting per frame.
        //------------------------------------------------------------
        var vData:UnsafeMutablePointer<Void> =
                    bufferPointer + 256 * renderFrameCycle 
                    
        //------------------------------------------------------------
        // now copy our data into a safe area of the  shared cpu/gpu buffer
        //------------------------------------------------------------
        memcpy(vData, vertexData, UInt(sizeof_vData))
    }
***


This code tells the shader where to receive the vertex data.

***
    //--------------------------------------------------------------------------
    func renderTriangle(renderEncoder:MTLRenderCommandEncoder, view:TView, name:String)
    {
        //------------------------------------------------------------
        // the current vertexBuffer is offset per renderFrameCycle [0, 1, 2]
        //------------------------------------------------------------
       renderEncoder.setVertexBuffer(
            vertexBuffer!,
            offset: Int(256 * renderFrameCycle),
            atIndex: Int(0) )
    }
    
***
note: I renamed this variable to renderFrameCycle.
The original program calls this variable constantDataBufferIndex.  
Which kind of weird since Index is a variable that changes every frame.
And we copy new data into the DataBuffer each frame.  
The only thing that is constant is the location of the vertexBuffer.





![](https://raw.githubusercontent.com/Jamnitzer/Metal_VertexStreaming/master/screen.png)




