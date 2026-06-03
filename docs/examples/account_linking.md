---
icon: lucide/link
---

# Account Linking
We need to pass some extra information to be authorizated in Discord, so we use `DiscordAuthorizationArgs` to hold everything.  

```gdscript title="GDScript" linenums="1" hl_lines="8 12 13 15 16 17 20"
extends Node


var application_id: int = 123456789012345678

var client := DiscordClient.new()

var code_verifier: DiscordAuthorizationCodeVerifier


func _ready() -> void:
	var args := DiscordAuthorizationArgs.new()
	code_verifier = client.create_authorization_code_verifier()
	
	args.set_client_id(application_id)
	args.set_scopes(DiscordClient.get_default_presence_scopes())
	args.set_code_challenge(code_verifier.challenge())
	
	client.add_log_callback(_on_log, DiscordLoggingSeverity.INFO)
	client.authorize(args, _on_authorization_response)


func _process(_delta: float) -> void:
	Discord.run_callbacks()


func _on_log(message: String, severity: DiscordLoggingSeverity.Enum) -> void:
	var enum_str: String = Discord.enum_to_string(severity, DiscordLoggingSeverity.id)
	
	print("[%s] %s" % [enum_str, message])
```

Did you noticed that I didn't use `client.set_application_id()`? It was unnecessary because we used `args.set_client_id()`, later Discord use this value to set the application id for us.  

??? question "`client.set_application_id()` vs `args.set_client_id()`"

    If you don't intend to call `client.connect_discord()` but still wants to use others features from the SDK, then `client.set_application_id()` is very useful.  

    What's the advantages of `args.set_client_id()`? I don't know...


```gdscript title="GDScript"
func _on_authorization_response(result: DiscordClientResult, code: String, redirect_uri: String) -> void:
	if not result.successful():
		print("❌ Authorization Error: %s" % result.error())
		return
	
	print("✅ Authorization successful! Next step: exchange code for an access token")
	client.get_token(application_id, code, code_verifier.verifier(), redirect_uri, _on_token_received)


func _on_token_received(
	result: DiscordClientResult,
	access_token: String,
	_refresh_token: String,
	token_type: DiscordAuthorizationTokenType.Enum,
	_expires_in: int,
	_scopes: String
) -> void:
	if not result.successful():
		print("❌ Token Error: %s" % result.error())
		return
	
	print("🔓 Access token received! Establishing connection...")
	client.update_token(token_type, access_token, _on_token_updated)


func _on_token_updated(result: DiscordClientResult) -> void:
	if not result.successful():
		print("❌ Token Update Error: %s" % result.error())
		return
	
	print("🔑 Token updated, connecting to Discord...")
	client.connect_discord()
```

## References