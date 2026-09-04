---
icon: lucide/users
---

# Relationships
!!! warning
	Prerequisites:

	- [Account linking](account_linking.md)
	- [Status](status.md)

	Any code from the prerequisites can be **omitted** to make it easier to read. If you do want the complete code, look at the [repository examples](https://github.com/thiagola92/discord-social-sdk/tree/main/demo/examples).  

## Get all relationships
The best way to get all relationships is to listen for status changes and wait for the status `READY`.  

```gdscript title="GDScript" linenums="1" hl_lines="24 27-86"
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
		_on_status_ready()


func _on_status_ready() -> void:
	var online_in_game: Array[DiscordRelationshipHandle] = client.get_relationships_by_group(
		DiscordRelationshipGroupType.ONLINE_PLAYING_GAME
	)
	
	print("=== Online - In Game (%s)" % online_in_game.size())
	
	for relationship in online_in_game:
		var user = relationship.user()
		
		if user is DiscordUserHandle:
			var display_str := "🟣 %s" % user.display_name()
			
			if relationship.discord_relationship_type() == DiscordRelationshipType.FRIEND:
				display_str += " 👾"
			
			if relationship.game_relationship_type() == DiscordRelationshipType.FRIEND:
				display_str += " 🎮"
			
			print(display_str)
	
	var online_elsewhere: Array[DiscordRelationshipHandle] = client.get_relationships_by_group(
		DiscordRelationshipGroupType.ONLINE_ELSEWHERE
	)
	
	print("=== Online - Elsewhere (%s)" % online_elsewhere.size())
	
	for relationship in online_elsewhere:
		var user = relationship.user()
		
		if user is DiscordUserHandle:
			var display_str := "🟢 %s" % user.display_name()
			
			if relationship.discord_relationship_type() == DiscordRelationshipType.FRIEND:
				display_str += " 👾"
			
			if relationship.game_relationship_type() == DiscordRelationshipType.FRIEND:
				display_str += " 🎮"
			
			print(display_str)
	
	var offline: Array[DiscordRelationshipHandle] = client.get_relationships_by_group(
		DiscordRelationshipGroupType.OFFLINE
	)
	
	print("=== Online - Elsewhere (%s)" % offline.size())
	
	for relationship in offline:
		var user = relationship.user()
		
		if user is DiscordUserHandle:
			var display_str := "⚫ %s" % user.display_name()
			
			if relationship.discord_relationship_type() == DiscordRelationshipType.FRIEND:
				display_str += " 👾"
			
			if relationship.game_relationship_type() == DiscordRelationshipType.FRIEND:
				display_str += " 🎮"
			
			print(display_str)
```

## Get relationship changes
If you already got all the relationships once, there is no reason to get everything again so it's better just to listen for changes.  

```gdscript title="GDScript" linenums="1" hl_lines="11 18-38"
extends Node


var application_id: int = 123456789012345678

var client := DiscordClient.new()


func _ready() -> void:
	client.set_application_id(application_id)
	client.set_relationship_groups_updated_callback(_on_relationship_updated)


func _process(_delta: float) -> void:
	Discord.run_callbacks()


func _on_relationship_updated(user_id: int) -> void:
	var user = client.get_user(user_id)
	
	if user is DiscordUserHandle:
		var display_str := "User %s status changed to" % user.display_name()
		var relationship: DiscordRelationshipHandle = user.relationship()
		
		if user.status() == DiscordStatusType.OFFLINE:
			display_str += " ⚫"
		elif user.game_activity() == null:
			display_str += " 🟢"
		elif user.game_activity() != null:
			display_str += " 🟣"
		
		if relationship.discord_relationship_type() == DiscordRelationshipType.FRIEND:
			display_str += " 👾"
		
		if relationship.game_relationship_type() == DiscordRelationshipType.FRIEND:
			display_str += " 🎮"
		
		print(display_str)
```

## References
- [Creating a Unified Friends List](https://docs.discord.com/developers/discord-social-sdk/development-guides/creating-a-unified-friends-list)