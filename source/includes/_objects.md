# Objects

ct.js provides several objects to expand the functionality of your imports without you needing to delve into base
Minecraft code. A list is found below.

Object | Description
------ | -----------
Book | Makes an openable book in Minecraft
Display | Renders text on to the game screen
Gui | Makes an openable gui in Minecraft
KeyBind | Used for detecting a key's state
XMLHttpRequest | Used for making an HTTP request
Thread | This is a pseudo object, used to do tasks that take a long time

## Books

Books objects are used for displaying base Minecraft book GUI's with customizable text.

### Creation

> This is how you create a book

```javascript
var book = new Book("Example Book");
```

We create our book with the Book constructor of `new Book(bookName);`. We want to create our book in the global scope,
as explained below.

<aside class="warning">It is preferable to not create a new book every time you wish to display it, they're slightly
heavy objects.</aside>

### Adding content

> This is how you add pages to the book

```javascript
var book = new Book("Example Book");

book.addPage("This is a very simple page with just text.");
book.addPage(new Message("This is a page with a ", ChatLib.hover("twist!", "Hi! I'm hover text :o")));
```

To add content to our book, we'll want to utilize the `.addPage(message)` method. This can take either a simple
string as the message for the page, or a Message object if you want to utilize the functionality the provide, covered
[here](#message-objects). This should be done right after the instantiation of the book.

### Updating content

> This is how to update a page's content

```javascript
book.setPage(1, new Message("lul!"));
```

To set a page, we use the `.setPage(pageNumber, message)`. Page number is the number of the page you wish to update,
0 based. The message _has_ to be a Message object, there is no method for just a string.

This can be done anytime, just re-display the book to see the updated version. The page you try to set must already exist,
or else there will be errors. Just add the page if you need to add a new page afterwards.

### Displaying

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

## Displays

Displays are used for rendering simple text on to the players screen. If you would like to utilize other rendering
functions found in [the rendering section](#rendering), use custom rendering functions.

### Creation