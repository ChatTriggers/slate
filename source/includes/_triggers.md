# Triggers

This is a list of all the Triggers currently available:

Trigger | Description | Cancelable
------- | ----------- | ----------
[Chat](#chat-triggers) | Fires when a chat event is received | yes
[RenderOverlay](#rendering) | Fires when the game's overlay is rendered, tied to the game's FPS | no
[SoundPlay](#sound-play-triggers) | Fired when a sound is played | no
[Step](#step-triggers) | Fired a certain amount of times per second, no matter the FPS | no
Tick | Fired every time the Minecraft game loop is ran | no
WorldLoad | Fired when the game loads a world | no
WorldUnload | Fired when the game unloads a world | no
Clicked | Fires when a certain position is clicked | no
Command | Fires when a specified command is run by the player | no
Dragged | Fires as a specified mouse button is being held down | no 
GuiOpened | Fires when a gui is opened | yes
RenderAir | Fires when the player's air level is rendered (i.e. while the player is underwater) | yes
RenderBossHealth | Fires when a boss health bar is being rendered | yes
RenderCrosshair | Fires as the crosshair is drawn | yes
RenderDebug | Fires when the debug screen (F3) is being drawn | yes
RenderExperience | Fires when the player's experience bar is being drawn | yes
RenderFood | Fires when the player's food (hunger) is being drawn | yes
RenderHealth | Fires when the player's health is being drawn | yes
RenderHotbar | Fires when the player's hotbar is being drawn | yes
RenderMountHealth | Fires when the mount's health (horse or pig) is being drawn | yes
RenderPlayerList | Fires when the player list (tab list) is being drawn | yes
SoundPlay | Fires when a specified sound is played | yes

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