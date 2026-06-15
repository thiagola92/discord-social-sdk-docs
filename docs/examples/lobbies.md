---
icon: lucide/mic-vocal
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

## Create or Join
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

```gdscript title="GDScript" linenums="1" hl_lines="31 36-40"
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
```gdscript title="GDScript" linenums="1" hl_lines="31 37-41"
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

## References
- [Creating and Managing Lobbies](https://docs.discord.com/developers/discord-social-sdk/development-guides/managing-lobbies)