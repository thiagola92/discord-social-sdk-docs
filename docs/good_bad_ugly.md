---
icon: lucide/laugh
---

# The Good, The Bad, The Ugly

### The Bad
2. Some functions were renamed because their name was already being used in Godot class. I just added a `_discord` to their name.  
    - For example, the class `DiscordClient` will have these two methods:  
        - `connect()`: Godot method [`Object.connect()`](https://docs.godotengine.org/en/stable/classes/class_object.html#class-object-method-connect).  
        - `connect_discord()`: Discord method [`discordpp:Client::Connect()`](https://discord.com/developers/docs/social-sdk/classdiscordpp_1_1Client.html#a873a844c7c4c72e9e693419bb3e290aa).  
4. No signals usage.  
	- I would love to transform some of these callbacks into signals (♥️), but is not possible to identify when can it be done just by looking at functions signature. For example: `void xxxxx(Callback cb);`  
		- If the function name is `set_xxx_callback`: You know that will call you function when "xxx" happens.  
		- If the function name is `do_xxx`: You know that will do "xxx" and call you function when it's done.  
		- The first case can be transformed to signal, while the second can't... Analysing the function name and deciding what to do is a bit too much for me.  