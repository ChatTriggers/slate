---
title: ChatTriggers Tutorial

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

search: true

code_clipboard: true
---

# Introduction

ChatTriggers is a framework for Minecraft Forge that allows for mods to be scripted, in
languages such as JavaScript. Scripts are able to be hot reloaded, which means
you can make changes to your mod without restarting!

<aside class="warning">
  Currently, only JavaScript is supported.
</aside>

JavaScript scripts are executed with our custom fork of Mozilla's
[Rhino](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/Rhino)
library. Our custom fork supports many ES6 and ESNEXT features. For the (almost)
full feature list,check our
[compatibility table](https://chattriggers.github.io/rhino/);

<aside class="warning">
  Some of this documentation may be out of date or incomplete.
</aside>

# Setup

To set up ChatTriggers, all you have to do is put the ChatTriggers jar into your `mods` folder,
and launch Minecraft. By default, ChatTriggers modules are stored in
`.minecraft/config/ChatTriggers/modules/`, but this can be changed in the
configuration. To access the ChatTriggers settings, type `/ct settings` in game. The
rest of this tutorial will refer to this directory as the "modules directory",
and will assume it is in the default location.

# Creating a module

To create a module, create a folder in your modules folder. The folder can have
whatever name you want, but typically it is just the name of the module. Our
module will be called ExampleModule. Our folder structure now looks like
`.minecraft/config/ChatTriggers/modules/ExampleModule`.

## The metadata file

> An example `metadata.json` file

```json
{
  "name": "ExampleModule",
  "creator": "YourNameHere",
  "description": "Our first module!",
  "entry": "index.js"
}
```


All modules MUST have a `metadata.json` file.
This file contains important information about our module. You can see an
example of this file to the right. The metadata file contains a number of
important fields, documented here:

- `name`: The name of the module
- `creator`: The name of the module creator
- `version`: The version of the module. This should conform to
  [SEMVER](https://semver.org/)
- `entry`: This is the name of a file that should actually be ran. This key is
  necessary if, for example, your module registers triggers or commands. This field is
  **REQUIRED** for all modules that wish to run code, without it your module will NOT run.
  If all your module provides is a library that other modules can use, this is not needed.
- `requires`: An array of names of other modules that your module depends on.
  These modules are guaranteed to be loaded before your module, allowing you to
  use them directly.
- `asmEntry` and `asmExposedFunctions`: These will be discussed later in the ASM
  section

## Scripting

We now need to create our script files. Typically, the root file of your module
is named `index.js`. This is a general web development practice. You can name
your files whatever your want, however one benefit of having an `index.js` file
is that if someone tries to import from your module folder directly, instead of
a specific file in your module, it will be imported from the `index.js` file, if
one exists. If no `index.js` file exists, other people will have to import
directly from the files themselves.

# The Basics

## Registering a Trigger

We register a WorldLoad trigger like so:

> The argument passed into the register function is the function you want to trigger:

```javascript
function exampleWorldLoad() {

}

register("worldLoad", exampleWorldLoad);
```

> You can also register an anonymous function for simplicity:

```javascript
register("worldLoad", () => {

});
```

In ctjs, "triggers" are events that get fired
when a certain action happens in game, like a sound being played or a chat
message being sent. Let's start with one of the simplest triggers: WorldLoad. In
order to register a trigger, we use the provided `register` function. It takes
the trigger name as the first argument (case-insensitive), and the function to
run as the second argument. You can see a complete list of these triggers on our
[javadocs, under `IRegister`](https://www.chattriggers.com/javadocs/com/chattriggers/ctjs/engine/IRegister.html).

<aside class="notice">
  To convert an IRegister name to a trigger name, just remove the "register" from the beginning of the method name.
</aside>

Now any code inside of our `exampleWorldLoad` trigger will be ran whenever a world is loaded. From here, you
can do many things, such as interacting with Minecraft methods or getting information from arguments that
certain triggers may pass in.

<aside class="notice">
  You can register as many triggers as you want, and you can even use the same function in multiple different triggers.
</aside>

## Chat Trigger

> A basic chat trigger

```js
register("chat", (player, message, event) => {
  ChatLib.chat(player + ": " + message);
}).setCriteria("<${player}> ${message}");
```

This is how you can set a chat trigger. Chat triggers trigger when a chat message matches the specific criteria that is set.
In this case, the trigger will fire whenever a chat message matches a chat message `<${player}> ${message}`. You can reference
variables by adding `${ }` around the variable name. All variables must be accounted for in the function parameters, following the order they appear in.
Also keep in mind that the event that is fired is always the last parameter.

> You may notice that the original message is still being sent, which looks really ugly. To fix this, we can cancel the event.

```js
register("chat", (player, message, event) => {
  cancel(event);
  ChatLib.chat(player + ": " + message);
}).setCriteria("<${player}> ${message}");
```

Setting criteria as it is in the example to the right will try to match the exact message. In order to allow a message to just
contain the criteria, you can add `.setContains()` after `setCriteria`. An example of this is to the right. With this, `hi Player! how are you?`, etc. will also trigger. These are just simple examples, but the idea still is there. The message just has to contain the criteria you set when you add `.setContains()`. There are also `setStart` and `setEnd` modifiers you can use instead.

Also, if the criteria you set contains color codes (starting with § or &), the message will try to match the exact color throughout the message. This can be beneficial or annoying depending on your use case.

> This will be triggered if a player says `hi User!` anywhere inside their message

```js
register("chat", (player, event) => {
  ChatLib.chat("howdy " + player);
}).setCriteria("hi ${player}!").setContains();
```

## MessageSent Trigger

> Display "Pong!" in response to YOUR message sent containing "ping"

```js
register("messageSent", (message, event) => {
  if (message.toLowerCase().includes("ping")) {
    ChatLib.chat("Pong!");
  }
});
```

In addition to letting you know when an event has occurred by calling your function, many triggers pass through additional information about their respective event. Let's take a look at a different trigger: MessageSent. This trigger is fired whenever the client (you) sends a message.

Let's make a trigger that, whenever you sent a message with the word "ping", displays the message "Pong!". In order to do this, we need to accept the arguments passed in by the MessageSent trigger. You can see all the arguments that a trigger passes through in the javadocs linked above. The MessageSent trigger passes in the message event and the actual message.

Many triggers are _cancellable_, meaning that they actually fire _before_ the event in question happens. In our case, our MessageSent trigger will fire before the chat message is actually sent. Cancelling the event is as easy as calling `cancel(event)`, however we won't do that here.

We are interested in the message parameter. We simply check if it contains the word we are interested in, and if so, we use the `ChatLib` utility to send a message only visible to the player. You can read more about the `ChatLib` utility [here](#chatlib)

## Command Trigger

Another one of the most common triggers is the Command trigger. This allows the user to make custom commands of their choosing.

```js
register("command", (user) => {
  ChatLib.chat("Hi " + user);
}).setName("mycommand");
```

Commands are one of the few triggers that do not have event as a parameter. This example creates a command which can be called through `/mycommand`. The arguments of the function are the arguments the user types into the command. If the user types `/mycommand kerbybit`, then the function will be triggered, and `Hi kerbybit` will be printed into chat.

<aside class="warning">
  Forge has a bug which will make commands with capital letters as the base to not fire. Set the full command name lowercase to avoid the issue.
</aside>

## Module Organization

> ES6 style import syntax (preferred)

```js
import playerLocation from "WhereAmi";
```

This imports a function from WhereAmI's index file, you could specify additional files i.e. WhereAmI/otherfile
You can also import from other files within the current module using local file paths such as `./folder/file` (where folder resides within WhereAmI).

> Node style require syntax

```js
const playerLocation = require("WhereAmI/index");
```

This imports a function from another module, called WhereAmI, to get a players location.

When making a module, it is important to know how ChatTriggers loads your files so you can organize them efficiently. When your module is loaded, only the file specified as the entry in `metadata.json` is loaded. Any other code you want to run must be imported, through the `require` syntax, or ES6 style `import` syntax.

<aside class="warning">In order to use variables and functions defined in other modules, you must list those module names in your module's <code>requires</code> array in the <code>metadata.json</code> file, and then import them as needed</aside>
