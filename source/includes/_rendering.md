# Rendering

Rendering is where modules can draw most anything on to the game screen. All 2D rendering involves
calling methods in the `Renderer` object. 3D rendering involves calling methods in the
[`Tessellator`](#tessellator) object.

<aside class="notice">All 2D rendering coordinates start at the top left of the screen. X increases from left to right, and Y increases from top to bottom.</aside>

## Setting up

> Function to be ran everytime the game overlay is rendered

```javascript
register("renderOverlay", myRenderOverlay);

function myRenderOverlay() {

}
```

Rendering has to be done every frame of the game, otherwise it will only be on the screen for one frame. There are many different render triggers, and they all start with `Render`. The most common render trigger is RenderOverlay: this trigger fires every frame with no conditions.

## Setting priority

> It is possible to set a certain trigger's priority like so

```javascript
register("renderOverlay", myRenderOverlayLast).setPriority(Priority.LOWEST);
register("renderOverlay", myRenderOverlayFirst).setPriority(Priority.HIGHEST);

function myRenderOverlayLast() {

}

function myRenderOverlayFirst() {

}
```

Here, were are dealing with the priority of triggers. Priorities are `LOWEST, LOW, NORMAL, HIGH, HIGHEST`.
Triggers with a priority of HIGHEST are ran first, because they have first say on an event. Triggers with a priority of LOWEST are ran last. The function lan rast will draw on TOP of anything before it.

<aside class="notice">All trigger types can have a priority set, it's just most commonly used in rendering</aside>

## Simple text rendering

> You can render text onto the screen with this code

```javascript
register("renderOverlay", myRenderOverlay);

function myRenderOverlay() {
  Renderer.drawString("Hello World!", 10, 10);
}
```

Every frame, the code inside `myRenderOverlay` is called. Inside of this function, we make one call
to `Renderer.drawString(text, screenX, screenY)`. We make the text say "Hello World!", and place it on the screen
at 10, 10 (the top left corner).

<aside class="warning">If all you are rendering is text, it is preferable to use `Display` objects, covered <a href="#displays">later</a>.</aside>

## More complex text rendering

> This is how you would draw the same string (but colored) with an object

```javascript
register("renderOverlay", myRenderOverlay);
var myTextObject = new Text("Hello World!", 10, 10).setColor(Renderer.RED);

function myRenderOverlay() {
  myTextObject.draw();
}
```

Here, instead of simply making a method call, we are instatiating an object to do our drawing. This allows for much greater customization, such as rotation, scaling, and as described below, coloring.

The other interesting part to take a look at is the call to `setColor`, which will, as you can guess, set the color of the text. For the color, we use a preset color in `Renderer`. We could have also made a call to `Renderer.color(red, green, blue, alpha)`, and subsequently passed that in to the method. In this example, that call would be `Renderer.color(255, 255, 255, 255)`. Values should range from 0-255.

<aside class="warning">Do not instantiate objects inside of a render trigger. Create them outside of the trigger, and if necessary, make any changes to the object inside of the trigger.</aside>

## Rendering of shapes

> This example renders a rectangle, circle, and triangle

```javascript
register("renderOverlay", myRenderOverlay);

var rectangle = new Rectangle(Renderer.WHITE, 10, 10, 50, 50);
var circle = new Shape(Renderer.WHITE).setCircle(100, 100, 25, 360);
var polygon = new Shape(Renderer.WHITE)
  .addVertex(300, 300)
  .addVertex(400, 400)
  .addVertex(200, 400);

function myRenderOverlay() {
  rectangle.draw();
  circle.draw();
  polygon.draw();
}
```

Let's look at more complex rendering using shapes. We can see our first complex piece of rendering code to the right. The first thing to notice is how we define the shapes outside of our render trigger. This way we aren't create three objects 60+ times a second.

### The different shape classes

We create the first shape, the rectangle, with the instantiation of the `Rectangle` class, whose constructor takes the following arguments: color, x, y, width, and height.

The next shape is a circle, which we create through the more general `Shape` class, which is just a collection of (x, y) points to connect together. We use the `setCircle` method, which automatically populates the Shape's vertices to give us a perfect circle.

Finally, we manually configure the vertices of the last shape ourselves with the `addVertex` method. After we have created all of our shapes outside of the trigger function, we call the `draw` method inside of the trigger function to render them to the screen.

## Rendering images

> This example renders the images on the screen

```javascript
register("renderOverlay", myRenderImageOverlay);
var image = new Image("ctjs-logo.png", "http://ct.kerbybit.com/ct.js/images/logo.png");

function myRenderImageOverlay() {
  image.draw(100, 100);
}
```

As before, we register for a render overlay trigger so we can do our rendering in it. However, this time, we create an `Image` object with the file name and URL. The file will download to the ct.js assets directory, which defaults to `.minecraft/config/ChatTriggers/images`.

<aside class="notice">Images can also be put in /Imports/Example/assets/ for the same result, just make sure resource name is
the name of the file, and the file has a <code>.png</code> extension.</aside>

## Advanced rendering

> Here we are rendering text that is a rainbow color

```javascript
register("renderOverlay", myRenderOverlay);
var exampleImportStep = 0;

function myRenderOverlay() {
  var text = new Text("Rainbows!", 10, 10).setColor(Renderer.getRainbow(exampleImportStep));
  text.draw();

  exampleImportStep++;
}
```

This topic covers advanced rendering, like rainbow colors and dynamic positioning.

### Rainbow colors

Again, we setup the default rendering scheme of a RenderOverlay trigger and its corresponding function. However, this
time we also create a "exampleImportStep" variable that starts of at 0. Then, every time we render to the screen, we
increment this step variable by 1.

This variable is used when it is passed into the `Renderer.getRainbow(step)` method, which produces rotating colors,
which we then use as the color for the drawString method.

### Dynamic positioning

> This example showcases how to make render positioning dynamic

```javascript
register("renderOverlay", myRenderOverlay);

var width = Renderer.screen.getWidth();
var rectWidth = 50;
var textStr = "White Text!";

var rectangle = new Rectangle(Renderer.WHITE, width / 2 - rectWidth / 2, 200, rectWidth, 50);
var text = new Text(textStr, width / 2 - Renderer.getStringWidth(textStr) / 2, 100).setColor(Renderer.WHITE);

function myRenderOverlay() {
  text.draw();
  rectangle.draw();
}
```

Here we are making all of our rendered objects be perfectly aligned horizontally on the screen for all windows sizes. We start off by getting the height of the current window, with the call to `Renderer.screen.getWidth()`.

Then, for each part we render, we get half the width of the window, and then subtract half the width of our rendered object. For a string, this is done with `(width / 2) - (Renderer.getStringWidth(textToRender) / 2)`. For a fixed width object, you can replace `Renderer.getStringWidth(textToRender)` with the width of the object. Notice how we use the `Text` object and instantiate it outside of the render function. The `Text` object allows you to set additional properties, such as shadow and color.

# Tessellator

The Tessellator object is meant for rendering in 3 dimensions. You have to call this object under the `renderWorld` trigger.

## Drawing Text

> You can set floating text at specific world coordinates using `Tessellator.drawString(text, x, y, z)`

```javascript
register("renderWorld", myWorldRender);

function myWorldRender() {
  Tessellator.drawString("Hello there!", 100, 100, 100);
}
```

This will draw the words "Hello There" at the world location `(100, 100, 100)`.
You can also change the text color and scale, whether or not to render the shadow, and whether or not
the size increases from distance. 

## Drawing Textures

> You can also draw specific textures like so

```javascript
register("renderWorld", myWorldRender);

var img = new Image("kerbybit.png", "https://www.chattriggers.com/assets/images/kerbybit.png");

function myWorldRender() {
  Tessellator.bindTexture(img);
  Tessellator.begin()
    .translate(Player.getX(), Player.getY(), Player.getZ())
    .pos(-0.5, 0.5, -0.5).tex(0, 0)
    .pos(-0.5, 0.5, 0.5).tex(0, 1)
    .pos(0.5, 0.5, 0.5).tex(1, 1)
    .pos(0.5, 0.5, -0.5).tex(1, 0)
    .draw();
}
```

This will draw kerbybit's skin texture at the player's feet position. This works by first binding the texture
of the image to the `Tessellator` object. It then associates positions in the 3D world (relative to the
`Tesselator`'s current position) to points on the texture. The `tex` function takes an `x` and
`y` argument, both ranging from `0.0` to `1.0`, which identifies a point on the texture (i.e. 
`.tex(0.3, 0.7)` refers to the point 30% across from the left and 70% down from the top).
Finally, a call to `.draw()` finished the operation.

### Partial Ticks

Partial ticks are a fractional value representing the amount of time that’s passed between the last full tick and now. This is
useful in rendering, as the rest of the game runs on the tick system, but rendering uses frames, a much more precise operation.
We use this because otherwise the animation would be jittery because there are fewer ticks per second than frames per second.

If we wanted to make the example from above not be jittery, we would use partial ticks.

```javascript
function myWorldRender(partialTicks) {
  const lastX = new Entity(Player.getPlayer()).getLastX();
  const lastY = new Entity(Player.getPlayer()).getLastY();
  const lastZ = new Entity(Player.getPlayer()).getLastZ();

  const currentX = Player.getX();
  const currentY = Player.getY();
  const currentZ = Player.getZ();

  Tessellator.bindTexture(img);
  Tessellator.begin()
    .translate(
      lastX + (currentX - lastX) * partialTicks,
      lastY + (currentY - lastY) * partialTicks,
      lastZ + (currentZ - lastZ) * partialTicks
    )
    .pos(-0.5, 0.5, -0.5).tex(0, 0)
    .pos(-0.5, 0.5, 0.5).tex(0, 1)
    .pos(0.5, 0.5, 0.5).tex(1, 1)
    .pos(0.5, 0.5, -0.5).tex(1, 0)
    .draw();
}
```

The key difference here is that the change in position is multiplied by the change in time every frame. 
This makes the drawing smooth since it updates every frame instead of every tick.
