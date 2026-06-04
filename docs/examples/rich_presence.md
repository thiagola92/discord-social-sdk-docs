---
icon: lucide/sparkles
---

# Rich Presence
!!! warning "Rich Presence without [Account linking](account_linking.md)"

    You will not need account linking to use Rich Presence if the SDK find Discord running in your desktop.  

    ---

    **Note for Linux developers:** SDK doesn't seem to find Discord running when using Flatpak/Snap.  

## Details and State
```gdscript title="GDScript" linenums="1" hl_lines="12-15 17 24-26"
extends Node


var application_id: int = 123456789012345678

var client := DiscordClient.new()


func _ready() -> void:
	client.set_application_id(application_id)
	
	var activity := DiscordActivity.new()
	activity.set_type(DiscordActivityTypes.PLAYING)
	activity.set_details("Learning to Use")
	activity.set_state("In Godot")

	client.update_rich_presence(activity, _on_rich_presence_updated)


func _process(_delta: float) -> void:
	Discord.run_callbacks()


func _on_rich_presence_updated(result: DiscordClientResult) -> void:
	if result.successful():
		print("✅ Rich presence updated!")
```

## Timestamps
```gdscript title="GDScript" linenums="1" hl_lines="17-19"
extends Node


var application_id: int = 123456789012345678

var client := DiscordClient.new()


func _ready() -> void:
	client.set_application_id(application_id)
	
	var activity := DiscordActivity.new()
	activity.set_type(DiscordActivityTypes.PLAYING)
	activity.set_details("Learning to Use")
	activity.set_state("In Godot")
	
	var timestamps := DiscordActivityTimestamps.new()
	timestamps.set_start(0)
	activity.set_timestamps(timestamps)

	client.update_rich_presence(activity, _on_rich_presence_updated)


func _process(_delta: float) -> void:
	Discord.run_callbacks()


func _on_rich_presence_updated(result: DiscordClientResult) -> void:
	if result.successful():
		print("✅ Rich presence updated!")
```

## Assets
```gdscript title="GDScript" linenums="1" hl_lines="21-27"
extends Node


var application_id: int = 123456789012345678

var client := DiscordClient.new()


func _ready() -> void:
	client.set_application_id(application_id)
	
	var activity := DiscordActivity.new()
	activity.set_type(DiscordActivityTypes.PLAYING)
	activity.set_details("Learning to Use")
	activity.set_state("In Godot")
	
	var timestamps := DiscordActivityTimestamps.new()
	timestamps.set_start(0)
	activity.set_timestamps(timestamps)
	
	var assets := DiscordActivityAssets.new()
	assets.set_large_image("surprise")
	assets.set_large_text("Surprise")
	assets.set_small_image("happy-face")
	assets.set_small_text("Happy face")
	assets.set_invite_cover_image("thumbnail")
	activity.set_assets(assets)

	client.update_rich_presence(activity, _on_rich_presence_updated)


func _process(_delta: float) -> void:
	Discord.run_callbacks()


func _on_rich_presence_updated(result: DiscordClientResult) -> void:
	if result.successful():
		print("✅ Rich presence updated!")
```

## Field URLs
```gdscript title="GDScript" linenums="1" hl_lines="29 30"
extends Node


var application_id: int = 123456789012345678

var client := DiscordClient.new()


func _ready() -> void:
	client.set_application_id(application_id)
	
	var activity := DiscordActivity.new()
	activity.set_type(DiscordActivityTypes.PLAYING)
	activity.set_details("Learning to Use")
	activity.set_state("In Godot")
	
	var timestamps := DiscordActivityTimestamps.new()
	timestamps.set_start(0)
	activity.set_timestamps(timestamps)
	
	var assets := DiscordActivityAssets.new()
	assets.set_large_image("surprise")
	assets.set_large_text("Surprise")
	assets.set_small_image("happy-face")
	assets.set_small_text("Happy face")
	assets.set_invite_cover_image("thumbnail")
	activity.set_assets(assets)
	
	activity.set_details_url("https://github.com/thiagola92/discord-social-sdk/tree/main")
	activity.set_state_url("https://store.godotengine.org/asset/thiagola92/discord-social-sdk/")

	client.update_rich_presence(activity, _on_rich_presence_updated)


func _process(_delta: float) -> void:
	Discord.run_callbacks()


func _on_rich_presence_updated(result: DiscordClientResult) -> void:
	if result.successful():
		print("✅ Rich presence updated!")
```

