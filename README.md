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

We have now opened the shader graph window. In the bottom right we have a little preview of our final shader. In the top left we have our Blackboard, this is where our Properties and Keywords go that will make our shader customizable from within the editor and code (we'll get to this later). And in the middle we have a PBR Master node. This node is the final result of our shader.

![The Shader Graph Window](/Images/ShaderGraphWindow.PNG)

You can scroll in and out by using the scroll wheel. You can drag the view by holding alt+leftmouse or the middle mouse and then dragging your cursor. The blackboard and preview are always shown like a HUD and can be dragged around by their title and/or resized by the bottom right corner of their windows.

![Rezising the preview window](/Images/ResizingPreview.gif)

## Creating the glowing shader

### Setting a base color

We can now edit properties of the master node as you would normally edit properties of a material in the editor. For instance we can set the albedo to a nice blue color. Once you have done this, you will see that the preview will also update to reflect the new color.

![Changing the albedo settings](/Images/ChangingAlbedo.gif)

### Adding a glow

Now to make the object glow we need to make use of the emission property. However by just setting the emission to an RGB value of `255, 255, 255` will turn the entire preview  object white. This is not what we are trying to achieve.

Instead we will leave the emission RGB at `0, 0, 0` and create a new node. You can create a new node by right clicking on an empty space in the shader graph window and selecting `Create Node` or by pressing space when the shader graph window is focused. The effect we are looking for is called a 'Fresnel Effect', so we create a new Fresnel Effect node. We can use the Power property of the Fresnel Effect node to tone down the effect. Let's set the Power to 3 for now.

![Creating a new Fresnel Effect node](/Images/CreatingFresnelNode.gif)

After creating our Fresnel node we connect its output to the emision of the Master node by clicking and holding the left mouse on the output and dragging it to the emmision input. This also immediately updates our preview to reflect the changes.

![Connecting the Fresnel node to the emission](/Images/ConnectingFresnelToEmission.gif)

### Setting a glow color

Now let's add a color to our glowing effect. We start by adding a 'Color' node. We set a color to something random (I'm using `255, 133, 0`). Then we add another node called the 'Multiply' node. This node can be seen as taking two inputs and combining their results. If we click on the connection between the Fresnel and Master nodes and press `Delete` we can delete this connection. Now we can connect the outputs of the Color and Fresnel node to the input of the Multiply node. We then connect the output of Multiply to the emission of our Master node. The result should look something like this:

![Colored fresnel effect](/Images/ColoredFresnel.PNG)

To slightly bump up the glow we can change the mode on the Color node to HDR and set the intensity to +1.

![Setting the glow to an HDR effect](/Images/HDRFresnelColor.gif)

### Viewing the shader in the scene

Now that we have a good glowing effect we can apply it to one of the monkeys in the scene. First we save our shader by clicking on 'Save Asset' in the top left of our shader graph window.

Then we natigate to the `Materials/` folder in our project window. We select the `Monkey Left` material and at the top of the inspector we expand the Shader dropdown and select `ShaderGraphs > GlowShader`

![Changing the shader on a material](/Images/ChangingMaterialShader.gif)

This change should now also be reflected in our scene and game views.

![One of the monkeys is now glowing](/Images/GlowingMonkey.PNG)

### Exposing the glow color in the editor

Currently, if we want to change the color of the glow effect we would need to open up the shader, make a change to the color node and save our changes. It would be much nicer if we could make these changes from within the inspector.

We can do this by right clicking on our Color node and selecting 'Convert To Property'. This will change our node to refer to the property and the contents of the node will be copied to a property that now shows up on the blackboard.

![Converting the glow color to a property](/Images/ConvertingGlowColorToProperty.gif)

The 'Color' property will now show up in our blackboard. From here we can check if it is Exposed (checking this will make it show up in the inspector) and set some of the default values. We can also right click the property on the blackboard and rename it. We'll rename it to 'Glow Color'

![The converted glow color node as shown on the blackboard](/Images/ConvertedGlowColor.PNG)

### Changing the preview

Instead of switching to the scene view to view our changes on the monkey head, we could also change our preview to show what it would look like on the monkey head. We can change the look of the preview by right clicking on it and selecting 'Custom Mesh' and then selecting our monkey mesh. This will adjust the preview to apply our shader to the monkey instead of the default sphere. Once adjusted we can rotate the monkey by holding left click on the preview and dragging the mouse to rotate the preview.

![Adjusting the shader preview](/Images/AdjustingPreview.gif)

### Animating the effect

