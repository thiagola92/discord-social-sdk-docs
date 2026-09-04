---
icon: lucide/party-popper
---

# Lobbies
!!! warning
	Prerequisites:

	- [Account linking](account_linking.md)
		- Using `DiscordClient.get_default_communication_scopes()`
	- [Status](status.md)

	Any code from the prerequisites can be **omitted** to make it easier to read. If you do want the complete code, look at the [repository examples](https://github.com/thiagola92/discord-social-sdk/tree/main/demo/examples).  

Lobbies are groups of users that can communicate via text and voice.  
They are **not** the text/voice channels that you see in your Discord server.  

??? note "Lobby vs Channel"
    - Lobby is a **temporary** group to be used for communication with people that you just matched  
        - Represents a matchmaking lobby
    - Channel is a **permanent** group to be used for communication with people that you may know  
        - Represents a place to hangout with your friends/community

## Create/Join
It will create the lobby if doesn't exist and join if it does.  

```gdscript title="GDScript" linenums="1" hl_lines="24 27-31"
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
	
	if status == DiscordClientStatus.READY:
		client.create_or_join_lobby("your-unique-lobby-secret", _on_joined_lobby)


func _on_joined_lobby(result: DiscordClientResult, lobby_id: int) -> void:
	if result.successful():
		print("🎮 Lobby created or joined successfully! Lobby Id: %s" % lobby_id)
	else:
		print("❌ Lobby creation/join failed")
```

## Leave
Is highly recommended to leave the lobby after using (it will delete itself after some minutes without nobody using).  

```gdscript title="GDScript" linenums="1" hl_lines="33 38-42"
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
	
	if status == DiscordClientStatus.READY:
		client.create_or_join_lobby("your-unique-lobby-secret", _on_joined_lobby)


func _on_joined_lobby(result: DiscordClientResult, lobby_id: int) -> void:
	if result.successful():
		print("🎮 Lobby created or joined successfully! Lobby Id: %s" % lobby_id)
		
		await get_tree().create_timer(60).timeout
		
		client.leave_lobby(lobby_id, _on_left_lobby)
	else:
		print("❌ Lobby creation/join failed")


func _on_left_lobby(result: DiscordClientResult) -> void:
	if result.successful():
		print("🎮 Left lobby successfully!")
	else:
		print("❌ Leaving lobby failed")
```

## Send Message
```gdscript title="GDScript" linenums="1" hl_lines="31 40-44"
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
	
	if status == DiscordClientStatus.READY:
		client.create_or_join_lobby("your-unique-lobby-secret", _on_joined_lobby)


func _on_joined_lobby(result: DiscordClientResult, lobby_id: int) -> void:
	if result.successful():
		print("🎮 Lobby created or joined successfully! Lobby Id: %s" % lobby_id)
		
		client.send_lobby_message(lobby_id, "Hello", _on_lobby_message)
		
		await get_tree().create_timer(60).timeout

		client.leave_lobby(lobby_id, _on_left_lobby)
	else:
		print("❌ Lobby creation/join failed")


func _on_lobby_message(result: DiscordClientResult, message_id: int) -> void:
	if result.successful():
		print("📨 Message sent successfully! Message Id: %s" % message_id)
	else:
		print("❌ Message sending failed")


func _on_left_lobby(result: DiscordClientResult) -> void:
	if result.successful():
		print("🎮 Left lobby successfully!")
	else:
		print("❌ Leaving lobby failed")
```

## Receive Message
Exactly the same for receving direct messages:  

```gdscript title="GDScript" linenums="1" hl_lines="11 52-56"
extends Node


var application_id: int = 123456789012345678

var client := DiscordClient.new()


func _ready() -> void:
	client.set_application_id(application_id)
	client.set_message_created_callback(_on_message_created)
	client.set_status_changed_callback(_on_status_changed)


func _process(_delta: float) -> void:
	Discord.run_callbacks()


func _on_log(message: String, severity: DiscordLoggingSeverity.Enum) -> void:
	var enum_str: String = Discord.enum_to_string(severity, DiscordLoggingSeverity.id)
	
	print("[%s] %s" % [enum_str, message])


func _on_status_changed(status: DiscordClientStatus.Enum, _error: DiscordClientError.Enum, _error_detail: int) -> void:
	var enum_str: String = Discord.enum_to_string(status, DiscordClientStatus.id)
	
	print("Status changed to %s" % enum_str)
	
	if status == DiscordClientStatus.READY:
		client.create_or_join_lobby("your-unique-lobby-secret", _on_joined_lobby)


func _on_joined_lobby(result: DiscordClientResult, lobby_id: int) -> void:
	if result.successful():
		print("🎮 Lobby created or joined successfully! Lobby Id: %s" % lobby_id)
		
		await get_tree().create_timer(60).timeout
		
		client.leave_lobby(lobby_id, _on_left_lobby)
	else:
		print("❌ Lobby creation/join failed")


func _on_left_lobby(result: DiscordClientResult) -> void:
	if result.successful():
		print("🎮 Left lobby successfully!")
	else:
		print("❌ Leaving lobby failed")


func _on_message_created(message_id: int) -> void:
	var message = client.get_message_handle(message_id)
	
	if message is DiscordMessageHandle:
		print("📨 New message received: " % message.content())
```

## Message History
Retrive recent messages from the lobby:  

```gdscript title="GDScript" linenums="1" hl_lines="38 47-54"
extends Node


var application_id: int = 123456789012345678

var client := DiscordClient.new()


func _ready() -> void:
	client.set_application_id(application_id)
	client.set_message_created_callback(_on_message_created)
	client.set_status_changed_callback(_on_status_changed)


func _process(_delta: float) -> void:
	Discord.run_callbacks()


func _on_log(message: String, severity: DiscordLoggingSeverity.Enum) -> void:
	var enum_str: String = Discord.enum_to_string(severity, DiscordLoggingSeverity.id)
	
	print("[%s] %s" % [enum_str, message])


func _on_status_changed(status: DiscordClientStatus.Enum, _error: DiscordClientError.Enum, _error_detail: int) -> void:
	var enum_str: String = Discord.enum_to_string(status, DiscordClientStatus.id)
	
	print("Status changed to %s" % enum_str)
	
	if status == DiscordClientStatus.READY:
		client.create_or_join_lobby("your-unique-lobby-secret", _on_joined_lobby)


func _on_joined_lobby(result: DiscordClientResult, lobby_id: int) -> void:
	if result.successful():
		print("🎮 Lobby created or joined successfully! Lobby Id: %s" % lobby_id)
		
		client.get_lobby_messages_with_limit(lobby_id, 50, _on_lobby_history)
		
		await get_tree().create_timer(60).timeout
		
		client.leave_lobby(lobby_id, _on_left_lobby)
	else:
		print("❌ Lobby creation/join failed")


func _on_lobby_history(result: DiscordClientResult, messages: Array[DiscordMessageHandle]) -> void:
	if result.successful():
		print("🕰 Retrieved %s messages from lobby chat history!" % messages.size())
		
		for message in messages:
			print("Message: %s" % message.content())
	else:
		print("❌ Failed to retrieve lobby chat history")


func _on_left_lobby(result: DiscordClientResult) -> void:
	if result.successful():
		print("🎮 Left lobby successfully!")
	else:
		print("❌ Leaving lobby failed")


func _on_message_created(message_id: int) -> void:
	var message = client.get_message_handle(message_id)
	
	if message is DiscordMessageHandle:
		print("📨 New message received: " % message.content())
```

## References
- [Creating and Managing Lobbies](https://docs.discord.com/developers/discord-social-sdk/development-guides/managing-lobbies)