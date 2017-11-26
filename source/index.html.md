---
title: ct.js Tutorial

language_tabs: # must be one of https://git.io/vQNgJ
  - javascript

toc_footers:
  - <a href='http://chattriggers.com'>ChatTriggers Website</a>
  - <a href='https://discordapp.com/invite/0fNjZyopOvBHZyG8'>ChatTriggers Discord</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
- chatlib
- rendering
- objects
- minecraftvars
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
 `.minecraft/mods/ChatTriggers/modules` and `.minecraft/mods/ChatTriggers/libs`.
 
# Creating a Module

To create a module, create a folder in your `.minecraft/mods/ChatTriggers/modules` folder, and have it's name be the name
of your module. Our module will be called Example. Our folder structure now looks like<br/> `.minecraft/mods/ChatTriggers/modules/Example/`.

We now need to create our scripts, so create a file in the folder named whatever you would like, the name is only for your
own management of the module. We'll call our main file `main`.

<aside class="notice">
Make the extension of the file be the normal extension for your specified language, i.e. main.js for JavaScript.
</aside>

# The Basics

## Registering a Trigger

>We register a WorldLoad trigger like so:

```javascript
TriggerRegister.registerWorldLoad("exampleImportWorldLoad");
```

The base of ct.js modules are "Triggers". These are events that get fired when a certain action happens in game,
like a sound is played, or chat message is received. A full list of these is at the [bottom of the page](#triggers).
 
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

>A chat trigger that gets fired on the chat message <code>&lt;FalseHonesty&gt; Hello World!</code>

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

>To cancel a cancelable event, do this:

```javascript
TriggerRegister.registerChat("exampleImportChat").setChatCriteria("<${*}> ${message}");

function exampleImportChat(message, event) {
  event.setCanceled(true);
  
  ChatLib.chat("I did cancel this: " + message + "!");
}
```

>The event parameter is optional, so this works just the same if you don't need to use the event

```javascript
TriggerRegister.registerChat("exampleImportChat").setChatCriteria("<${*}> ${message}");

function exampleImportChat(message) {
  ChatLib.chat("I didn't cancel this: " + message + "!");
}
```

Some events are cancelable, like chat events. If it is cancelable, the last parameter passed into the function will always
be the event