## Buttons
```gdscript title="GDScript" linenums="1" hl_lines="32-35"
extends Node


var application_id: int = 123456789012345678

var client := DiscordClient.new()


func _ready() -> void:
	client.set_application_id(application_id)
	
	var activity := DiscordActivity.new()
	activity.set_type(DiscordActivityTypes.PLAYING)
	activity.set_details("Learning to Use")
	activity.set_state("In Godot")
	
	var timestamps := DiscordActivityTimestamps.new()
	timestamps.set_start(0)
	activity.set_timestamps(timestamps)
	
	var assets := DiscordActivityAssets.new()
	assets.set_large_image("surprise")
	assets.set_large_text("Surprise")
	assets.set_small_image("happy-face")
	assets.set_small_text("Happy face")
	assets.set_invite_cover_image("thumbnail")
	activity.set_assets(assets)
	
	activity.set_details_url("https://github.com/thiagola92/discord-social-sdk/tree/main")
	activity.set_state_url("https://store.godotengine.org/asset/thiagola92/discord-social-sdk/")
	
	var issue_button := DiscordActivityButton.new()
	issue_button.set_label("Report bugs")
	issue_button.set_url("https://github.com/thiagola92/discord-social-sdk/issues")
	activity.add_button(issue_button)

	client.update_rich_presence(activity, _on_rich_presence_updated)


func _process(_delta: float) -> void:
	Discord.run_callbacks()


func _on_rich_presence_updated(result: DiscordClientResult) -> void:
	if result.successful():
		print("✅ Rich presence updated!")
```

## Status Text
```gdscript title="GDScript" linenums="1" hl_lines="37"
extends Node


var application_id: int = 123456789012345678

var client := DiscordClient.new()


func _ready() -> void:
	client.set_application_id(application_id)
	
	var activity := DiscordActivity.new()
	activity.set_type(DiscordActivityTypes.PLAYING)
	activity.set_details("Learning to Use")
	activity.set_state("In Godot")
	
	var timestamps := DiscordActivityTimestamps.new()
	timestamps.set_start(0)
	activity.set_timestamps(timestamps)
	
	var assets := DiscordActivityAssets.new()
	assets.set_large_image("surprise")
	assets.set_large_text("Surprise")
	assets.set_small_image("happy-face")
	assets.set_small_text("Happy face")
	assets.set_invite_cover_image("thumbnail")
	activity.set_assets(assets)
	
	activity.set_details_url("https://github.com/thiagola92/discord-social-sdk/tree/main")
	activity.set_state_url("https://store.godotengine.org/asset/thiagola92/discord-social-sdk/")
	
	var issue_button := DiscordActivityButton.new()
	issue_button.set_label("Report bugs")
	issue_button.set_url("https://github.com/thiagola92/discord-social-sdk/issues")
	activity.add_button(issue_button)

	activity.set_status_display_type(DiscordStatusDisplayTypes.STATE)

	client.update_rich_presence(activity, _on_rich_presence_updated)


func _process(_delta: float) -> void:
	Discord.run_callbacks()


func _on_rich_presence_updated(result: DiscordClientResult) -> void:
	if result.successful():
		print("✅ Rich presence updated!")
```

