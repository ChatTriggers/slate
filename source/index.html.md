---
title: ct.js Tutorial

language_tabs: # must be one of https://git.io/vQNgJ
  - javascript

toc_footers:
  - <a href='http://chattriggers.com'>ChatTriggers Website</a>
  - <a href='https://discordapp.com/invite/0fNjZyopOvBHZyG8'>ChatTriggers Discord</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>
  
includes:
- triggers

search: true
---

# Introduction

ct.js is a framework for Minecraft Forge that allows for mods to be scripted, in languages such as JavaScript.
Scripts are able to be hot reloaded, which means you can make changes to your mod without restarting!

<aside class="warning">Currently, only JavaScript is supported.</aside>

JavaScript scripts are executed with Java's [Nashorn](http://openjdk.java.net/projects/nashorn/) library,
which means you can use all extensions it includes, found [here](https://wiki.openjdk.java.net/display/Nashorn/Nashorn+extensions).

<aside class="success">JavaScript <em>can</em> access Minecraft classes.</aside>

# Setup

To setup a ct.js coding environment, all you have to do is put the ct.js jar into your `.minecraft/mods` folder, and launch 
Minecraft. In your mods folder, you will get a folder structure automatically created. The structure should be<br/>
 `.minecraft/mods/ChatTriggers/Imports` and `.minecraft/mods/ChatTriggers/libs`.
 
# Creating an Import

To create an import, create a folder in your `.minecraft/mods/ChatTriggers/Imports` folder, and have it's name be the name
of your import. Our import will be called Example. Our folder structure now looks like<br/> `.minecraft/mods/ChatTriggers/Imports/Example/`.

We now need to create our scripts, so create a file in the folder named whatever you would like, the name is only for your
own management of the import. We'll call our main file `main`.

<aside class="notice">
Make the extension of the file be the normal extension for your specified language, i.e. main.js for JavaScript.
</aside>

# The Basics

## Registering a Trigger

>We register a WorldLoad trigger like so:

```javascript
TriggerRegister.registerWorldLoad("exampleImportWorldLoad");
```

The base of ct.js imports are "Triggers". These are events that get fired when a certain action happens in game,
like a sound is played, or chat message is received. A full list of these is at the <a href="#triggers">bottom of the page</a>.
 
So, we want to start of by listening to one of these, let's start with one of the simplest, WorldLoad.

What we're doing here is calling a method in the "TriggerRegister" class that registers "exampleImportWorldLoad" as a trigger
to be activated when the world is loaded. However, there is no function with that name yet!

## Receiving the Event

>We create the function to be called by the trigger like so:

```javascript
TriggerRegister.registerWorldLoad("exampleImportWorldLoad");

function exampleImportWorldLoad() {
  
}
```

Everything inside of this function is ran when the world loads. From here we can call other methods, interact with Minecraft,
and many other things. 

<aside class="notice">Multiple triggers can refer to one function.</aside>

## Responding to an Event

>For code to be activated by a trigger, simply put it in your function called by that registered trigger

```javascript
TriggerRegister.registerWorldLoad("exampleImportWorldLoad");

function exampleImportWorldLoad() {
  ChatLib.chat("&6Gold Text says that the world has just loaded!");
}
```

One of the things we can do inside of function is send a message to the player.

The best way to interact with Minecraft's chat is via the `ChatLib` class. It is accessed very simply through the
variable ChatLib. A list of the methods it provides can be found in the documentation [here](http://ct.kerbybit.com/ct.js/com/chattriggers/ctjs/libs/ChatLib.html).

## A more complicated Trigger

> A chat trigger that gets fired on the chat message <code>&lt;FalseHonesty&gt; Hello World!</code>

```javascript
TriggerRegister.registerChat("exampleImportChat").setChatCriteria("<${*}> ${message}");

function exampleImportChat(message) {
  
}
```

This function receives chat messages based on a certain criteria. If a received chat message follows the pattern
put in `.setChatCriteria()`, then the function will be called. `${variableName}` creates a variable in the criteria, and when the event
is fired, its value is passed into the function.

`TriggerRegister.registerChat()` and `.setChatCriteria()` return the created trigger, so you can chain configuration methods.
A list of these are found in the documentation for each specific trigger type, you can find the available modifications
[here](http://ct.kerbybit.com/ct.js/com/chattriggers/ctjs/triggers/TriggerRegister.html#registerChat-java.lang.String-).

<aside class="notice"><code>${*}</code> creates a variable that isn't passed into the function</aside>

## Canceling events

> To cancel a cancelable event, do this:

```javascript
TriggerRegister.registerChat("exampleImportChat").setChatCriteria("<${*}> ${message}");

function exampleImportChat(message, event) {
  event.setCanceled(true);
  
  ChatLib.chat("I did cancel this: " + message + "!");
}
```

> The event parameter is optional, so this works just the same if you don't need to use the event

```javascript
TriggerRegister.registerChat("exampleImportChat").setChatCriteria("<${*}> ${message}");

function exampleImportChat(message) {
  ChatLib.chat("I didn't cancel this: " + message + "!");
}
```

Some events are cancelable, like chat events. If it is cancelable, the last parameter passed into the function will always
be the event

# Rendering

<aside class="notice">All rendering coordinates start at the top left of the screen!</aside>

## Setting up

> Function to be ran everytime the game overlay is rendered

```javascript
TriggerRegister.registerRenderOverlay("exampleImportRenderOverlay");

function exampleImportRenderOverlay() {
  
}
```

Rendering has to be done every frame of the game, otherwise it will only be on the screen for one frame.
The RenderOverlay Trigger is called every frame of the game, so it is required for rendering. All of the actual
rendering code will go inside this function, although it could be seperated into seperate ones.

## Setting priority

>It is possible to set a certain trigger's priority like so:

```javascript
TriggerRegister.registerRenderOverlay("exampleImportRenderOverlayLast").setPriority(Priority.LOWEST);
TriggerRegister.registerRenderOverlay("exampleImportRenderOverlayFirst").setPriority(Priority.HIGHEST);

function exampleImportRenderOverlayLast() {
  
}

function exampleImportRenderOverlayFirst() {
  
}
```

Here, were are dealing with the priority of triggers. Priorities are `LOWEST, LOW, NORMAL, HIGH, HIGHEST`.
Triggers with a priority of HIGHEST are ran first, because they have first say on an event. Triggers with a priority of LOWEST
then, are ran last.

<aside class="notice">All trigger types can have a priority set, it's just most commonly used in rendering</aside>

## Simple text rendering

>You can render text onto the screen with this code:

```javascript
TriggerRegister.registerRenderOverlay("exampleImportRenderOverlay");

function exampleImportRenderOverlay() {
  RenderLib.drawString("Hello World!", 10, 10, RenderLib.color(255, 255, 255, 255));
}
```

Every frame, the code inside `exampleImportRenderOverlay` is called. Inside of this function, we make one call
to `RenderLib.drawString(text, screenX, screenY, color)`. We make the text say "Hello World!", and place it on the screen
at 10, 10 (the top left corner).

The other interesting part to take a look at is the 4th argument, which is the color of the
text. For the color, we make a call to `RenderLib.color(red, green, blue, alpha)`. In this example, the text will be white.

<aside class="warning">If all you are rendering is text, it is preferable to use Display objects, covered later.</aside>

## Rendering of shapes

>This example renders a rectangle, circle, and triangle

```javascript
TriggerRegister.registerRenderOverlay("exampleImportRenderOverlay");

function exampleImportRenderOverlay() {
  var white = RenderLib.color(255, 255, 255, 255);
  
  RenderLib.drawRectangle(white, 10, 10, 50, 50);
  RenderLib.drawShape(white, 360, 100, 100, 25);
  RenderLib.drawPolygon(white, [300, 300], [400, 400], [200, 400]);
}
```

In our rendering function we are now drawing a bunch of shapes with a bunch of different methods.
The first line is simply a variable keeping the color white so we don't have to repeat ourselves so much.

The first actual rendering line is the call to `RenderLib.drawRectangle(color, screenX, screenY, width, height);`.
In this example, its a simple 50x50 square, starting at (10,10) on the player's screen.

The second line that does rendering calls the `RenderLib.drawShape(color, segments, screenX, screenY, radius);` method.
This one draws a perfect shape with that number of segments. 3 would draw a perfect triangle, 5 a pentagon. This could be 
used instead of the next line to draw a triangle, or the line before for a square. For this example, it draws a circle.

The last line makes use of the `RenderLib.drawPolygon(color, [x, y]...);` method. It can take as many arrays with x,y
coordinates as you pass it to add more and more points. This example is used to make a triangle.