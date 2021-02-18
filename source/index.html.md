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

search: true

code_clipboard: true
---

# Introduction

ct.js is a framework for Minecraft Forge that allows for mods to be scripted, in
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

To setup ct.js, all you have to do is put the ct.js jar into your `mods` folder,
and launch Minecraft. By default, ct.js modules are stored in
`.minecraft/config/ChatTriggers/modules/`, but this can be changed in the
configuration. To access the ct.js settings, type `/ct settings` in game. The
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

## Trigger arguments

> Preparing a trigger to intercept messages

```js
register('messageSent', (message, event) => {
  // TODO
});
```

> Display "Pong!" in response to a message sent containing "ping"

```js
register('messageSent', (message, event) => {
  if (message.toLowerCase().includes('ping')) {
    ChatLib.chat('Pong!');
  }
});
```

In addition to letting you know when an event has occurred by calling your function, many triggers pass through additional information about their respective event. Let's take a look at a different trigger: MessageSent. This trigger is fired whenever a player sends a message.

Let's make a trigger that, whenever the player sent a message with the word "ping", displays the message "Pong!". In order to do this, we need to accept the arguments passed in by the MessageSent trigger. You can see all the arguments that a trigger passes through in the javadocs linked above. The MessageSent trigger passes in the message event and the actual message.

Many triggers are _cancellable_, meaning that they actually fire _before_ the event in question happens. In our case, our MessageSent trigger will fire before the chat message is actually sent. Cancelling the event is as easy as calling `cancel(event)`, however we won't do that here.

We are interested in the message parameter. We simply check if it contains the word we are interested in, and if so, we use the `ChatLib` utility to send a message only visible to the player. You can read more about the `ChatLib` utility [here](#ChatLib)

## Module Organization

> ES6 style import syntax (preferred)

```js
// This imports a function from WhereAmI's index file, you could specify additional files i.e. WhereAmI/otherfile
// You can also import from other files within the current module using local file paths
// ...such as ./folder/file (where folder resides within WhereAmI)
import playerLocation from 'WhereAmi';
```

> Node style require syntax

```js
// This imports a function from another module, called WhereAmI, to get a players location
let playerLocation = require('WhereAmI/index');
```

When making a module, it is important to know how ct.js loads your files so you can organize them efficiently. When your module is loaded, only the file specified as the entry in `metadata.json` is loaded. Any other code you want to run must be imported, through the `require` syntax, or ES6 style `import` syntax.

<aside class="warning">In order to use variables and functions defined in other modules, you must list those module names in your module's <code>requires</code> array in the <code>metadata.json</code> file, and then import them as needed</aside>
