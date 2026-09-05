---
icon: lucide/scroll
---

# Logging
!!! warning
	Prerequisites:

	- [Core](core.md)

	Any code from the prerequisites can be **omitted** to make it easier to read. If you do want the complete code, look at the [repository examples](https://github.com/thiagola92/discord-social-sdk/tree/main/demo/examples).  

One of the first things you should do is add a log callback, otherwise you will never know when something goes wrong with your client.  

```gdscript title="GDScript" linenums="1" hl_lines="11 18-21"
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
