# MinecraftVars

MinecraftVars is an object containing many methods that provide information about the player
and the world around them. However, unlike the other objects, most of MinecraftVars' methods
have built in javascript variable names. As such, you rarely need to directly use the
`MinecraftVars` object in your code - you can simply use the built in variable names.

## Provided Variables

> Some examples below

```javascript
// Play a sound if the player's health gets too low.
TriggerRegister.registerTick("checkPlayerHealth");
function checkPlayerHealth() {
  if (hp <= 4) {
    WorldLib.playSound("random.orb", 100, 1);
  }
}
```

> Using the server variables to register certain triggers

```javascript
TriggerRegister.registerWorldLoad("checkServer");
function checkServer() {
  var trigger = TriggerRegister.registerChat("privateMessage");

  if (server === "MyFavoriteServer") {
    // Register trigger for MyFavoriteServer
    trigger.setChatCriteria("&r&4${type} ${name}&r: ${message}");
  } else if (server === "SomeOtherServer") {
    // Register trigger for SomeOtherServer
    trigger.setChatCriteria("${type} &b${name} - &r${message}");
  }
}

function privateMessage(type, name, message, event) {
  event.setCanceled(true);
  if (type === "To") {
    ChatLib.chat("&5ME > " + name + "&r: " + message);
  } else if (type === "From") {
    ChatLib.chat("&5ME < " + name + "&r: " + message);
  }
}
```

Below is a list of all the provided variable names, a brief description, and an example
return value. Keep in mind, you can use these variables anywhere you want to in your
javascript code, as they are already defined

Variable Name | Description | Return value
--------------|-------------|---------------------
hp | The player's health points | Integer between 0 and 20
hunger | The player's hunger points | Integer between 0 and 20
saturation | The player's saturation points | Float between 0 and 5.0
armorPoints | The player's armor points | Integer between 0 and 20
airLevel | The player's air level | Integer between -20 and 300. If negative, the player is taking damage. If 300, the player is not in water.
xpLevel | The player's XP level | Integer greater than or equal to 0.
xpProgress | The player's progress to the next XP level | A float between 0 and 1 (a percentage)
inChat | Whether or not the player has the chat window open | A boolean
inTab | Whether or not the player has the tab window open | A boolean
isSprinting | Whether or not the player is springing | A boolean
isSneaking | Whether or not the player is sneaking | A boolean
ping* | The ping to the current server | An integer
posX | The player's X position | A float
posY | The player's Y position | A float
posZ | The player's Z position | A float
motionX | The player's X velocity | A float
motionY | The player's Y velocity | A float
motionZ | The player's Z velocity | A float
biome | The biome the player is in | A string (ex: "Desert")
lightLevel | The lightLevel of the block the player is in | An integer
cameraPitch | The pitch value of the player's camera | A float
cameraYaw | The yaw value of the player's camera | A float
fps | The fps the player is getting | An integer
facing | One of eight directions the player is facing | A string (ex: "North", "North West")
leftArrow | Whether or not the left arrow key is pressed | A boolean
rightArrow | Whether or not the right arrow key is pressed | A boolean
upArrow | Whether or not the up arrow key is pressed | A boolean
downArrow | Whether or not the down arrow key is pressed | A boolean
tabbedIn | Whether or not the Minecraft window is active | A boolean
potEffects | A list of the currently active potion effects | A string array
serverIP* | The IP of the server the player is on | A string
serverMOTD* | The MOTD of the server the player is on | A string
server* | The name of the server the player is on | A string

<aside class="notice">* These variables relate to the server the player is currently on. However, if the
player is in a single player world, they give fixed values. serverMOTD and server will both return
"SinglePlayer", serverIP will return "localhost", and ping will return 5.</aside>

> Using getTabsList()

```javascript
// Search the tabs list for a particular user, and if found, say hi to them
TriggerRegister.registerWorldLoad("playerSearch")
function playerSearch() {
  for (var item in MinecraftVars.getTabsList()) {
    if (item === "MyBestFriend") {
      ChatLib.say("Hello, MyBestFriend!");
    }
  }
}
```

<br>
Because the above javascript variables are updated every tick, some methods don't have provided variables. These methods are
ones that don't necessary need to be updated every tick, as their use will be limited. Currently, there is only one on this list.

Method Name | Description | Return value
------------|-------------|-------------
getTabsList() | Gets the player names listed in tab | An ArrayList of strings
