---
icon: lucide/message-circle-more
---

# Direct Messages
!!! warning
	Prerequisites:

	- [Account linking](account_linking.md)
		- Using `DiscordClient.get_default_communication_scopes()`
	- [Status](status.md)

	Any code from the prerequisites can be **omitted** to make it easier to read. If you do want the complete code, look at the [repository examples](https://github.com/thiagola92/discord-social-sdk/tree/main/demo/examples).  

## Sending
```gdscript title="GDScript" linenums="1" hl_lines="6 26-28 31-35"
extends Node


var application_id: int = 123456789012345678

var target_id: int = 987654323109876543

var client := DiscordClient.new()


func _ready() -> void:
    client.set_application_id(application_id)
    client.set_status_changed_callback(_on_status_changed)


func _process(_delta: float) -> void:
    Discord.run_callbacks()


func _on_status_changed(status: DiscordClientStatus.Enum, _error: DiscordClientError.Enum, _error_detail: int) -> void:
    var enum_str: String = Discord.enum_to_string(status, DiscordClientStatus.id)

    print("Status changed to %s" % enum_str)
	
	if status == DiscordClientStatus.READY:
		var message := "ready to queue?"
		
		client.send_user_message(target_id, message, _on_message_sent)


func _on_message_sent(result: DiscordClientResult, message_id: int) -> void:
	if result.successful():
		print("✅ Message sent successfully (message id: %s)" % message_id)
	else:
		print("❌ Failed to send message: %s" % result.error())
```

### Supressing double notifications
Tell Discord client that you are already showing the chat so it shouldn't notificate the user about new messages (don't forget to turn off when not showing chat anymore):  

```gdscript linenums="1"
client.set_showing_chat(true)
```

## Receiving
```gdscript title="GDScript" linenums="1" hl_lines="11 18-22"
extends Node


var application_id: int = 123456789012345678

var client := DiscordClient.new()


func _ready() -> void:
    client.set_application_id(application_id)
	client.set_message_created_callback(_on_message_created)


func _process(_delta: float) -> void:
    Discord.run_callbacks()


func _on_message_created(result: DiscordClientResult, message_id: int) -> void:
	var message = client.get_message_handle(message_id)
	
	if message is DiscordMessageHandle:
		print("New message from %s: %s" % [message.author_id(), message.content()])
```

Watch for changes so you can update the game messages in your game too:  

```gdscript title="GDScript" linenums="1" hl_lines="12 26-30"
extends Node


var application_id: int = 123456789012345678

var client := DiscordClient.new()


func _ready() -> void:
    client.set_application_id(application_id)
	client.set_message_created_callback(_on_message_created)
	client.set_message_updated_callback(_on_message_updated)


func _process(_delta: float) -> void:
    Discord.run_callbacks()


func _on_message_created(result: DiscordClientResult, message_id: int) -> void:
	var message = client.get_message_handle(message_id)
	
	if message is DiscordMessageHandle:
		print("New message from %s: %s" % [message.author_id(), message.content()])


func _on_message_updated(message_id: int) -> void:
	var message = client.get_message_handle(message_id)
	
	if message is DiscordMessageHandle:
		print("Message from %s updated: %s" % [message.author_id(), message.content()])
```

Remember to also watch for messages that are deleted:  

```gdscript title="GDScript" linenums="1" hl_lines="13 34-38"
extends Node


var application_id: int = 123456789012345678

var client := DiscordClient.new()


func _ready() -> void:
    client.set_application_id(application_id)
	client.set_message_created_callback(_on_message_created)
	client.set_message_updated_callback(_on_message_updated)
	client.set_message_deleted_callback(_on_message_deleted)


func _process(_delta: float) -> void:
    Discord.run_callbacks()


func _on_message_created(result: DiscordClientResult, message_id: int) -> void:
	var message = client.get_message_handle(message_id)
	
	if message is DiscordMessageHandle:
		print("New message from %s: %s" % [message.author_id(), message.content()])


func _on_message_updated(message_id: int) -> void:
	var message = client.get_message_handle(message_id)
	
	if message is DiscordMessageHandle:
		print("Message from %s updated: %s" % [message.author_id(), message.content()])


func _on_message_deleted(message_id: int, channel_id: int) -> void:
	var message = client.get_message_handle(message_id)
	
	if message is DiscordMessageHandle:
		print("Message from %s in %s deleted" % [message.author_id(), channel_id])
```

## References
- [Sending Direct Messages](https://docs.discord.com/developers/discord-social-sdk/development-guides/sending-direct-messages)