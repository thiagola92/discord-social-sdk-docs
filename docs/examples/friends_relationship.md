---
icon: lucide/users
---

# Friends & Relationship
!!! warning "[Account linking](account_linking.md) is necessary."

## Get all relationships
The best way to get all relationships is to listen for status changes and wait for the status `READY`.  

```gdscript title="GDScript" linenums="1" hl_lines="11 23 24 27 28 29 30 31 32 33 34 35 36 37 38 39 40 41 42 43 44 45 46 47 48 49 50 51 52 53 54 55 56 57 58 59 60 61 62 63 64 65 66 67 68 69 70 71 72 73 74 75 76 77 78 79 80 81 82 83 84 85 86"
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

```gdscript title="GDScript" linenums="1" hl_lines="11 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 33 34 35 36 37 38"
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