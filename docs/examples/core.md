---
icon: lucide/cpu
---

# Core
!!! warning
	Prerequisites:

	- [Access](../access.md)
	- [Installation](../installation.md)

This is the minimum that you will need for every single example:  

```gdscript title="GDScript"
extends Node


var application_id: int = 123456789012345678

var client := DiscordClient.new()


func _ready() -> void:
	client.set_application_id(application_id)


func _process(_delta: float) -> void:
	Discord.run_callbacks()
```

Every step is very important:  

- Create client to interact with Discord
- Identifying yourself to the client
- Run pending callbacks every frame

## References
- [Run Callbacks](https://docs.discord.com/developers/discord-social-sdk/getting-started/using-c++#step-7-run-callbacks)
