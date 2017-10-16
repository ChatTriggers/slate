# Objects

ct.js provides several objects to expand the functionality of your imports without you needing to delve into base
Minecraft code. A list is found below.

<aside class="warning">It is bad practice to create new object every time you use it, they're heavy object.
Create one in the global scope, and refer back to it.</aside>

Object | Description
------ | -----------
[Book](#books) | Makes an openable book in Minecraft
[Display](#displays) | Renders text on to the game screen
[Gui](#guis) | Makes an openable gui in Minecraft
[KeyBind](#keybinds) | Used for detecting a key's state
[XMLHttpRequest](#xmlhttprequests) | Used for making an HTTP request
Thread | This is a pseudo object, used to do tasks that take a long time

# Books

Books objects are used for displaying base Minecraft book GUI's with customizable text.

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
book.addPage(new Message("This is a page with a ", ChatLib.hover("twist!", "Hi! I'm hover text :o")));
```

To add content to our book, we'll want to utilize the `.addPage(message)` method. This can take either a simple
string as the message for the page, or a Message object if you want to utilize the functionality the provide, covered
[here](#message-objects). This should be done right after the instantiation of the book.

## Updating content

> This is how to update a page's content

```javascript
book.setPage(1, new Message("lul!"));
```

To set a page, we use the `.setPage(pageNumber, message)`. Page number is the number of the page you wish to update,
0 based. The message _has_ to be a Message object, there is no method for just a string.

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

# Displays

Displays are used for rendering simple text on to the players screen. If you would like to utilize other rendering
functions found in [the rendering section](#rendering), use custom rendering functions.

## Creation

> This is how you can create a Display object

```javascript
var display = new Display();
```

This display object is now created, but it doesn't do much of anything yet.

## Adding content

> This is how you add lines to a display

```javascript
var display = new Display();

display.addLine("Ay! First line.");
display.addLines("2nd line", "3rd line");
display.addLines(2);
```

Displays consist of lines of text. These lines can be added and set, and they can use color codes. The first call to
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
display.setAlign(DisplayHandler.Align.LEFT);
```

This aligns your display on the left side of the screen. Other options are `CENTER` and `RIGHT`.

> This is how you set the order of the lines

```javascript
display.setOrder(DisplayHandler.Order.DOWN);
```

This renders the lines from 0 going downwards, usually what you'd want. Other options are `UP`.

> This is how you set the exact position of the display

```javascript
display.setRenderLoc(10, 10);
```

This sets the X and Y coordinate of where your display should start, with the first argument being X, and the second Y.

## Setting background and foreground options

> This sets the background color of the display

```javascript
display.setBackgroundColor(RenderLib.ORANGE);
```

This makes the background color of the display orange. Other options are all the colors in RenderLib, or a custom color
with `RenderLib.color(r, g, b, a)`.

> This sets the type of background for the display

```javascript
display.setBackground(DisplayHandler.Background.PER_LINE);
```

This option sets how the background color should be displayed. `PER_LINE` says that the background should be the width
of each line. `NONE` would mean don't show background color, and `FULL` would indicate make the background color draw in
a box around the entire display.

> This sets the foreground (text) color of the display

```javascript
display.setTextColor(RenderLib.BLUE);
```

All text in the display will now show blue. This method can take any RenderLib color, including custom ones described above.

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
gui.registerOnDraw("myGuiRenderFunction");

function myGuiRenderFunction(mouseX, mouseY, partialTicks) {
  RenderLib.drawRectangle(RenderLib.WHITE, mouseX, mouseY, 50, 50);
}
```

Everything inside of the "myGuiRenderFunction" will be ran while the gui is open. Inside of this function you should
make use of the [RenderLib](#rendering) functions to draw what you want on to the screen. The three arguments passed in
are the x coordinate of the user's mouse, the y coordinate of the users mouse, and the partial ticks.

In this example, we render a 50x50 square with the top left corner being the user's mouse position.

## Adding interactivity

> This is how you detect when a user presses a key

```javascript
var gui = new Gui();
gui.registerOnKeyTyped("myGuiKeyTypedFunction");

function myGuiKeyTypedFunction(typedChar, keyCode) {
  ChatLib.chat("You typed " + typedChar);
}
```

> This is how you detect when the user clicks

```javascript
var gui = new Gui();
gui.registerOnClicked("myGuiClickedFunction");
gui.registerOnDraw("myGuiRenderFunction");

var renderSquareX = 0;
var renderSquareY = 0;

function myGuiRenderFunction(mouseX, mouseY, partialTicks) {
  RenderLib.drawRectangle(RenderLib.WHITE, renderSquareX, renderSquareY, 50, 50);
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

# KeyBinds

KeyBinds are used for detecting the state of a key.

<aside class="notice">These aren't meant to be used for Guis, it's preferable to register a keytyped method for detecting
if a player pressed a key inside your Gui</aside>

## Creation

> This is the preferred method to get a keybind

```javascript
var wKeyBind = getKeyBindFromKey(Keyboard.KEY_W, "My W Key");

function getKeyBindFromKey(key, description) {
  var mcKeyBind = MinecraftVars.getKeyBindFromKey(key);
  
  if (mcKeyBind == null || mcKeyBind == undefined) {
      mcKeyBind = new KeyBind(description, key);
  }
  
  return mcKeyBind;
}
```

Let's break this down. First, we call the function "getKeyBindFromKey" and save the result in a variable. This result
is our finished KeyBind. We pass into this function a keyCode, from the [Keyboard](http://legacy.lwjgl.org/javadoc/org/lwjgl/input/Keyboard.html) class.

Next, we have our function. First, it tries to get the Keybind for our specified key from MinecraftVars. We do this
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

# XMLHttpRequests

XMLHttpRequests are used to send HTTP requests. This can be used for things like making API calls.

## Creation

> This is how you can create the request object

```javascript
var request = new XMLHttpRequest();
```

This creates the request object from which will will make the HTTP connection.

## Opening a connection

> This is how you can open a GET request

```javascript
var request = new XMLHttpRequest();

request.open("GET", "httpbin.com/get", true);
```

This first example opens the GET request to a url (this is just a simple testing website) with the `.open(method, url, async)`
method. The last flag is whether or not we want the request to be done asynchronously.

<aside class="notice">For GET requests, all params should be done in the URL with <code>?key=value&key2=value2</code>.</aside>

> This is how you can open a POST request

```javascript
var request = new XMLHttpRequest();

request.open("POST", "httpbin.com/post", true);
```

The second example showcases how to open a POST request. All arguments are the same, `.open(method, url, async)`.

<aside class="notice">For POST requests, all params should be done in the send method shown later.</aside>

## Setting the callback

> This is how you set the callback for the request

```javascript
var request = new XMLHttpRequest();

request.open("GET", "httpbin.com/get", true);
request.setCallbackMethod("requestCompleted");


function requestCompleted(request) {
  
}
```

The function we set as the callback method will be called when the request has finished. Currently, it will never be called,
because we haven't sent the request yet. The callback method should take one parameter, which will be the XHR request
object, because it is what you get the response data from.

## Sending the request

> This is how you actually send the GET request

```javascript
var request = new XMLHttpRequest();

request.open("GET", "httpbin.com/get?foo=bar", true);
request.setCallbackMethod("requestCompleted");
request.send();


function requestCompleted(request) {
  print("Finished GETting!");
}
```

This sends the asynchronous GET request with one request data, that being foo=bar. This prints `"Finished!"` to the
console when completed.

> This is how you actually send the POST request

```javascript
var request = new XMLHttpRequest();

request.open("POST", "httpbin.com/post", true);
request.setCallbackMethod("requestCompleted");
request.send("foo", "bar");


function requestCompleted(request) {
  print("Finished POSTing!");
}
```

This sends the asynchronous POST request with one bit of post data, again being foo=bar. The send method takes as many
strings as you pass in, and every other is the key, and the one following be the value.

## Receiving the request

> This is how you can receive the response data

```javascript
var request = new XMLHttpRequest();

request.open("GET", "httpbin.com/get", true);
request.setCallbackMethod("requestCompleted");
request.send();


function requestCompleted(request) {
  print(request.statusText);
  print(request.responseText);
}
```

The request object passed into the function is the same XMLHttpRequest you created, opened, and sent. It has three fields,
`status`, `statusText`, and `responseText` which are set when the request completes. Status is used for the response code
for the request, i.e. `200` for OK, `404` for not found, etc.

## Extra data

> This is how you can store extra data in your request for use in the callback

```javascript
var request = new XMLHttpRequest();

request.open("GET", "httpbin.com/get", true);
request.setCallbackMethod("requestCompleted");
request.extras.put("myMessage", "seekrits");
request.send();


function requestCompleted(request) {
  print(request.extras.get("myMessage"));
}
```

Here we are utilizing the "extras" map that is a part of the XHR object. When we are setting up our request, we put
a key value pair in the extras map. The key in this case is "myMessage", and the value could be anything at all, we are
just using a string.

When we get the request passed back to the callback function, we print out the message we stored. This prints `seekrits`,
just like it should. This is an easy way to tunnel information from the request sending to completion without needing
global variables.

## Custom headers

> This example sets the Max-Forwards header

```javascript
var request = new XMLHttpRequest();

request.open("GET", "httpbin.com/get", true);
request.setCallbackMethod("requestCompleted");
request.addRequestHeader("Max-Forwards", "5");
request.send();
```

Here we are attaching our own header to the HTTP request, in this case it is `Max-Forwards`. The method used is
`.addRequestHeader(header, value)`, both arguments being strings.

<aside class="warning">The request must be opened before adding request headers, or else errors will be thrown</aside>