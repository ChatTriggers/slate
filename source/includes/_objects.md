# Objects

ct.js provides several objects to expand the functionality of your imports without you needing to delve into base
Minecraft code. A list is found below.

<aside class="warning">It is bad practice to create new object every time you use it, they're heavy objects.
Create one in the global scope, and refer back to it.</aside>

| Object                  | Description                                                            |
| ----------------------- | ---------------------------------------------------------------------- |
| [Book](#books)          | Makes an openable book in Minecraft                                    |
| [CPS](#cps)             | Contains information about the player's clicks per second              |
| [Display](#displays)    | Renders text on to the game screen                                     |
| [Gui](#guis)            | Makes an openable gui in Minecraft                                     |
| [Inventory](#inventory) | Contains information about the player's inventory                      |
| [KeyBind](#keybinds)    | Used for detecting a key's state                                       |
| ParticleEffect          | Allows creation of custom particle effects to be displayed client side |
| [Player](#player)       | Used for getting information about the player                          |
| Thread                  | This is a pseudo object, used to do tasks that take a long time        |

# Books

`Book` objects are used for displaying base Minecraft book GUIs with customizable text.

## Creation

> This is how you create a book

```javascript
var book = new Book("Example Book");
```

We create our book with the Book constructor of `new Book(bookName);`. We want to create our book in the global scope,
as explained below.

## Adding content

> This is how you add pages to the book

```javascript
var book = new Book("Example Book");

book.addPage("This is a very simple page with just text.");
book.addPage(new Message("This is a page with a ", new TextComponent("twist!").setHoverValue("Hi! I'm hover text :o")));
```

To add content to our book, we'll want to utilize the `.addPage(message)` method. This can take either a simple
string as the message for the page, or a `Message` object if you want to utilize the functionality the provide, covered
[here](#message-objects). This should be done right after the instantiation of the book.

## Updating content

> This is how to update a page's content

```javascript
book.setPage(1, new Message("lol!"));
```

To set a page, we use the `.setPage(pageNumber, message)`. Page number is the number of the page you wish to update,
0 based. The message _has_ to be a `Message` object, there is no method for just a string.

This can be done anytime, just re-display the book to see the updated version. The page you try to set must already exist,
or else there will be errors. Just add the page if you need to add a new page afterwards.

## Displaying

> This is how to display the book to the user

```javascript
book.display();
```

> This is how to display the book starting on a certain page

```javascript
book.display(1);
```

This is a very simple operation which just opens the book. You can also specify a page number to open the book to as
the first argument, it defaults to 0. If the page you specify doesn't exist, the player will have to click one of the
arrows to go to the next available page.

# CPS

> The `CPS` object gives information about the player's clicks per second.

## Clicks per second

> To get the left clicks per second, use this

```javascript
var leftClicks = CPS.getLeftClicks();
```

> To get the right clicks per second, use this

```javascript
var rightClicks = CPS.getRightClicks();
```

There are more methods for the `CPS` object, but these are the most common. You can always see a full list
of up to date documentation on the [JavaDocs](https://www.chattriggers.com/javadocs).

# Displays

Displays are used for rendering simple text on to the players screen. If you would like to utilize other rendering
functions found in [the rendering section](#rendering), use custom rendering functions.

## Creation

> This is how you can create a `Display` object

```javascript
var display = new Display();
```

This `Display` object is now created, but it doesn't do much of anything yet.

## Adding content

> This is how you add lines to a display

```javascript
var display = new Display();

display.addLine("Ay! First line.");
display.addLines("2nd line", "3rd line");
display.addLines(2);
display.addLine(new DisplayLine("2 line gap above me!"));
```

`Displays` consist of lines of text. These lines can be added and set, and they can use color codes. The first call to
`.addLine(message)` adds a line to the display with the text passed in. The second call to `.addLines(messages...)` adds
as many lines as you pass into it, in our case just 2. The final call to `.addLines(number)` adds as many lines as
you pass in, this is used for setting lines later that you don't want to say anything yet.

## Setting content

> This is how you set a line in a display

```javascript
display.setLine(3, "Now this line has text :)");
```

In this example the call to `.setLine(lineNumber, message)` sets the 4th line in the display (0 based) which was previously
blank to our example text. This is what we would use if we want to update a display with information, like the player's
current coordinates.

## Setting positioning

> This is how you set the alignment of a display

```javascript
display.setAlign("left");
```

This aligns your display on the left side of the screen. Other options are `"center"` and `"right"`.

> This is how you set the order of the lines

```javascript
display.setOrder("down");
```

This renders the lines from 0 going downwards, usually what you'd want. The other option is `"up"`.

> This is how you set the exact position of the display

```javascript
display.setRenderLoc(10, 10);
```

This sets the X and Y coordinate of where your display should start, with the first argument being X, and the second being Y.

## Setting background and foreground options

> This sets the background color of the display

```javascript
display.setBackgroundColor(Renderer.AQUA);
```

This makes the background color of the display aqua. Other options are all the colors in Renderer, or a custom color
with `Renderer.color(r, g, b, a)`.

> This sets the type of background for the display

```javascript
display.setBackground(DisplayHandler.Background.PER_LINE);
```

This option sets how the background color should be displayed. `PER_LINE` says that the background should be the width
of each line. `NONE` would mean don't show background color, and `FULL` would indicate make the background color draw in
a box around the entire display.

> This sets the foreground (text) color of the display

```javascript
display.setTextColor(Renderer.BLUE);
```

All text in the display will now show blue. This method can take any Renderer color, including custom ones described above.

DisplayLine can also be used to set the text color, background color, and alignment for a specific line.

```javascript
display.addLine(
  new DisplayLine("This will have green text!").setTextColor(Renderer.GREEN)
);
```

This will set the new line to have green text, overriding the blue we set earlier.

# Guis

Guis are screens that are opened in game, such as the chat gui, or the escape menu. These stop the player from moving.

## Creation

> This is how you create a gui

```javascript
var gui = new Gui();
```

Like other objects, creating a Gui is very simple.

## Rendering the gui

> This is how you set up a function to render the gui

```javascript
var gui = new Gui();
gui.registerDraw(myGuiRenderFunction);

function myGuiRenderFunction(mouseX, mouseY, partialTicks) {
  Renderer.drawRect(Renderer.WHITE, mouseX, mouseY, 50, 50);
}
```

Everything inside of the "myGuiRenderFunction" will be ran while the gui is open. Inside of this function you should
make use of the [Renderer](#rendering) functions to draw what you want on to the screen. The three arguments passed in
are the x coordinate of the user's mouse, the y coordinate of the users mouse, and the partial ticks.

In this example, we render a 50x50 square with the top left corner being the user's mouse position.

## Adding interactivity

> This is how you detect when a user presses a key

```javascript
var gui = new Gui();
gui.registerKeyTyped(myGuiKeyTypedFunction);

function myGuiKeyTypedFunction(typedChar, keyCode) {
  ChatLib.chat("You typed " + typedChar);
}
```

> This is how you detect when the user clicks

```javascript
var gui = new Gui();
gui.registerClicked(myGuiClickedFunction);
gui.registerDraw(myGuiRenderFunction);

var renderSquareX = 0;
var renderSquareY = 0;

function myGuiRenderFunction(mouseX, mouseY, partialTicks) {
  Renderer.drawRect(Renderer.WHITE, renderSquareX, renderSquareY, 50, 50);
}

function myGuiClickedFunction(mouseX, mouseY, button) {
  renderSquareX = mouseX;
  renderSquareY = mouseY;
}
```

In the first example we register our key typed function to be activated when someone types a key in our Gui. The keyCode
passed can be compared with the Keyboard class keys.

In the next example we make things more complicated. We register both a draw function and a mouse clicked function.
We render the same box as in the previous draw example, however, we make the coordinates of it be the last place the
mouse was clicked.

## Displaying the gui

> To display the gui, use this

```javascript
gui.open();
```

> To close the gui, use this

```javascript
gui.close();
```

These very simple methods open and close the gui, and neither take any arguments.

# Inventory

The Inventory object contains methods used for getting information about the user's inventory. It can be called
through `Player.getInventory()`. More about the `Player` class can be found [later](#player).

## Example

```javascript
function hasSponge() {
  var inventory = Player.getInventory();

  // The ID for sponge is 19.
  var spongeSlot = inventory.indexOf(19);

  if (spongeSlot !== -1) {
    ChatLib.chat("Sponge found in slot " + spongeSlot + "!");
  } else {
    ChatLib.chat("Sponge not found!");
  }
}
```

The example above lets us see if the inventory has a sponge item in it, and if so, say the slot it's in.
This works by first getting the inventory of the player, then it gets the slot ID of sponge, if there is any.
If any slot has a sponge, it will output the slot, otherwise it will return -1.

# KeyBinds

KeyBinds are used for detecting the state of a key.

<aside class="notice">These aren't meant to be used for Guis, it's preferable to register a keytyped method for detecting
if a player pressed a key inside your Gui</aside>

## Creation

> This is the preferred method to get a keybind

```javascript
var wKeyBind = getKeyBindFromKey(Keyboard.KEY_W, "My W Key");

function getKeyBindFromKey(key, description) {
  var mcKeyBind = Client.getKeyBindFromKey(key);

  if (mcKeyBind == null || mcKeyBind == undefined) {
    mcKeyBind = new KeyBind(description, key);
  }

  return mcKeyBind;
}
```

Let's break this down. First, we call the function "getKeyBindFromKey" and save the result in a variable. This result
is our finished KeyBind. We pass into this function a keyCode, from the [Keyboard](http://legacy.lwjgl.org/javadoc/org/lwjgl/input/Keyboard.html) class.

Next, we have our function. First, it tries to get the Keybind for our specified key from Client. We do this
because if we want a keybind Minecraft already uses, we don't want to override it. However, if Minecraft isn't using
that key (because the function returned null), we need to make our own, with the description and key we specified.

In our case, this will return the keybind already used by minecraft for the run key (unless of course you changed yours
to a different key).

## Using the keybind

> To check if they key bind is being held, do this

```javascript
if (wKeyBind.isKeyDown()) {
  ChatLib.chat("Key is down!");
}
```

> To check if the key bind was pressed, use this

```javascript
if (wKeyBind.isPressed()) {
  ChatLib.chat("Key is pressed!");
}
```

This first example would spam your chat if it was in an Tick trigger or something of the like, as it will always be true
if you are holding down the key.

Now, in the second example, we would only get the chat message once every time we press and hold the key. It only
returns true one time per key press. If you let go and press again, it will return true once more.

# Player

The `Player` object contains many methods used for retrieving information about the player.

## Location

> To get the direction the player is facing, use this

```javascript
var direction = Player.facing();
```

> To get the coordinates of the player, you can use this

```javascript
var playerX = Player.getX();
var playerY = Player.getY();
var playerZ = Player.getZ();
```

Using this in conjunction with displays, you can make a coordinates HUD.

```js
register("tick", locationTracker);

var display = new Display();
display.setRenderLoc(10, 10);

function locationTracker() {
  display.setLine(0, "X: " + Player.getX());
  display.setLine(1, "Y: " + Player.getY());
  display.setLine(2, "Z: " + Player.getZ());
}
```

In this case, we just made a display, and every tick it updates to show the player's location.

## LookingAt

> The `LookingAt` object was replaced with the `Player.lookingAt()` method.

This gets the current object that the player is looking at, whether that be a block or an entity. It returns
either the `Block`, `Sign`, `Entity` class, or an air block when not looking at anything.

## Health

> You can not only get the player's health, hunger, active potion effects, but much more! Here's just a few
> examples of how to get each part.

```javascript
var health = Player.getHP();
var hunger = Player.getHunger();
var potions = Player.getActivePotionEffects();
var xpLevel = Player.getXPLevel();
```

## Armor

> To get the player's armor, you can use `Player.armor`

```javascript
var helmet = Player.armor.getHelmet();
```

This would return the item that is in the helmet slot of the player. This can be a pumpkin or any item
that is on the helmet slot.

## Getting Inventory information

> Here's an example showing how to find the durability and stack size of the item the player is holding.

```javascript
function displayHeldItemInfo() {
  var item = Player.getHeldItem();

  if (item.getName() !== "tile.air.name") {
    var durabilityPercentage = Math.ceil(
      (item.getDurability() / item.getMaxDamage()) * 100
    );

    // If NaN, that means it's a block
    if (isNaN(durabilityPercentage)) durabilityPercentage = "N/A (not a tool!)";

    ChatLib.chat("Item: " + item.getName());
    ChatLib.chat("Durability: " + durabilityPercentage);
    ChatLib.chat("Stack Size: " + item.getStackSize());
  } else {
    ChatLib.chat("&4You aren't holding anything!");
  }
}
```

In this case, we get the held item of the player. If the item isn't air, then it finds how damaged it is. If
the durability isn't a number, that means the item has to be a block. Then, if the player isn't holding anything, it
runs the last piece of the code, which is when the hand is empty.
If you want to get the player's inventory as a list of `Items`, use `Player.getInventory()`.
