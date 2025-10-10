# Making a simple pong game with Nebulite

This tutorial covers the creation of a very simple Pong game with Nebulite.
It includes:

- Player-controlled paddles
- Rudimentary AI-controlled paddles
- Object collision and bouncing logic
- Score count

## Initialize Repository

```bash
git clone https://github.com/lbastigk/Nebulite
cd Nebulite
./install.sh
```
This will take a while. Grab a coffee and let Nebulite handle the install. 
The installation process will also run tests to see if the engine is working properly.

If you are using VSCode, you may benefit from the language extension of the repository.
See `./Languages/nebs/nebulite-script-vscode/`

We can now initialize an empty renderer:
```bash
./bin/Nebulite
```
<p align="center">
<img src="Images/Empty renderer.jpg" width=75%>
</p>

## Making Objects

The RenderObject class is a core component of Nebulite, representing a single renderable entity. They are defined via json/jsonc-files. 

### Modifying the standardfile

Inside the Nebulite directory, you can use
```bash
./bin/Nebulite standardfile renderobject
```
to generate a standard file `./Resources/Renderobjects/standard.jsonc`. Depending on the version of Nebulite, it may look like the following:
```jsonc
{
    "id": 0,
    "invokeSubscriptions": [
        "all"
    ],
    "invokes": [],
    "layer": 0,
    "posX": 0,
    "posY": 0,
    "sprite": {
        "link": "Resources/Sprites/TEST001P/001.bmp",
        "sizeX": 32,
        "sizeY": 32,
        "spritesheet": {
            "isSpritesheet": false,
            "offsetX": 0,
            "offsetY": 0,
            "sizeX": 0,
            "sizeY": 0
        }
    },
    "text": {
        "color": {
            "A": 255,
            "B": 255,
            "G": 255,
            "R": 255
        },
        "dx": 0.0,
        "dy": 0.0,
        "fontSize": 0,
        "str": ""
    }
}
```
which we can spawn with:
```bash
./bin/Nebulite spawn './Resources/Renderobjects/standard.jsonc'
```
<p align="center">
<img src="Images/Standard spawned.jpg" width=75%>
</p>

Note the litte red square at the top. That's our object. By modifying `posX` or `posY` inside the json file, and spawning the object again, we can move its position in the object container.

We will not use any prebuild Renderobjects from the Nebulite repository. Instead, we make a new directoy
```bash
./Resources/Pong/
```

for the following files.

### The ball

```bash
./Resources/Pong/ball.jsonc
```

We keep the dimensions and sprite of the standardfile and add
- invoke subscriptions
- invokes
- physical properties
- variable for setup

We may also remove the text properties, as we will not use them for this object.

```jsonc
{
    "id": 0,
    "invokeSubscriptions": [
        "all"
    ],
    "invokes": [
        "./Resources/Pong/ball_setup.jsonc",    // Sets the position according to render size
        "./Resources/Pong/integrate_v.jsonc",   // Integrate velocity to position
        "./Resources/Pong/bounce.jsonc",        // bounce on walls and paddles
        "./Resources/Pong/respawn_left.jsonc",  // Respawn if out of bounds, increase score for Player right
        "./Resources/Pong/respawn_right.jsonc"  // Respawn if out of bounds, increase score for Player left
    ],
    "layer": 0,
    "physics": {
        "vX": 4,
        "vY": 3
    },
    "posX": 0,
    "posY": 0,
    "setup_done": 0,    // If the ball was positioned correctly
    "sprite": {
        "link": "Resources/Sprites/TEST001P/001.bmp",
        "sizeX": 32,
        "sizeY": 32,
        "spritesheet": {
            "isSpritesheet": false,
            "offsetX": 0,
            "offsetY": 0,
            "sizeX": 0,
            "sizeY": 0
        }
    }
}
```

### The walls

```bash
./Resources/Pong/wall_top.jsonc
```

```jsonc
{
    "id": 0,
    "invokeSubscriptions": [
        "all",
        "bounce"    // Listens to the balls bounce ruleset
    ],
    "invokes": [
        "./Resources/Pong/wall_setup_top.jsonc",    // Sets the position and size according to render size
    ],
    "layer": 0,
    "posX": 0,
    "posY": 0,
    "setup_done": 0,    // If the wall was positioned correctly
    "sprite": {
        "link": "Resources/Sprites/TEST001P/001.bmp",
        "sizeX": 32,
        "sizeY": 32,
        "spritesheet": {
            "isSpritesheet": false,
            "offsetX": 0,
            "offsetY": 0,
            "sizeX": 0,
            "sizeY": 0
        }
    }
}
```

