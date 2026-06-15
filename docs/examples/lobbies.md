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

??? note "Lobby vs Channel"
    - Lobby is a **temporary** group to be used for communication with people that you just matched  
        - Represents a matchmaking lobby
    - Channel is a **permanent** group to be used for communication with people that you may know  
        - Represents a place to hangout with your friends/community

## References
- [Creating and Managing Lobbies](https://docs.discord.com/developers/discord-social-sdk/development-guides/managing-lobbies)