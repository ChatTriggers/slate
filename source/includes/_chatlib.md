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