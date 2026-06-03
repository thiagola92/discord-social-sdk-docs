---
icon: lucide/scroll
---

# Logging
One of the first things you should do is add a log callback, otherwise you will never know when something goes wrong with Discord.  

```gdscript title="GDScript"
func _ready() -> void:
	client.add_log_callback(_on_log, DiscordLoggingSeverity.INFO)


func _on_log(message: String, severity: DiscordLoggingSeverity.Enum) -> void:
	var enum_str: String = Discord.enum_to_string(severity, DiscordLoggingSeverity.id)

	print("[%s] %s" % [enum_str, message])
```

??? warning "`discordpp::EnumToString()` != `Discord.enum_to_string()`"

    In case you didn't know, there is more than 20 functions `discordpp::EnumToString()` in the C++ code. This can exist because C++ support function overloading, so during the compilation is able to look at yours parameters type and link to the correct function.  

    ```c++ title="C++"
    discordpp::EnumToString(discordpp::ActivityActionTypes value)
    discordpp::EnumToString(discordpp::ActivityGamePlatforms value)
    discordpp::EnumToString(discordpp::ActivityPartyPrivacy value)
    discordpp::EnumToString(discordpp::ActivityTypes value)
    ```

    GDScript doesn't have function overloading because it is a runtime language, so making it discover the correct function during execution would drop performance.  

    My [gambiarra](https://pt.wikipedia.org/wiki/Gambiarra) to solve the problem was to add an extra parameter that identifies the type of the first parameter.  

    ```gdscript title="GDScript"
    Discord.enum_to_string(value: int, enum_id: int)
    ```

    Every single enum has this identifier:  

    ```gdscript title="GDScript"
    DiscordActivityActionTypes.id
    DiscordActivityGamePlatforms.id
    DiscordActivityPartyPrivacy.id
    DiscordActivityTypes.id
    ```

    Note how `id` is not UPPER_CASE, this prevents conflicting with true constants.  

## References
- [client.add_log_callback()](https://discord.com/developers/docs/social-sdk/classdiscordpp_1_1Client.html#af78996cff24a40f5dc7066beed16692c)
- [Discord.enum_to_string()](https://discord.com/developers/docs/social-sdk/namespacediscordpp.html#a0fd967a23d2d106ced3d6669b9a810ad)