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
- SDK Documentation
	- [`DiscordClient`](https://discord.com/developers/docs/social-sdk/classdiscordpp_1_1Client.html)
	- [`client.set_application_id()`](https://discord.com/developers/docs/social-sdk/classdiscordpp_1_1Client.html#ad452335c06b28be0406dab824acccc49)
	- [`Discord.run_callbacks()`](https://discord.com/developers/docs/social-sdk/namespacediscordpp.html#ab5dd8cf274f581ee1885de5816be3c29)  