-------

```bash
./Resources/Pong/wall_bottom.jsonc
```

```jsonc
{
    "id": 0,
    "invokeSubscriptions": [
        "all",
        "bounce"    // Listens to the balls bounce ruleset
    ],
    "invokes": [
        "./Resources/Pong/wall_setup_bottom.jsonc",    // Sets the position and size according to render size
    ],
    "layer": 0,
    "posX": 0,
    "posY": 0,
    "setup_done": 0,    // If the wall was positioned correctly
    "sprite": {
        "link": "Resources/Sprites/TEST001P/001.bmp",
        "sizeX": 32,
        "sizeY": 32,
        "spritesheet": {
            "isSpritesheet": false,
            "offsetX": 0,
            "offsetY": 0,
            "sizeX": 0,
            "sizeY": 0
        }
    }
}
```

### The paddles

```bash
./Resources/Pong/paddle_left.jsonc
```

```jsonc
{
    "id": 0,
    "invokeSubscriptions": [
        "all",
        "bounce"    // Listens to the balls bounce ruleset
    ],
    "invokes": [
        "./Resources/Pong/paddle_setup_left.jsonc",     // Sets the position and size according to render size
        "./Resources/Pong/movement_player_left.jsonc",  // w-s-Keys to velocity
        "./Resources/Pong/integrate_v.jsonc"            // Integrate velocity to position
    ],
    "layer": 0,
    "posX": 0,
    "posY": 0,
    "setup_done": 0,    // If the paddle was positioned correctly
    "sprite": {
        "link": "Resources/Sprites/TEST001P/001.bmp",
        "sizeX": 32,
        "sizeY": 32,
        "spritesheet": {
            "isSpritesheet": false,
            "offsetX": 0,
            "offsetY": 0,
            "sizeX": 0,
            "sizeY": 0
        }
    }
}
```

-------

```bash
./Resources/Pong/paddle_right.jsonc
```

```jsonc
{
    "id": 0,
    "invokeSubscriptions": [
        "all",
        "bounce"    // Listens to the balls bounce ruleset
    ],
    "invokes": [
        "./Resources/Pong/paddle_setup_left.jsonc",     // Sets the position and size according to render size
        "./Resources/Pong/movement_player_left.jsonc",  // up-down-Keys to velocity
        "./Resources/Pong/integrate_v.jsonc"            // Integrate velocity to position
    ],
    "layer": 0,
    "posX": 0,
    "posY": 0,
    "setup_done": 0,    // If the paddle was positioned correctly
    "sprite": {
        "link": "Resources/Sprites/TEST001P/001.bmp",
        "sizeX": 32,
        "sizeY": 32,
        "spritesheet": {
            "isSpritesheet": false,
            "offsetX": 0,
            "offsetY": 0,
            "sizeX": 0,
            "sizeY": 0
        }
    }
}
```

### The Scoreboard

```bash
./Resources/Pong/scoreboard.jsonc
```

## Making Rulesets

We will not use any premade jsonc ruleset files. Instead, we will make our own.

Nebulite Ruleset files consist of:

- a topic they're broadcast to, so we can subscribe or unsubscribe objects from that ruleset
- a logical expression that needs to be true for the ruleset to be invoked
- a list of assignments
- a list of functioncalls

This allows us to write game logic like:

```
I am a fire.
Pick all trees in the forest (broadcast to topic onTree), 
see if they're very close to me (logical expression, check distance).
If that is the case, lower their health (assign other.health += -1) 
and modify their sprite to be on fire (functioncall on other: "sprite-modify style fire")
```

### Simple Physics: Velocity and Position

### Complex Physics: Collisions

### Setup based on display size

### Player movement with constrain

### Score count to string

## Player-AI

## Creating a scene

```bash
./Resources/pong.nebs
```

```bash
set-res 640 400 3 # Creates a renderer with a resolution of 640x400, window 3 times as big
spawn ... # TODO: Spawn all objects
```

## Result