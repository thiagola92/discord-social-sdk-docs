---
icon: lucide/ethernet-port
---

# Status
!!! warning
	Prerequisites:

	- [Core](core.md)

	Any code from the prerequisites can be **omitted** to make it easier to read. If you do want the complete code, look at the [repository examples](https://github.com/thiagola92/discord-social-sdk/tree/main/demo/examples).  

!!! note
	This refers to the status of the internal websocket that the SDK uses to communicate with Discord.  
	Do not confuse this with the user status (online, offline, busy, ...).   

Monitoring the status of your connection with the Discord is important because some Discord methods only works when the status is `READY`.  

Running the following code will not provide any output (even if you setup logging) because your status start as `DISCONNECTED` and only change when you attempt to connect to Discord, which we will cover in [Account Linking](account_linking.md).  

```gdscript title="GDScript" linenums="1" hl_lines="11 18-21"
extends Node


var application_id: int = 123456789012345678

var client := DiscordClient.new()


func _ready() -> void:
	client.set_application_id(application_id)
	client.set_status_changed_callback(_on_status_changed)


func _process(_delta: float) -> void:
	Discord.run_callbacks()


func _on_status_changed(status: DiscordClientStatus.Enum, _error: DiscordClientError.Enum, _error_detail: int) -> void:
	var enum_str: String = Discord.enum_to_string(status, DiscordClientStatus.id)
	
	print("Status changed to %s" % enum_str)
```

# References
- [Monitoring Connection Status](https://docs.discord.com/developers/discord-social-sdk/getting-started/using-c++#monitoring-connection-status)
