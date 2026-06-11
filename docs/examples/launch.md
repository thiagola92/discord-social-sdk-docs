---
icon: lucide/power
---

# Launch
There is situations that you want the Discord launching the game for you. For example, when you accept your friend invite to play the game.  

## Launch Command
Register inside Discord client the command that it must use to launch your game.  

```gdscript title="GDScript"
func _ready() -> void:
	client.register_launch_command(application_id, "/my/game/path")
```

## Launch Steam Game