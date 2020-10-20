# Shader Graph Demo

Shader Graphs are a way to create your own shaders using a visual node based editor. No code required :)

This readme will go through the steps to create your own shaders. Over the course of the demo we will be creating three unique shaders.
1. A glowing effect
2. A dissolving effect
3. A hologram effect

The included project contains a basic scene, some models and some materials to start working on.

![Scene and Project Layout](/Images/SceneProjectLayout.PNG)

Of note are the three monkeys in the scene `Scenes/Shaders.unity`. Each has his own material in the `Materials/` folder. The `Textures/` folder contains some textures that we will be working with during the demo. `Models/` contains the monkey model. The rest of the folders are some standard assets needed for a URP project and can be ignored.

## Creating our first shader

We will start by creating a new folder under `Assets/` called `Shaders`. In this folder we use the create menu to create a new PBR Graph by going to `Create > Shader > PBR Graph`. (This create menu also contains the classic methods such as surface and compute shaders)

![Creating a new shader](/Images/CreateNewShader.gif)

Rename the shader to something along the lines of `GlowShader` and open it by double clicking on it.

## The shader graph window

We have now opened the shader graph window. In the bottom right we have a little preview of our final shader. In the top left we have our shader properties and keywords, something we can use to make our shader interactable in the editor (we'll get to this later). And in the middle we have a PBR Master node. This node is the final result of our shader.

![The Shader Graph Window](/Images/ShaderGraphWindow.PNG)

You can scroll in and out by using the scroll wheel. You can drag the view by holding alt+leftmouse or the middle mouse and then dragging your cursor. The windows of the shader preview and the shader properties are always shown like a HUD and  can be dragged around by their title or resized by the bottom right corner of their windows.

![Rezising the preview window](/Images/ResizingPreview.gif)

## Creating a glowing effect

We can now edit properties of the master node as you would normally edit properties of a material in the editor. For instance we can set the albedo to a nice blue color. Once you have done this, you will see that the preview will also update to reflect the new color.

![Changing the albedo settings](/Images/ChangingAlbedo.gif)

Now to make the object glow we need to make use of the emission property. However by just setting the emission to an RGB value of `255, 255, 255` will turn the entire preview  object white. This is not what we are trying to achieve.

Instead we will leave the emission RGB at `0, 0, 0` and create a new node. You can create a new node by right clicking on an empty space in the shader graph window and selecting `Create Node` or by pressing space when the shader graph window is focused. The effect we are looking for is called a 'Fresnel Effect', so we create a new Fresnel Effect node. We can use the Power property of the Fresnel Effect node to tone down the effect. Let's set the Power to 3 for now.

![Creating a new Fresnel Effect node](/Images/CreatingFresnelNode.gif)

After creating our Fresnel node we connect its output to the emision of the Master node by clicking and holding the left mouse on the output and dragging it to the emmision input. This also immediately updates our preview to reflect the changes.

![Connecting the Fresnel node to the emission](/Images/ConnectingFresnelToEmission.gif)
