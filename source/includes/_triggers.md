# Triggers

This is a list of all the Triggers currently available:

Trigger | Description | Cancelable
------- | ----------- | ----------
ActionBar | Fires when a message is displayed in the action bar | yes
BlockBreak | Fires when a block is broken | yes
[Chat](#chat-triggers) | Fires when a chat event is received | yes
ChatComponentClicked | Fires when the user clicks a chat component | yes
ChatComponentHover | Fires when the user hoveres over a chat component | yes
Clicked | Fires when a certain position is clicked | no
Command | Fires when a specified command is run by the player | no
Dragged | Fires as a specified mouse button is being held down | no 
DrawBlockHighlight | Fires when an outline is drawn over the block being moused over | yes
DropItem | Fires when a player drops an item | no
GameLoad | Fires after a ct (re)load or when a module is deleted | no
GameUnload | Fires before ct (re)loads or a module is deleted | no
GuiKey | Fires when a key is pressed while a gui is open | yes
GuiMouseClick | Fires when the mouse is clicked while a gui is open | yes
GuiMouseDrag | Fires when the mouse is dragged across a gui screen | yes
GuiMouseRelease | Fires when a mouse button is released while a gui is open | yes
GuiOpened | Fires when a gui is opened | yes
GuiRender | Fires when a gui is rendered | no
ItemTooltip | Fires when a tooltip is shown to the user | no
MessageSent | Fires before the player sends a chat message | yes
NoteBlockPlay | Fires when a noteblock is played | yes
NoteBlockChange | Fires when a player changes the pitch of a noteblock | yes
PacketSent | Fires when a packet is sent from the client to the server | yes
PickupItem | Fires when the player picks up an item | yes
PlayerInteract | Fires when the player left or right clicks | yes
PlayerJoined | Fires when a player joined the current world | no
PlayerLeft | Fires when a player leaves the current world | no
RenderAir | Fires when the player's air level is rendered (i.e. while the player is underwater) | yes
RenderBossHealth | Fires when a boss health bar is being rendered | yes
RenderCrosshair | Fires as the crosshair is drawn | yes
RenderDebug | Fires when the debug screen (F3) is being drawn | yes
RenderEntity | Fires when an entity is rendered | yes
RenderExperience | Fires when the player's experience bar is being drawn | yes
RenderFood | Fires when the player's food (hunger) is being drawn | yes
RenderHealth | Fires when the player's health is being drawn | yes
RenderHotbar | Fires when the player's hotbar is being drawn | yes
RenderMountHealth | Fires when the mount's health (horse or pig) is being drawn | yes
[RenderOverlay](#rendering) | Fires when the game's overlay is rendered, tied to the game's FPS | no
RenderPlayerList | Fires when the player list (tab list) is being drawn | yes
RenderWorld | Fires when the world is rendered | no
ScreenshotTaken | Fires when the user takes a screenshot | yes
[SoundPlay](#sound-play-triggers) | Fired when a sound is played | yes
[Step](#step-triggers) | Fired a certain amount of times per second, no matter the FPS | no
Tick | Fired every time the Minecraft game loop is ran | no
WorldLoad | Fired when the game loads a world | no
WorldUnload | Fired when the game unloads a world | no

## Advanced registering

> This is how you unregister a trigger

```javascript
var myTrigger = TriggerRegister.registerChat("myMethod");

function myMethod() {
  myTrigger.unregister();
}
```

> This is how you re-register a trigger

```javascript
var myTrigger = TriggerRegister.registerChat("myMethod");

function myMethod() {
  myTrigger.unregister();
}

myTrigger.register();
```

> This is how you check if a trigger is registered

```javascript
var myTrigger = TriggerRegister.registerChat("myMethod");

if (myTrigger.isRegistered()) {
    
}
```

In this first example, the "myMethod" function will only be ran once, because it is unregistered in the function.

In the second example, we simply re-register the trigger, this can be done at anytime.

In the last example, we are only running a block of code if the trigger is actually registered.

<aside class="warning">If you register a trigger that hasn't been unregistered, it will register again!</aside>

<aside class="success">You can still set special parameters for each trigger, and then save it in a variable, or you
could even set special parameters later!</aside>

## General triggers

> How to set the priority of any trigger

```javascript
TriggerRegister.registerChat("myChatMethod").setPriority(Priority.HIGH);
TriggerRegister.registerRenderOverlay("myChatMethod").setPriority(Priority.LOW);
```

These examples apply to all triggers, not just the examples given.

### Setting priority

This sets the order in which triggers of the same type are ran. Available priorities are LOWEST, LOW, NORMAL, HIGH, and HIGHEST.
Triggers with a priority of LOWEST are ran first, then LOW, all the way until HIGHEST.

## Chat triggers

A chat trigger is a trigger that responds to chat messages. You have plenty of options to customize on which specific
message, or types of messages, you wish to respond to. You can cancel this event, so you can customize chat _very_
heavily.

### Creation

> This is how you create a chat trigger

```javascript
TriggerRegister.registerChat("myChatMethod");

function myChatMethod() {
  
}
```

This is how you create the simplest possibly chat trigger that activates on all chat messages.

> These are all of the available settings for chat triggers

```javascript
var myChatTrigger = TriggerRegister.registerChat("myChatMethod");

myChatTrigger.setChatCriteria("<${*}> ${message}");
myChatTrigger.setParameter("<s>");
```

### Set chat criteria

Setting the chat criteria sets what chat messages this trigger will receive. In our case, it only fires for chat
messages that have something inside of `<>`'s, and then something afterward.

The `${}` we created are variables, thatmeans they will match anything, but have to match the parts around them too
If the variable has a name inside the block, it will be passed to your callback function. However, if it is just a star,
it won't be.

### Set criteria parameter

The "parameter" is a special flag for the type of check that should be done on the criteria. By default, this is
if the chat message is the same as what the message was (including variables).

The other options are `"<s>"` for if the message starts with your criteria and then can have anything else afterwards.
`"<c>"` is for if the message contains your criteria, and finally, `"<e>"` checks if your criteria comes at the very end
of the chat message.

## Sound play triggers