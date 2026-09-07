---
icon: lucide/mic-vocal
---

# Voice
!!! warning
	Prerequisites:

	- [Lobbies](lobbies.md)

	Any code from the prerequisites can be **omitted** to make it easier to read. If you do want the complete code, look at the [repository examples](https://github.com/thiagola92/discord-social-sdk/tree/main/demo/examples).  

## Create/Join
```gdscript title="GDScript" linenums="1" hl_lines="31-36"
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
		print("🎮 Successfully joined lobby!")
		
		var call: DiscordCall = client.start_call(lobby_id)
		
		if call:
			print("🎤 Voice call operation initiated...")
		else:
			print("ℹ️ Already in this voice channel")
	else:
		print("❌ Failed to join lobby: %s" % result.error())
```

!!! note
	Once you join, you are officially in a voice chat!  

	Unless you mute, your microphone input will be captured and sent to others.  

## Controls
We can manipulate the voice settings for a specific call or for all calls.  

### Specific Call
```gdscript title="GDScript" linenums="1" hl_lines="6 40-41 46-53"
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
		client.create_or_join_lobby("your-unique-lobby-secret", _on_joined_lobby)


func _on_joined_lobby(result: DiscordClientResult, lobby_id: int) -> void:
	if result.successful():
		print("🎮 Successfully joined lobby!")
		
		var call: DiscordCall = client.start_call(lobby_id)
		
		if call:
			print("🎤 Voice call operation initiated...")
		else:
			print("ℹ️ Already in this voice channel")
		
		# Let's give a second to simulate interacting from anywhere in your code.
		get_tree().create_timer(1).timeout.connect(_on_call_started.bind(lobby_id))
	else:
		print("❌ Failed to join lobby: %s" % result.error())


func _on_call_started(lobby_id: int) -> void:
	var call: DiscordCall = client.get_call(lobby_id)
	
	if call:
		call.set_self_mute(true)
		call.set_self_deaf(false)
		call.set_participant_volume(target_id, 150.0)
		call.set_vad_threshold(false, -30.0)
```

### All Calls
```gdscript title="GDScript" linenums="1" hl_lines="55-70"
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
		client.create_or_join_lobby("your-unique-lobby-secret", _on_joined_lobby)


func _on_joined_lobby(result: DiscordClientResult, lobby_id: int) -> void:
	if result.successful():
		print("🎮 Successfully joined lobby!")
		
		var call: DiscordCall = client.start_call(lobby_id)
		
		if call:
			print("🎤 Voice call operation initiated...")
		else:
			print("ℹ️ Already in this voice channel")
		
		# Let's give a second to simulate interacting from anywhere in your code.
		get_tree().create_timer(1).timeout.connect(_on_call_started.bind(lobby_id))
	else:
		print("❌ Failed to join lobby: %s" % result.error())


func _on_call_started(lobby_id: int) -> void:
	var call: DiscordCall = client.get_call(lobby_id)
	
	if call:
		call.set_self_mute(true)
		call.set_self_deaf(false)
		call.set_participant_volume(target_id, 150.0)
		call.set_vad_threshold(false, -30.0)
	
	client.set_self_mute_all(true)
	client.set_input_volume(75.0)
	client.set_output_volume(120.0)
	client.set_no_audio_input_threshold(-60.0)
	client.set_no_audio_input_callback(_on_audio_crossing_threshold)
	client.set_noise_suppression(true)
	client.set_echo_cancellation(true)
	client.set_automatic_gain_control(true)
	client.set_noise_cancellation(true)


func _on_audio_crossing_threshold(input_detected: bool) -> void:
	if not input_detected:
		print("🔈 Mic appears to be silent — check your device settings.")
	else:
		print("🔊 Mic is receiving audio again")
```

## Audio Processing
!!! warning
	While the SDK provide ways to manipulate audio when received, the GDExtension still doesn't support it.  

	Changing the values of the variables has no effect in the audio.  

```gdscript title="GDScript" linenums="1" hl_lines="33 43-55"
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
		client.create_or_join_lobby("your-unique-lobby-secret", _on_joined_lobby)


func _on_joined_lobby(result: DiscordClientResult, lobby_id: int) -> void:
	if result.successful():
		print("🎮 Successfully joined lobby!")
		
		var call: DiscordCall = client.start_call_with_audio_callbacks(lobby_id, _on_audio_received, _on_audio_captured)
		
		if call:
			print("🎤 Voice call operation initiated...")
		else:
			print("ℹ️ Already in this voice channel")
	else:
		print("❌ Failed to join lobby: %s" % result.error())


func _on_audio_received(user_id: int, data: Array[int], samples_per_channel: int, sample_rate: int, channels: int, out_should_mute: bool) -> void:
	# Changing "data" doesn't reflect into SDK.
	for i in data.size():
		data[i] *= 0.5
	
	# Changing "out_should_mute" doesn't reflect into SDK.
	out_should_mute = true
	
	var total_num_samples = samples_per_channel * channels


func _on_audio_captured(data: Array[int], samples_per_channel: int, sample_rate: int, channels: int) -> void:
	pass
```

## Leave

### Specific Call
```gdscript title="GDScript" linenums="1" hl_lines="65 75-76"
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
		client.create_or_join_lobby("your-unique-lobby-secret", _on_joined_lobby)


func _on_joined_lobby(result: DiscordClientResult, lobby_id: int) -> void:
	if result.successful():
		print("🎮 Successfully joined lobby!")
		
		var call: DiscordCall = client.start_call(lobby_id)
		
		if call:
			print("🎤 Voice call operation initiated...")
		else:
			print("ℹ️ Already in this voice channel")
		
		# Let's give a second to simulate interacting from anywhere in your code.
		get_tree().create_timer(1).timeout.connect(_on_call_started.bind(lobby_id))
	else:
		print("❌ Failed to join lobby: %s" % result.error())


func _on_call_started(lobby_id: int) -> void:
	var call: DiscordCall = client.get_call(lobby_id)
	
	if call:
		call.set_self_mute(true)
		call.set_self_deaf(false)
		call.set_participant_volume(target_id, 150.0)
		call.set_vad_threshold(false, -30.0)
	
	client.set_self_mute_all(true)
	client.set_input_volume(75.0)
	client.set_output_volume(120.0)
	client.set_no_audio_input_threshold(-60.0)
	client.set_no_audio_input_callback(_on_audio_crossing_threshold)
	client.set_noise_suppression(true)
	client.set_echo_cancellation(true)
	client.set_automatic_gain_control(true)
	client.set_noise_cancellation(true)
	
	client.end_call(lobby_id, _on_call_ended)


func _on_audio_crossing_threshold(input_detected: bool) -> void:
	if not input_detected:
		print("🔈 Mic appears to be silent — check your device settings.")
	else:
		print("🔊 Mic is receiving audio again")


func _on_call_ended() -> void:
	print("🔇 Call ended successfully")
```

### All Calls
```gdscript title="GDScript" linenums="1" hl_lines="65 75-76"
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
		client.create_or_join_lobby("your-unique-lobby-secret", _on_joined_lobby)


func _on_joined_lobby(result: DiscordClientResult, lobby_id: int) -> void:
	if result.successful():
		print("🎮 Successfully joined lobby!")
		
		var call: DiscordCall = client.start_call(lobby_id)
		
		if call:
			print("🎤 Voice call operation initiated...")
		else:
			print("ℹ️ Already in this voice channel")
		
		# Let's give a second to simulate interacting from anywhere in your code.
		get_tree().create_timer(1).timeout.connect(_on_call_started.bind(lobby_id))
	else:
		print("❌ Failed to join lobby: %s" % result.error())


func _on_call_started(lobby_id: int) -> void:
	var call: DiscordCall = client.get_call(lobby_id)
	
	if call:
		call.set_self_mute(true)
		call.set_self_deaf(false)
		call.set_participant_volume(target_id, 150.0)
		call.set_vad_threshold(false, -30.0)
	
	client.set_self_mute_all(true)
	client.set_input_volume(75.0)
	client.set_output_volume(120.0)
	client.set_no_audio_input_threshold(-60.0)
	client.set_no_audio_input_callback(_on_audio_crossing_threshold)
	client.set_noise_suppression(true)
	client.set_echo_cancellation(true)
	client.set_automatic_gain_control(true)
	client.set_noise_cancellation(true)
	
	client.end_calls(_on_calls_ended)


func _on_audio_crossing_threshold(input_detected: bool) -> void:
	if not input_detected:
		print("🔈 Mic appears to be silent — check your device settings.")
	else:
		print("🔊 Mic is receiving audio again")


func _on_calls_ended() -> void:
	print("🔇 All calls ended successfully")
```

## Check

### Call
```gdscript title="GDScript" linenums="1" hl_lines="31-41"
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
		print("🎮 Successfully joined lobby!")
		
		var lobby = client.get_lobby_handle(lobby_id)
		
		if lobby is DiscordLobbyHandle:
			var call_info = lobby.get_call_info_handle()
			
			if call_info is DiscordCallInfoHandle:
				var participants: Array[int] = call_info.get_participants()
				
				print("Active call with %s participants" % participants.size())
			else:
				print("No active voice call in this lobby")
	else:
		print("❌ Failed to join lobby: %s" % result.error())
```

### Participant
```gdscript title="GDScript" linenums="1" hl_lines="31-50"
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
		print("🎮 Successfully joined lobby!")
		
		var lobby = client.get_lobby_handle(lobby_id)
		
		if lobby is DiscordLobbyHandle:
			var call_info = lobby.get_call_info_handle()
			
			if call_info is DiscordCallInfoHandle:
				var participants = call_info.get_participants()
				
				for participant_id in participants:
					var voice_state = call_info.get_voice_state_handle(participant_id)
					
					if voice_state is DiscordVoiceStateHandle:
						var is_muted = voice_state.self_mute()
						var is_deafened = voice_state.self_deaf()
						
						print("Participant %s - Muted: %s, Deafened: %s" % [
							participant_id,
							"Yes" if is_muted else "No",
							"Yes" if is_deafened else "No",
						])
	else:
		print("❌ Failed to join lobby: %s" % result.error())
```

## Import Settings
Instead of choosing a default voice settings or making the user adjust, we can import the user voice settings from the Discord client.  

### Fetch

### Changes Notification

## References
- [Managing Voice Chat](https://docs.discord.com/developers/discord-social-sdk/development-guides/managing-voice-chat)
