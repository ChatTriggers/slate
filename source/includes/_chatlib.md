# ChatLib

The ChatLib is a utility provided to imports for interacting with Minecraft chat. It has functionality to send, edit,
and delete messages in Minecraft's chat. You can create clickable, and hoverable, text in chat, run commands, and much
more.

## Sending messages

>This sends a message in chat

```javascript
ChatLib.chat("Coming from the code!");
```
>This sends a message in chat that doesn't trigger chat listeners

```javascript
ChatLib.chat("Coming from the code, but I don't trigger anything!", false);
```

The first example sends a simple string into the player's chat. However, this will trigger _all_ imports' chat
triggers with our message. If we send this message in a trigger that listens for the same one, the game will crash.

That's where the second example comes in. The false flag passed in at the end indicates that we don't want this to
occur, and no imports will receive this message.

## Message objects

>This example sends messages that have clickable and hoverable text

```javascript
var clickableMessage = new Message("This is not clickable. ", ChatLib.clickable("This is clickable", "run_command", "/help", "This is shown when hovering over the message!"), "!");

var hoverableMessage = new Message(ChatLib.hover("This message does nothing when clicked.", "But it shows this text when hovered over!"));

ChatLib.chat(clickableMessage);
ChatLib.chat(hoverableMessage);
```

Here we are creating new Message objects. These are required to send messages that have clickable or hoverable text.
The constructor of a Message can take as many strings, `ChatLib.clickable(text, action, value, hoverText)`, or
`ChatLib.hover(text, hoverText)` as you want. All of these can use color codes.

### Clickables

The first message we create is a message that has clickable, and non-clickable, text. The first part is regular text,
followed by a clickable part that run the `/help` command when clicked, and shows the hoverText when the mouse is
hovering over that part of the message. Then, at the end, it has a non-clickable exclamation point.

### Hovers

The second message created and chatted is a message that only contains a hoverable message. Nothing will be activated
or ran when the message is clicked.

<aside class="notice">You can also append the <code>false</code> flag when chatting messages to make them non-recursive.</aside>

## Message IDs

> This is how you send a chat message with an ID, and then delete it

```javascript
ChatLib.chat("This will be deleted!", 5050);

ChatLib.clearChat(5050);
```

> This is how you send a message object with an id, then delete it

```javascript
var myMessage = new Message("This will be deleted!");
myMessage.chatLineId = 5050;
ChatLib.chat(myMessage);

ChatLib.clearChat(5050);
```

Every message can have an ID specified, which can then be deleted from chat. This is best suited for auto-replacing menus,
chat messages you only want to display in chat for a certain amount of time, etc.

Only one chat message can have the same ID, as it will replace any messages with the same ID before sending.
The ID is passed as the last argument, and you pass the same ID to `ChatLib.clearChat(id)` to delete it.

<aside class="notice">Doing <code>ChatLib.clearChat()</code> will delete all messages in chat, no matter the ID</aside>

## Specially formatted messages

> This is how you center a chat message

```javascript
ChatLib.chat(ChatLib.getCenteredText("This is in the center of the chat!"));
```

> This is how you make a line break

```javascript
ChatLib.chat(ChatLib.getChatBreak("-"));
```

### Centered messages

To center a message in chat (padded by spaces), use the `ChatLib.getCenteredText(text)` method, and then chat what's
returned.

<aside class="warning">This is a <i>slightly</i> intensive operation, so if you can store this in a variable and re-use it, that's
preferable.</aside>

### Line breaks

To create a line break in chat that will be the exact length of the chat box no matter the user's width and size
settings, use the `ChatLib.getChatBreak(seperator)` method. This can take any seperator, like "-" as we used in the
example.

<aside class="success">Any length string can be used as the seperator, such as "seperate". However, because this is a long
string, there will be a gap at the end where another use of the string will not fit</aside>

## Editing chat

> This is how you edit a chat message after it has been sent to chat

```javascript
ChatLib.chat("Hey there! This will change...");

ChatLib.editChat("Hey there! This will change...", "And... changed!")
```

`ChatLib.editChat(message, replacer)` is a simple method that takes in an unformatted message and replaces all instances
of it with the replacer. This is a _slightly_ laggy operation if done extremely rapidly, like 60 times per second.

## Chat message from event

> This is how you get the unformatted message from a chat event

```javascript
function onChatReceived(event) {
  var unformattedMessage = ChatLib.getChatMessage(event);
}
```

> This is how you get the formatted message from a chat event

```javascript
function onChatReceived(event) {
  var formattedMessage = ChatLib.getChatMessage(event, true);
}
```

### Formatted chat

To get the unformatted chat from a chat event passed into a function by a Chat Trigger, pass it to the
`ChatLib.getChatMessage(event)` method, which returns an unformatted string.

### Unformatted chat

However, if you want the formatted version of the chat message, append the `true` flag to the
`ChatLib.getChatMessage(event, formatted`) method.