---
icon: lucide/power
---

# Launch
There is situations where you want Discord to launch your game. For example, when you accept your friend invite to play the game.  

To solve that, you need to register inside Discord client how to launch your game (at least once).  

!!! tip
	It's recommended to do that as soon as possible (e.g. when the game open).

## Launch Command
```gdscript title="GDScript"
func _ready() -> void:
	client.register_launch_command(application_id, "/the/for/the/game/executable")
```

We never know where the user will install the executable, so you should use some logic to find out. For example, registering the current path at runtime:  

```gdscript title="GDScript"
func _ready() -> void:
	client.register_launch_command(application_id, OS.get_executable_path())
```

Discord [example](https://docs.discord.com/developers/discord-social-sdk/development-guides/managing-game-invites#registering-a-launch-command-2) use [deep link](https://en.wikipedia.org/wiki/Mobile_deep_linking) (also knows as custom URI scheme in desktops):  

```gdscript title="GDScript"
func _ready() -> void:
	client.register_launch_command(application_id, "yourgame://")
```

## Launch Steam Game
In case your game was installed through [Steam](https://store.steampowered.com/), Discord knows how to make Steam launch your game just by using the [Steam App ID](https://partner.steamgames.com/doc/store/application).  

```gdscript title="GDScript"
func _ready() -> void:
	client.register_launch_steam_application(application_id, steam_app_id)
```