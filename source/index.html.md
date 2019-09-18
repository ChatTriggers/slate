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

<aside class="warning">Some of this documentation may be out of date or incomplete.</aside>

# Setup

To setup a ct.js coding environment, all you have to do is put the ct.js jar into your `.minecraft/mods` folder, and launch 
Minecraft. In your mods folder, you will get a folder structure automatically created. The default file location is<br/>
 `.minecraft/config/ChatTriggers/modules/` but this can be changed in the configuration.
 
# Creating a module

> An example `metadata.json` file

```json
{
  "name": "Example",
  "creator": "YourNameHere",
  "description": "Our first module!"
}
```

To create an import, create a folder in your `.minecraft/config/ChatTriggers/modules` folder, and have it's name be the name
of your import. Our import will be called Example. Our folder structure now looks like<br/> `.minecraft/config/ChatTriggers/modules/Example`.

If we intend to share our module through the ctjs website, we'll need a `metadata.json` file. This file contains important information about our module. You can see an example of this file to the right. Another important field you may want to include at some point is the `required` key. This key should be an array of module names which your module depends on.

We now need to create our scripts, so create a file in the folder named whatever you would like, the name is only for your
own management of the import. We'll call our main file `main`.

<aside class="notice">
Make the extension of the file be the normal extension for your specified language, i.e. <code>main.js</code> for JavaScript.
</aside>

# The Basics

## Registering a Trigger

>We register a WorldLoad trigger like so:

```javascript
register("worldLoad", exampleWorldLoad);
```

>The argument passed into the register function is the function you want to trigger:

```javascript
function exampleWorldLoad() {
  
}

register("worldLoad", exampleWorldLoad);
```

>You can also register an anonymous function for simplicity:

```javascript
register("worldLoad", function() {

});
```

The base of ct.js imports are "Triggers". These are events that get fired when a certain action happens in game, like a sound being played or a chat message being sent. A full list of these is at the [bottom of the page](#triggers).
 
Let's start with one of the simplest trigger: WorldLoad. In order to register a trigger, we use the provided `register` function. It takes the trigger name as the first argument (case-insensitive), and the function as the second argument. The second argument can either be the name of a global function, or an anonymous function defined in the `register` call.

Now any code inside of our `exampleWorldLoad` trigger will be ran whenever a world is loaded. From here, you can do many things, such as interacting with Minecraft methods or getting information from arguments that certain triggers may pass in.

<aside class="notice">You can register as many triggers as you want, and you can even use the same function in multiple different triggers.</aside>

<aside class="notice">To find out what arguments a trigger passes to its function, take a look at the javadocs, <a href="https://www.chattriggers.com/javadocs/com/chattriggers/ctjs/engine/IRegister.html">located here</a></aside>

## Trigger arguments

> Preparing a trigger to intercept messages

```js
register('messageSent', function(event, message) {
  // TODO
});
```

> Display "Pong!" in response to a message sent containing "ping"

```js
register('messageSent', function(event, message) {
  if (message.toLowerCase().indexOf('ping') !== -1) {
    ChatLib.chat('Pong!');
  }
});
```

In addition to letting you know when an event has occurred by calling your function, many trigger pass through additional information about their respective event. Let's take a look at a different trigger: MessageSent. This trigger is fired whenever a player sends a message.

Let's make a trigger that, whenever the player sent a message with the word "ping", we display the message "Pong!". In order to do this, we need to accept the arguments passed in by the MessageSent trigger. You can see all the arguments that a trigger passes through in the javadocs linked above. The MessageSent trigger passes in the message event and the actual message.

Many triggers are _cancellable_, meaning that they actually fire _before_ whatever the event in question happens. In our case, our MessageSent trigger will fire before the chat message is actually sent. Cancelling the event is as easy as calling `cancel(event)`, however we won't do that here.

We are interested in the message parameter. We simply check if it contains the word we are interested in, and if so, we use the `ChatLib` utility to send a message only visible to the player. You can read more about the `ChatLib` utility [here](TODO)

## Module Organization

When making a module, it is important to know how ct.js loads your files so you can organize them efficiently. When your module is loaded, the root folder and any subfolders are scanned for files of the correct type (for example, `.js` files) and are loaded as one big file. 

Those with previous programming experience with Javascript may be familiar with NodeJS's `require` system, where in order to get access to code in another file, you have to call a function and provide the file path. In ct.js, all of your module files are compiled into one big file and loaded at once. This means that if you define a global variable in one file, you can use it immediately in any other file in your module.

In fact, all modules share the same global namespace. This means that if you define a global variable in your module, you can access it from any other module*. Because the global namespace is shared with all modules, it is highly recommended **NOT** to use common variable names, such as `x` or `player`, as global variable names. 

One way to handle global variables is to prefix any of your global variables with your module name, decreasing the chance of someone else using the same variable name. Another way is to define a single global object to store all of your variables and functions.

<aside class="warning">*In order to use variables and function defined in other modules, you must list those module names in your module's <code>required</code> array in the <code>metadata.json</code> file</aside>