---
icon: lucide/scroll
---

# Logging
One of the first things you should do is add a log callback, otherwise you will never know when something goes wrong with your client.  

```gdscript title="GDScript" linenums="1" hl_lines="11 18 19 20 21"
extends Node


var application_id: int = 123456789012345678

var client := DiscordClient.new()


func _ready() -> void:
	client.set_application_id(application_id)
	client.add_log_callback(_on_log, DiscordLoggingSeverity.INFO)


func _process(_delta: float) -> void:
	Discord.run_callbacks()


func _on_log(message: String, severity: DiscordLoggingSeverity.Enum) -> void:
	var enum_str: String = Discord.enum_to_string(severity, DiscordLoggingSeverity.id)

	print("[%s] %s" % [enum_str, message])
```

## References
- [Debug & Log](https://docs.discord.com/developers/discord-social-sdk/how-to/debug-log)
- SDK Documentation
    - [client.add_log_callback()](https://discord.com/developers/docs/social-sdk/classdiscordpp_1_1Client.html#af78996cff24a40f5dc7066beed16692c)
    - [Discord.enum_to_string()](https://discord.com/developers/docs/social-sdk/namespacediscordpp.html#a0fd967a23d2d106ced3d6669b9a810ad)