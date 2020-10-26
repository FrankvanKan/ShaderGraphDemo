# Shader Graph Demo

Shader Graphs are a new method of creating your own shaders using a visual node based editor. No code required :)

This readme will teach you the basics of shader graphs by creating three shaders of your own.
1. [A glowing effect](#creating-our-first-shader)
2. [A dissolving effect](#creating-the-dissolve-shader)
3. [A hologram effect](#creating-the-hologram-shader)

![The final three shaders](/Images/FinalResult.gif)

This GitHub repository contains a basic unity project with some models and materials that we can start working on.

![Scene and Project Layout](/Images/SceneProjectLayout.PNG)

Of note are the three monkeys in the scene `Scenes/Shaders.unity`. Each monkey has its own material in the `Materials/` folder. The `Textures/` folder contains some textures that we will be working with during the demo. `Models/` contains the monkey model. And the rest of the folders are some standard assets needed for a URP project, those can be ignored.

## Creating our first shader

We will start by creating a new folder under `Assets/` called `Shaders`. In this folder we use the create menu to create a new PBR Graph by going to `Create > Shader > PBR Graph`. (This create menu also contains the classic methods such as surface and compute shaders)

![Creating a new shader](/Images/CreateNewShader.gif)

Rename the shader to `GlowShader` and open it by double clicking on it.

## The shader graph window

We have now opened the shader graph window. In the bottom right we have a preview of our final shader and in the top left we have our Blackboard, this is where our Properties and Keywords go that will make our shader customizable from within the editor and code (we'll get to this later). Inn the middle we have our PBR Master node. This node takes all the inputs that will shape the final result of our shader.

![The Shader Graph Window](/Images/ShaderGraphWindow.PNG)

You can scroll in and out by using the scroll wheel. You can drag the view by holding alt+leftmouse or the middle mouse and then dragging your cursor. The blackboard and preview are always shown like a HUD and can be dragged around by their title and/or resized by the bottom right corner of their windows.

![Rezising the preview window](/Images/ResizingPreview.gif)

## Creating the glowing shader

We will start off by making a shader that will add a glowing edge around objects. Aditionally we'll make this edge pulse over time.

### Setting a base color

Starting off, we can now edit properties of the master node as you would normally edit properties of a material in the editor. For instance we can set the albedo to a nice blue color. Once you have done this, you will see that the preview will also update to reflect the new color.

![Changing the albedo settings](/Images/ChangingAlbedo.gif)

### Adding a glow

Now to make the object glow we need to make use of the emission property. However, just setting the emission to an RGB value of `255, 255, 255` will turn the entire preview  object white. This is not the effect we are trying to achieve.

Instead we will create a new node. You can create a new node by right clicking on an empty space in the shader graph window and selecting `Create Node`, or by pressing space when the shader graph window is focused. The effect we are looking for is called a 'Fresnel Effect', so we create a new Fresnel Effect node. We can use the Power property of the Fresnel Effect node to tone down the effect. Let's set the Power to `3` for now.

![Creating a new Fresnel Effect node](/Images/CreatingFresnelNode.gif)

After creating our Fresnel node we connect its output to the emision of the Master node by clicking and holding the left mouse on the output and dragging it to the emmision input. This also immediately updates our preview to reflect the changes.

![Connecting the Fresnel node to the emission](/Images/ConnectingFresnelToEmission.gif)

### Setting a glow color

Now let's add a color to our glowing effect. We start off by adding a new 'Color' node. We can set a color to something random (I'm using `255, 133, 0`). Then we add another node called the 'Multiply' node. This node can be seen as taking two inputs and combining their results. If we click on the connection between the Fresnel and Master nodes and press `Delete` we can delete this connection. Now we can connect the outputs of the Color and Fresnel node to the inputs of the Multiply node. We then connect the output of Multiply to the emission of our Master node. The result should look something like this:

![Colored fresnel effect](/Images/ColoredFresnel.PNG)

To slightly bump up the glow we change the mode on the Color node to HDR and set the intensity to +1.

![Setting the glow to an HDR effect](/Images/HDRFresnelColor.gif)

### Viewing the shader in the scene

Now that we have a nice glowing effect we can apply it to one of the monkeys in the scene. But first, we need to save our shader by clicking on 'Save Asset' in the top left of our shader graph window.

Then we natigate to the `Materials/` folder in our project window. We select the `Monkey Left` material and at the top of the inspector we expand the Shader dropdown and select `ShaderGraphs > GlowShader`

![Changing the shader on a material](/Images/ChangingMaterialShader.gif)

This change should now also be reflected in our scene and game views.

![One of the monkeys is now glowing](/Images/GlowingMonkey.PNG)

### Exposing the glow color in the editor

Currently, if we want to change the color of the glow effect we would need to open up the shader, make a change to the color node and save our changes. It would be much better if we could make these changes from within the inspector.

We can do this by going back into our shader, right clicking on our Color node and selecting 'Convert To Property'. This will move the values of the node into a property on the blackboard. It will then change the node to represent a copy of the property instead. These properties can now be used in multiple places in the graph and the values can be edited from within the inspector.

![Converting the glow color to a property](/Images/ConvertingGlowColorToProperty.gif)

The 'Color' property will now show up in our blackboard. From here we can set the checkox to Expose the property (checking this will make it show up in the inspector) and set some of the default values. We can also right click the property on the blackboard and rename it. We'll rename it to 'Glow Color'.

![The converted glow color node as shown on the blackboard](/Images/ConvertedGlowColor.PNG)

### Changing the preview

Instead of switching to the scene view to view our changes on the monkey head, we could also change our preview to reflect what our shader would look like on the monkey. We can change object in the preview by right clicking on it, selecting 'Custom Mesh' and then selecting our monkey mesh. This will adjust the preview to apply our shader to the monkey instead of the default sphere. Once adjusted we can rotate the monkey by holding left click on the preview and dragging the mouse to rotate the preview.

![Adjusting the shader preview](/Images/AdjustingPreview.gif)

### Animating the effect

Let's now animate the glow effect over time. Start by freeing up some space by selecting the Fresnel, Color and Multiply nodes and dragging them away.

![Making some space within our shader graph window](/Images/MakingSpace.gif)

We'll create a new node called a 'Time' node. We'll also create a new Multiply node. Now we can delete the output of the old multiply node to the emission. Instead we will connect this up to the new multiply node. We'll then take the Sine Time of our Time node and hook it up to the new Multiply node. Lastly we connect our new multiply node to the emission of our Master node. It should look something like this:

![Creating a glow effect over time](/Images/GlowOverTime.PNG)

This adjustment has one small issue. Our glow will now turn negative for a few seconds. This is because the value supplied by Sine Time will hover between -1 and 1. If we only want the glow to fade out partially, we'll need to do something about the values supplied by Sine Time. We will delete the old connection from Sine Time to Multiply and add a new node in between these two. This node is called Remap. We supply the Sine Time output to the input of our Remap. We leave the 'In Min Max' at `-1, 1`, but we set our 'Out Min Max' to `0.2, 1`. This takes the values between -1 and 1 by Sine Time and maps them to a new range between 0.2 and 1. This new Remap node sits between our Time and Multiply like so:

![Fixing the glow over time effect](/Images/GlowOverTimeFixed.PNG)

### Working with textures

The last thing we'll add to our shader is a nice ambient occlusion effect to make the glow pop out a bit more. We will start by adding a new property in our blackboard with a type of 'Texture2D' and naming it 'Occlusion'. We then assign the `Monkey_Occlusion` texture to the Default value.

![Adding a new property for the occlusion texture](/Images/AddingOcclusionTextureProperty.gif)

We can drag our new Occlusion property from the blackboard into the graph. However, we cannot directly connect our Occlusion property to the Occlusion on the master node. The value from our blackboard property needs to be converted first. We do this by creating a new node 'Sample Texture 2D'. This node sits between our blackboard property and the Master node like so:

![Connecting the Occlusion texture to the Master node](/Images/GlowMonkeyOcclusion.PNG)

Now we can save the shader and go back to our material in the inspector. Here we can edit our Occlusion property to make use of the `Monkey_Occlusion` texture. Switching between our texture and `None` while looking at the scene view will let you see the change between the default monkey and the one with an ambient occlusion effect added onto it. This is our finalized glow shader. Next up is the dissolve shader.

## Creating the dissolving shader

We'll start by adding another PBR graph shader under our `Shaders/` folder and name it 'DissolveShader'. We'll open it up and go ahead and change the preview to use our monkey mesh again. We can also change our albedo to a darker color to make our dissolve effect stand out more. Let's set the albedo to `46, 46, 46`. With this we can start on our dissolve shader.

### Generating noise and setting transparency

To start off we need some noise to make random parts of our model transparent. We can use the Simple Noise node to generate some random noise. Add this Simple Noise node and set the Scale to 30. You'll see the cloudy texture that gets generated on the bottom of the node. We can then connect the output of our Noise node to the Alpha of our Master node. This change won't show in the preview however. To see the changes we need to allow our shader to be transparent. We can do this by selecting the Master node and clicking on the cog, then clicking on the Surface dropdown and switching it to Transparent.

![Making the master node transparent](/Images/MasterNodeTransparency.gif)

Looking at the preview you can see that the monkey is now a tiny bit transparent with a cloudy pattern. This is not the effect we're looking for. So we'll revert our change by setting the Surface back to Opaque. Instead we want a clear cutoff point. This is where AlphaClipThreshold comes in.

### All or nothing transparency

The AlphaClipThreshold is looking for a simple number. It will take the alpha value of every point on the texture and compare them to the threshold. If the alpha value is below the threshold then that point will be completely transparent.

So let's supply this threshold with a number then. Create a new 'Vector 1' node. This node is basically just a float. Connect this node to the AlphaClipThreshold on the Master node. If we now adjust the value of this Vector1 node you can see the preview start to dissolve.

![Manually dissolving the monkey by sliding the alpha threshold](/Images/ManuallyDissolveMonkey.gif)

### Automating the dissolving

Right now we need to manually change the value for how much we want to dissolve our monkey. We could expose this value to the inspector by converting the Vector1 node to a property, or we can automate it over time. Once again we'll choose to animate it over time. Start off by deleting the Vector1 node. We'll put a Time node in its place. Hook the Sine Time of the Time node up to a Remap node at default values, then pipe this Remap into the AlphaClipThreshold on the Master node like so:

![Animating the dissolving over time](/Images/AutomatedDissolve.PNG)

We have now automated the dissolve effect to loop over time. To improve the looks of our effect we want to create a glowing edge around the parts that are dissolving. So let's get started on that.

### The Step node

We'll start off by adding a new node called a 'Step' node. We hook the output of the Simple Noise node up to the Edge input of the Step node. Leave it so that the output of Simple Noise is going to **both** the Step node and the Alpha of the Master node. The step node essentially functions the same as the AlphaClipThreshold of the Master node. If you play around with the In value of the Step node you'll see the same kind of dissolving pattern appear as what you would see on the preview. If we now hook up the output of the Remap node to the In of the Step node it'll produce the exact same pattern as what would be applied to the Master node. We can now hook up this pattern from the step function to the Emission of the master node to make it glow in the same pattern as what is being dissolved.

![Progress on the step node](/Images/DissolveStepNode.PNG)

### Offsetting the Step node

Our changes don't show any glowing effect though. And that is because the parts that are glowing are also the parts that happen to be transparent. We need to slightly offset the glowing effect to bleed out of the dissolved edges. We can delete our connection between the Remap and Step nodes and create a new Add node in between. Take the result of the Remap node and input it into the Add node, then take the output from this Add node and hook it up to the Step node. Then set the other value of the Add node to something small like `0.01`. Now you'll see a glowing white edge around your dissolve effect.

(Tip: You can collapse a node to make it smaller by hovering over it and clicking on the `^` arrow in the output preview)

We can also expose the value that goes into our Add node to make it able to edit the glow thickness from within the inspector. Let's add a Vector1 property on our blackboard, drag it into our graph and hook it up to our Add node. The result of these changes should look like this:

![Offsetting the glowing effect](/Images/DissolveOffset.PNG)

### Adding a color to the glow

It would look better if we could make our edges glow in different colors. So let's add this functionality. First we'll delete the connection between our Step and Master nodes. Instead we'll create a new Color node and combine the results of this node with the output of the Step node in a new Multiply node. This Multiply node will then form the new connection to the Master node emission. We can change the Mode on the Color node to HDR to allow for really bright values and let's set the value to a light blue of `0, 82, 255` with an intensity of `4`.

![Changing the color of our dissolve](/Images/ColoredDissolve.PNG)

### Finishing up

We're pretty much done with our dissolve shader. There's just a few more things to adjust.

It would look better if we can see the back of the object while it is dissolving. We can do this by clicking the cog of the Master node and checking the 'Two Sided' checkbox.

We can also expose some values to the inspector to make editing the effect easier. Let's start by right clicking on the Color node and converting it to a property. We can rename it to 'Edge Color'.

Let's also create a new Vector1 property on the blackboard called 'Noise Scale', set the default value to `30`, drag it into the graph and hook it up to the Scale of the Simple Noise node.

Finally we'll save the shader by clicking 'Save Asset' at the top left of the shader graph window. Then we'll go to our `Monkey Center` material in the `Materials/` folder and change the shader to our new Dissolve Shader. Now you can play around with some of the values and watch them in the scene and game views. (The effect won't update unless holding left or right click in the scene view, or by entering play mode)

## Creating the hologram shader

Once again we start off by creating a new PBR Graph under the `Shaders/` folder and naming it `HologramShader`. We will expose our albedo to the inspector by adding a new Color property on the blackboard called 'Base Color' and connecting that property up to the Albedo of the Master node. Let's also go ahead and set the default of the Base Color to `110, 205, 125`, a dull green. Finally we'll change the preview to the monkey again.

### Texture Transparency

Holograms usually appear projected with these kinds of horizontal lines like some old CRT TV. We'll use a texture with these lines on it to make parts of our material transparent. We start off by adding a new property on the blackboard of type Texture2D. Rename the new property to `Hologram Texture` and set the default texture to `HologramLines_Simple` or `HologramLines_Cool`.

Once again we need to pipe our texture through a Sample Texture 2D node before adding it to the Alpha of our Master node. We also need to click the cog on our Master node and set the Surface to Transparent.

![Setting our hologram shader to make parts of the material transparent](/Images/HologramTextureTransparency.PNG)

### Drawing lines horizontally across the mesh

We have now ended up with mapping the texture onto the UV of the monkey. This creates a bunch of lines going in all different directions and is how you would usually apply textures. But in this case we want the lines to kind of scroll across from top to bottom.

To do this we start off by creating a new 'Tiling And Offset' node. We connect the output of this node to the UV of our Sample Texture 2D node. Then we create a new 'Screen Position' node and connect the output to the UV input of the Tiling And Offset node. The preview should now show lines going horizontally across the monkey instead of wrapping around it.

![Adjusting the UV of the hologram](/Images/AdjustingHologramTexture.PNG)

It would be nice if we could adjust the tiling of our texture in the inspector. To do this we'll create a new property on the blackboard of type Vector2, name it 'Hologram Texture Tiling', set the default to `1, 3` and connect it up to the Tiling of our Tiling And Offset node. We'll also collapse the Screen Position node to tidy up our graph.

![Tidied up our texture tiling](/Images/TidiedHologramTexture.PNG)

### Scrolling the texture

Just as we can adjust the tiling of our texture through the Tiling And Offset, so can we adjust the offset of the texture. We can do this by simply adding a Time node and connecting the Time output to the Offset of the Tiling And Offset node. Now you should be able to see the texture scrolling across the monkey in the preview.

It would be nice to be able to control the scroll speed though. To do this we will put a Multiply node between Time and Tiling And Offset. We add a new Vector1 property to the blackboard called 'Scroll Speed' with a default value of `0.1`. We'll also hook this Scroll Speed up to the Multiply node.

![Adjusting the offset over time to make the texture scroll](/Images/ScrollingHologramTexture.PNG)

### Applying the shader

Our shader should now look like this: 

![Full scrolling hologram](/Images/ScrollingHologramFull.PNG)

We can go back into the editor and set the shader on our `Right Monkey` material to the HologramShader. Now you can play around with the color, texture and scrolling speed in the inspector.

We'll continue to enchance the effect of the shader.

### Hologram glow

Let's enchace our hologram by adding a bit of a glow. First we'll create a new Color node with an HDR mode and an RGB of `255, 92, 0` and an intensity of `5`. We'll also add a Fresnel Effect node with a power of `3` and combine it with our color through a Multiply node. Then we'll take the output of that Multiply node and connect it to the Emission of our Master node.

![Creating a glow around the hologram](/Images/HologramGlow.PNG)

The preview is a bit dull, but if we look at it in the editor it is way too bright. So let's start off by converting the Color node to a property and naming it 'Glow Color'. We'll then also lower the brightness a bit by lowering the intensity of our new property back down to `2`.

### Enhancing the glow

We can enhance the glow a bit by taking the Sample Texture 2D output and sending it to a One Minus node alongside the Master node. This node inverts the input and sends it back out. We can combine the result of this One Minus with the Base Color property in a Multiply node. Then we combine this Multiply node with the colored Fresnel in a new Add node. Finally we output this Add node into the Emission of the Master node. This should make the lines on our hologram also glow to stand out a bit more.

![Enhancing the glow](/Images/EnhancedHologramGlow.PNG)

### Flickering glow

Lastly we can make our glow flicker. We start off with a Time node with the Time output going into a Random Range node with a min, max of `0, 1`. This Random Range node outputs into a Comparison node that compares Random Range to `0.9` using a `Greater` comparison. This Comparison then connects to the Predicate of a Branch node that output `1` for True and `0.8` for False. The branch outputs into a Multiply node that combines the effects of the flicker and the previous emission output.

![Making the hologram flicker](/Images/FlickeringHologram.PNG)

## End note

And with that we have finalized our three shaders. Shader graph is a powerful tool to quickly create complex shaders using simple operations. Due to it's visual nature it can also be understood and used by artists. Absolutely feel free to experiment with some of the nodes to create your own shaders and effects.
