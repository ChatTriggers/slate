# Rendering

Rendering is where imports can draw most anything on to the game screen.

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
rendering code will go inside this function, although it could be separated into separate ones.

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
  RenderLib.drawString("Hello World!", 10, 10, RenderLib.WHITE);
}
```

Every frame, the code inside `exampleImportRenderOverlay` is called. Inside of this function, we make one call
to `RenderLib.drawString(text, screenX, screenY, color)`. We make the text say "Hello World!", and place it on the screen
at 10, 10 (the top left corner).

The other interesting part to take a look at is the 4th argument, which is the color of the
text. For the color, we use a preset color in RenderLib. We could have also made a call to `RenderLib.color(red, green, blue, alpha)`.
In this example, that call would be RenderLib.color(255, 255, 255, 255). Values should range from 0-255.

<aside class="warning">If all you are rendering is text, it is preferable to use Display objects, covered <a href="#displays">later</a>.</aside>

## Rendering of shapes

>This example renders a rectangle, circle, and triangle

```javascript
TriggerRegister.registerRenderOverlay("exampleImportRenderOverlay");

function exampleImportRenderOverlay() {
  var white = RenderLib.WHITE;
  
  RenderLib.drawRectangle(white, 10, 10, 50, 50);
  RenderLib.drawShape(white, 360, 100, 100, 25);
  RenderLib.drawPolygon(white, [300, 300], [400, 400], [200, 400]);
}
```

In our rendering function we are now drawing a bunch of shapes with a bunch of different methods.
The first line is simply a variable keeping the color white so we don't have to repeat ourselves so much.

### Rendering rectangles

The first actual rendering line is the call to `RenderLib.drawRectangle(color, screenX, screenY, width, height);`.
In this example, its a simple 50x50 square, starting at (10,10) on the player's screen.

### Rendering perfect shapes

The second line that does rendering calls the `RenderLib.drawShape(color, segments, screenX, screenY, radius);` method.
This one draws a perfect shape with that number of segments. 3 would draw a perfect triangle, 5 a pentagon. This could be 
used instead of the next line to draw a triangle, or the line before for a square. For this example, it draws a circle.

### Rendering polygons

The last line makes use of the `RenderLib.drawPolygon(color, [x, y]...);` method. It can take as many arrays with x,y
coordinates as you pass it to add more and more points. This example is used to make a triangle.

## Rendering images

> This example renders the images on the screen

```javascript
TriggerRegister.registerRenderOverlay("exampleImportRenderImageOverlay");
RenderLib.downloadImage("http://ct.kerbybit.com/ct.js/images/logo.png", "ctjs-logo.png");

function exampleImportRenderImageOverlay() {
    RenderLib.drawImage("ctjs-logo.png", 10, 10, 0, 0, 256, 256, .5);
    RenderLib.drawItemIcon(250, 250, "minecraft:apple");
}
```

As before, we register for a render overlay trigger so we can do our rendering in it. However, this time, we also make
a call to `RenderLib.downloadImage(url, resourceName)` in the initialization of our script. This downloads the image from
the provided link and makes it available for rendering.

### Rendering custom images

The first thing we do in the render function is call
`RenderLib.drawImage(resourceName, screenX, screenY, textureMapX, textureMapY, textureWidth, textureHeight, scale)`
to render the image with 1/2 of its normal size.


The resource name has to be the same as what you pass in when you download the image. For the most part, textures will
start at 0,0 and be 256x256 in size.

<aside class="notice">Images can also be put in /Imports/Example/assets/ for the same result, just make sure resource name is
the name of the file, and the file has a <code>.png</code> extension.</aside>

### Rendering minecraft images

The last thing we do in the render function is call `RenderLib.drawItemIcon(screenX, screenY, itemName)`. Here we pass
the x and y of where on the screen we want the item image to be rendered, and the name of the item, such as `minecraft:apple`.

## Advanced rendering

>Here we are rendering text that is a rainbow color

```javascript
TriggerRegister.registerRenderOverlay("exampleImportRenderOverlay");
var exampleImportStep = 0;

function exampleImportRenderOverlay() {
  RenderLib.drawString("Rainbows!", 10, 10, RenderLib.getRainbow(exampleImportStep));
  
  exampleImportStep++;
}
```

This topic covers advanced rendering, like rainbow colors and dynamic positioning.

### Rainbow colors

Again, we setup the default rendering scheme of a RenderOverlay trigger and its corresponding function. However, this
time we also create a "exampleImportStep" variable that starts of at 0. Then, every time we render to the screen, we
increment this step variable by 1.

This variable is used when it is passed into the `RenderLib.getRainbow(step)` method, which produces rotating colors,
which we then use as the color for the drawString method.

### Dynamic positioning

>This example showcases how to make render positioning dynamic

```javascript
TriggerRegister.registerRenderOverlay("exampleImportRenderOverlay");

function exampleImportRenderOverlay() {
  var renderWidth = RenderLib.getRenderWidth();
  var white = RenderLib.WHITE;
  
  var textToRender = "Rainbows!";
  RenderLib.drawString(textToRender, (renderWidth / 2) - (RenderLib.getStringWidth(textToRender) / 2), 100, white);
  
  var rectWidth = 50;
  RenderLib.drawRectangle(white, (renderWidth / 2) - (rectWidth / 2), 200, rectWidth, 50);
}
```

Here we are making all of our rendered objects be perfectly aligned horizontally on the screen for all windows sizes.
We start off by getting the height of the current window, with the call to `RenderLib.getRenderWidth()`.

Then, for eachpart we render, we get half the width of the window, and then subtract half the width of our rendered object.
For a string this is done with `(renderWidth / 2) - (RenderLib.getStringWidth(textToRender) / 2)`. For a fixed width object,
you can replace `RenderLib.getStringWidth(textToRender)` with the width of the object.

### Step triggers

> This example shows how to create a step trigger

```javascript
TriggerRegister.registerStep("exampleImportStep");

function exampleImportStep() {
  
}
```