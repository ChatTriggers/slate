# Triggers

This is a list of all the Triggers currently available:

Trigger | Explanation | Cancelable
------- | ----------- | ----------
Chat | Fires when a chat event is received | true
RenderOverlay | Fires when the game's overlay is rendered, tied to the game's FPS | false
SoundPlay | Fired when a sound is played | false
Step | Fired a certain amount of times per second, no matter the FPS | false
Tick | Fired every time the Minecraft game loop is ran | false
WorldLoad | Fired when the game loads a world | false
WorldUnload | Fired when the game unloads a world | false