## Party
```gdscript title="GDScript" linenums="1" hl_lines="39-43"
extends Node


var application_id: int = 123456789012345678

var client := DiscordClient.new()


func _ready() -> void:
	client.set_application_id(application_id)
	
	var activity := DiscordActivity.new()
	activity.set_type(DiscordActivityTypes.PLAYING)
	activity.set_details("Learning to Use")
	activity.set_state("In Godot")
	
	var timestamps := DiscordActivityTimestamps.new()
	timestamps.set_start(0)
	activity.set_timestamps(timestamps)
	
	var assets := DiscordActivityAssets.new()
	assets.set_large_image("surprise")
	assets.set_large_text("Surprise")
	assets.set_small_image("happy-face")
	assets.set_small_text("Happy face")
	assets.set_invite_cover_image("thumbnail")
	activity.set_assets(assets)
	
	activity.set_details_url("https://github.com/thiagola92/discord-social-sdk/tree/main")
	activity.set_state_url("https://store.godotengine.org/asset/thiagola92/discord-social-sdk/")
	
	var issue_button := DiscordActivityButton.new()
	issue_button.set_label("Report bugs")
	issue_button.set_url("https://github.com/thiagola92/discord-social-sdk/issues")
	activity.add_button(issue_button)

	activity.set_status_display_type(DiscordStatusDisplayTypes.STATE)
	
	var party := DiscordActivityParty.new()
	party.set_id("party1234")
	party.set_current_size(1)
	party.set_max_size(5)
	activity.set_party(party)

	client.update_rich_presence(activity, _on_rich_presence_updated)


func _process(_delta: float) -> void:
	Discord.run_callbacks()


func _on_rich_presence_updated(result: DiscordClientResult) -> void:
	if result.successful():
		print("✅ Rich presence updated!")
```

## Party Secret
```gdscript title="GDScript" linenums="1" hl_lines="45-47"
extends Node


var application_id: int = 123456789012345678

var client := DiscordClient.new()


func _ready() -> void:
	client.set_application_id(application_id)
	
	var activity := DiscordActivity.new()
	activity.set_type(DiscordActivityTypes.PLAYING)
	activity.set_details("Learning to Use")
	activity.set_state("In Godot")
	
	var timestamps := DiscordActivityTimestamps.new()
	timestamps.set_start(0)
	activity.set_timestamps(timestamps)
	
	var assets := DiscordActivityAssets.new()
	assets.set_large_image("surprise")
	assets.set_large_text("Surprise")
	assets.set_small_image("happy-face")
	assets.set_small_text("Happy face")
	assets.set_invite_cover_image("thumbnail")
	activity.set_assets(assets)
	
	activity.set_details_url("https://github.com/thiagola92/discord-social-sdk/tree/main")
	activity.set_state_url("https://store.godotengine.org/asset/thiagola92/discord-social-sdk/")
	
    # Doesn't work while using party secrets.
	#var issue_button := DiscordActivityButton.new()
	#issue_button.set_label("Report bugs")
	#issue_button.set_url("https://github.com/thiagola92/discord-social-sdk/issues")
	#activity.add_button(issue_button)

	activity.set_status_display_type(DiscordStatusDisplayTypes.STATE)
	
	var party := DiscordActivityParty.new()
	party.set_id("party1234")
	party.set_current_size(1)
	party.set_max_size(5)
	activity.set_party(party)

	var secrets := DiscordActivitySecrets.new()
	secrets.set_join("your-join-secret")
	activity.set_secrets(secrets)

	client.update_rich_presence(activity, _on_rich_presence_updated)


func _process(_delta: float) -> void:
	Discord.run_callbacks()


func _on_rich_presence_updated(result: DiscordClientResult) -> void:
	if result.successful():
		print("✅ Rich presence updated!")
```

!!! note "Buttons doesn't work while using party secrets"

## References
- [Setting Rich Presence](https://docs.discord.com/developers/discord-social-sdk/development-guides/setting-rich-presence)