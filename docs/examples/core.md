---
icon: lucide/cpu
---

# Core
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

- [x] Create client to interact with Discord
- [x] Identifying yourself to the client
- [x] Run pending callbacks every frame

## References
- [Run Callbacks](https://docs.discord.com/developers/discord-social-sdk/getting-started/using-c++#step-7-run-callbacks)