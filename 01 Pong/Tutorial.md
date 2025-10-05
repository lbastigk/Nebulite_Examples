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

## Making Objects

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

### Player movement with constrain

### Score count to string

## Making objects

## Player-AI

## Creating a scene

## Result