---
icon: lucide/square-terminal
---

# Examples
The minimum that I recommend you to read is:  

- [Core](core.md)
- [Logging](logging.md)

## Code Style
I'm always following [GDScript style guide](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_styleguide.html) but you may find my examples a little weird when comparing to C++ documentation. For example, let's take the following callback chain:  

```
main()
└── client->Authorize()
    └── client->GetToken()
        └── client->UpdateToken()
```

Their example chain the calls in a way that I don't like to read:  

```c++ title="C++"
int main() {
    client->Authorize(args, [client, codeVerifier](auto result, auto code, auto redirectUri) {
        client->GetToken(APPLICATION_ID, code, codeVerifier.Verifier(), redirectUri,
            [client](discordpp::ClientResult result,
            std::string accessToken,
            std::string refreshToken,
            discordpp::AuthorizationTokenType tokenType,
            int32_t expiresIn,
            std::string scope) {
            client->UpdateToken(discordpp::AuthorizationTokenType::Bearer,  accessToken, [client](discordpp::ClientResult result) {
                // Last call.
            });
        });
        }
    });
}
```

So I prefer to create the functions instead:  

```gdscript title="GDScript"
var client

var code_verifier


func _ready():
    client.authorize(args, _on_authorized)


func _on_authorized(result: DiscordClientResult, code: String, redirect_uri: String) -> void:
    client.get_token(
        APPLICATION_ID,
        code,
        code_verifier.verifier(),
        redirect_uri,
        _on_token_received
    )


func _on_token_received(
		result: DiscordClientResult,
		access_token: String,
		refresh_token: String,
		token_type: DiscordAuthorizationTokenType.Enum,
		expires_in: int,
		scopes: String
) -> void:
    client.update_token(DiscordAuthorizationTokenType.BEARER, access_token, _on_token_updated)


func _on_token_updated(result: DiscordClientResult) -> void:
    pass # Last call.
```