Let's now animate the glow effect over time. Start by freeing up some space by selecting the Fresnel, Color and Multiply nodes and dragging them away.

![Making some space within our shader graph window](/Images/MakingSpace.gif)

We'll create a new node called a 'Time' node. We'll also create a new Multiply node. Now we can delete the output of the old multiply node to the emission. Instead we will connect this up to the new multiply node. We'll also take the Sine Time of our Time node and hook it up to the new Multiply node. Lastly we connect our new multiply node to the emission of our Master node. It should look something like this:

![Creating a glow effect over time](/Images/GlowOverTime.PNG)

This adjustment has one small issue. Our glow will now turn off and on for a few seconds. This is because the value supplied by Sine Time will hover between -1 and 1. If we only want the glow to fade a bit, but not fully disappear we'll need to change the values supplied by Sine Time. We will delete the old connection from Sine Time to Multiply and add a new node in between these two. This node is called Remap. We supply the Sine Time output to the input of our Remap. We leave the 'In Min Max' at `-1, 1`, but we set our 'Out Min Max' to `0.2, 1`. This takes the values between -1 and 1 by Sine Time and maps them to a new range between 0.2 and 1. This new Remap node sits between our Time and Multiply like so:

![Fixing the glow over time effect](/Images/GlowOverTimeFixed.PNG)

### Working with textures

The last point of our glow shader is to work with textues. We will start by adding a new property in our blackboard with a type of 'Texture2D' and naming it 'Occlusion'. We then assign the `Monkey_Occlusion` texture to the Default value.

![Adding a new property for the occlusion texture](/Images/AddingOcclusionTextureProperty.gif)

We can drag our new Occlusion property from the blackboard into the graph. However we cannot directly connect our Occlusion property to the Occlusion on the master node. The value from our blackboard property needs to be converted first. We do this by creating a new node 'Sample Texture 2D'. This node sits between our blackboard property and the Master node like so:

![Connecting the Occlusion texture to the Master node](/Images/GlowMonkeyOcclusion.PNG)

Now we can save the shader and go back to our material in the inspector. Here we can edit our Occlusion property to make use of the `Monkey_Occlusion` texture. Switching between our texture and `None` while looking at the scene view will let you see the change between the default monkey and the one with an ambient occlusion effect added onto it. This is our finalized glow shader. Next up is the dissolve shader.

## Creating the dissolving shader

We'll start by adding another PBR graph shader under our `Shaders/` folder and name it 'DissolveShader'. We'll open it up and go ahead and change the preview to use our monkey mesh again. We can also change our albedo to a darker color to make our dissolve effect stand out more. Let's set the albedo to `46, 46, 46`. With this we can start on our dissolve shader.

### Generating noise and setting transparency

To start off we need some noise to make random parts of our model transparent. We can use the Simple Noise node to generate some random noise. Add this Simple Noise node and set the Scale to 30. You'll see the cloudy texture that gets generated on the bottom of the node. We can then connect the output of our Noise node to the Alpha of our Master node. This change won't show in the preview however. To show the change in the preview we need to allow our shader to be transparent. We can do this by selecting the Master node and clicking on the cog, then clicking on the Surface dropdown and switching to Transparent.

![Making the master node transparent](/Images/MasterNodeTransparency.gif)

Looking at the preview you can see that the monkey is now a bit transparent. This is not the effect we're looking for though. So we'll revert our change by setting the Surface back to Opaque. Instead we want a clear cutoff point. This is where AlphaClipThreshold comes in.

### All or nothing transparency

The AlphaClipThreshold is a simple number between 0 and 1. It will take the alpha value of every point on the texture and see if it is below the threshold. If the alpha value is below the threshold then that point will be completely transparent.

So let's supply this threshold with a number then. Create a new 'Vector 1' node. This node is basically just a float. Connect this node to the AlphaClipThreshold on the Master node. If we now adjust the value of this Vector1 node you can see the preview start to dissolve.

![Manually dissolving the monkey by sliding the alpha threshold](/Images/ManuallyDissolveMonkey.gif)

### Automating the dissolving

Right now we need to manually change the value for how much we want to dissolve our monkey. We could expose this value to the inspector by converting the Vector1 node to a property, or we can automate it over time. Once again we'll choose to animate it over time. Start off by deleting the Vector1 node. We'll put a Time node in its place. Hook the Sine Time of the Time node up to a Remap node at default values, then pipe this Remap into the AlphaClipThreshold on the Master node like so:

![Animating the dissolving over time](/Images/AutomatedDissolve.PNG)
