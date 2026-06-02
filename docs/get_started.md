---
icon: lucide/rocket
---

# Get Started
!!! warning

    Before starting, you need to follow 3 steps from [*Getting Started with C++*](https://discord.com/developers/docs/discord-social-sdk/getting-started/using-c++):  

    - Step 1: Create a Discord Developer Team.  
    - Step 2: Create a Discord Application.  
    - Step 3: Enable Discord Social SDK for Your App.  

    That's it, you don't need any other step to start using the GDExtension in GDScript.  

## Usage
This GDExtension is a wrapper around the C++ SDK, which means that each GDScript method it's just calling the C++ counterpart.  

All that you need to do is read the C++ documentation and convert the code to GDScript. For example, I was able to convert their conclusion code from [*Getting Started with C++*](https://discord.com/developers/docs/discord-social-sdk/getting-started/using-c++) to:  

```gdscript
extends Control

# Replace with your Discord Application ID
var APPLICATION_ID: int = 1349146942634065960

var client := DiscordClient.new()
var args := DiscordAuthorizationArgs.new()
var code_verifier: DiscordAuthorizationCodeVerifier = null


func _ready() -> void:
    print("🚀 Initializing Discord SDK...")
    
    client.add_log_callback(_on_log_message, DiscordLoggingSeverity.INFO)
    client.set_status_changed_callback(_on_status_changed)
    
    code_verifier = client.create_authorization_code_verifier()
    
    args.set_client_id(APPLICATION_ID)
    args.set_scopes(DiscordClient.get_default_presence_scopes())
    args.set_code_challenge(code_verifier.challenge())
    client.authorize(args, _on_authorized)


func _process(_delta: float) -> void:
    Discord.run_callbacks()


func _on_log_message(message: String, severity: DiscordLoggingSeverity.Enum) -> void:
    print("[%s] %s" % [Discord.enum_to_string(severity, DiscordLoggingSeverity.id), message])


func _on_status_changed(status: DiscordClientStatus.Enum, error: DiscordClientError.Enum, error_detail: int) -> void:
    print("🔄 Status changed: %s" % status)
    
    if status == DiscordClientStatus.READY:
        print("✅ Client is ready! You can now call SDK functions.")
        print("👥 Friends Count: %s" % client.get_relationships().size())
        
        var activity := DiscordActivity.new()
        activity.set_type(DiscordActivityTypes.PLAYING)
        activity.set_state("In Competitive Match")
        activity.set_details("Rank: Diamond II")
        
        client.update_rich_presence(activity, _on_rich_presence_updated)
    elif error != DiscordClientError.NONE:
        print("❌ Connection Error: %s - Details: %s" % [error, error_detail])


func _on_rich_presence_updated(result: DiscordClientResult) -> void:
    if result.successful():
        print("🎮 Rich Presence updated successfully!")
    else:
        print("❌ Rich Presence update failed")


func _on_authorized(result: DiscordClientResult, code: String, redirect_uri: String) -> void:
    if not result.successful():
        print("❌ Authentication Error: %s" % result.error())
    else:
        print("✅ Authorization successful! Getting access token...")
        
        client.get_token(APPLICATION_ID, code, code_verifier.verifier(), redirect_uri, _on_token_received)


func _on_token_received(
        _result: DiscordClientResult,
        access_token: String,
        _refresh_token: String,
        _token_type: DiscordAuthorizationTokenType.Enum,
        _expires_in: int,
        _scopes: String
) -> void:
    print("🔓 Access token received! Establishing connection...")
    
    client.update_token(DiscordAuthorizationTokenType.BEARER, access_token, _on_token_updated)


func _on_token_updated(result: DiscordClientResult) -> void:
    if result.successful():
        print("🔑 Token updated, connecting to Discord...")
        
        client.connect_discord()
```

## `C++` to `GDScript`
At first it may sound confuse when I tell you to "**convert**" C++ to GDSCript, but get easier once you see examples.  

!!! tip

    **`F1` is your best friend!**

    You can probably find the class that you are looking for by searching for part of it name.  

    ---

    I extracted their official documentation to classes/methods using [Doxygen](https://www.doxygen.nl/), so you probably don't need to check [Discord Social SDK Reference](https://discord.com/developers/docs/social-sdk/index.html) to know how they work.  

    --- 

    It's good to know that we attempt to follow [GDScript style guide](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_styleguide.html), so it probably help guessing the names of methods/properties/enums/methods/params.  

### Namespace Function
```c++ title="C++"
discordpp::RunCallbacks()
```

```gdscript title="GDScript"
Discord.run_callbacks()
```

1. Transform function call
    - `::RunCallbacks()` => `.run_callbacks()`
2. Transform namespace
    - `discordpp` => `Discord`

### Class
```c++ title="C++"
discordpp::Client
```

```gdscript title="GDScript"
DiscordClient
```

1. Transform class
    - `::Client` => `Client`
2. Transform namespace
    - `discordpp` => `Discord`

### Method
```c++ title="C++"
client->AddLogCallback()
```

```gdscript title="GDScript"
client.add_log_callback()
```

1. Transform method call
    - `->AddLogCallback()` => `.add_log_callback()`

```c++ title="C++"
args.SetClientId()
```

```gdscript title="GDScript"
args.set_client_id()
```

1. Transform method call
    - `.SetClientId()` => `.set_client_id()`

### Enum Type
```c++ title="C++"
discordpp::Client::Status
```

```gdscript title="GDScript"
DiscordClientStatus.Enum
```

1. Transform enum type
    - `::Status` => `Status.Enum`
2. Transform class
    - `::Client` => `Client`
3. Transform namespace
    - `discordpp` => `Discord`

??? note "Why not `DiscordClient.Status`?"

    Each enum has it own class, this happened because the **Godot C++** doesn't let me use the same name in different enums.  

    Let's look at [`discordpp::RelationshipType`](https://discord.com/developers/docs/social-sdk/namespacediscordpp.html#a28fc5199b9211c24124c06f30c1d0cbb) and [`discordpp::HttpStatusCode`](https://discord.com/developers/docs/social-sdk/namespacediscordpp.html#a12b04d48d8ea98ec007270a10e0c88ba), they have an enum called `None`. So I could represent they in GDScript like:  

    ```gdscript title="GDScript"
    class_name Discord
    # ...

    enum RelationshipType {
        NONE,
        # ...
    }

    enum HttpStatusCode {
        NONE,
        # ...
    }
    ```

    This doesn't work in Godot C++ because every time that you register a constant inside an enum, you also register it in the class.  

    ```c++ title="C++"
    void Discord::_bind_methods() {
        // Define Discord.RelationshipType.NONE
        // Define Discord.NONE
        ClassDB::bind_integer_constant(get_class_static(), "RelationshipType", "NONE", 0);

        // Error because Discord.NONE is already defined.
        ClassDB::bind_integer_constant(get_class_static(), "HttpStatusCode", "NONE", 0);
    }
    ```

    The solution that I came was to create a class for each enum, which would be something like this in GDScript:  

    ```gdscript title="GDScript"
    class_name DiscordRelationshipType
    # ...

    enum Enum {
        NONE,
        FRIEND,
        BLOCKED,
        # ...
    }
    ```

    As I said before, Godot C++ always register constants in the class, so you can know get the enum value through both:

    ```gdscript title="GDScript"
    DiscordRelationshipType.Enum.NONE
    DiscordRelationshipType.NONE
    ```

    Reference: [godot-cpp/issues/1910](https://github.com/godotengine/godot-cpp/issues/1910)  


### Enum Value
```c++ title="C++"
discordpp::Client::Status::Ready
```

```gdscript title="GDScript"
DiscordClientStatus.READY
```

1. Transform enum value
    - `::Ready` => `.READY`
2. Transform enum name
    - `::Status` => `Status`
3. Transform class
    - `::Client` => `Client`
4. Transform namespace
    - `discordpp` => `Discord`

### Unsigned 64-bit Integer
```c++ title="C++"
uint64_t
```

```gdscript title="GDScript"
int
```

1. Transform unsigned 64-bit integer
    - `uint64_t` => `int`

??? danger "Don't operate over it"

    Godot only works with `int64`, so we always convert `uint64` to `int64` when receiving from SDK and the opposite when sending to SDK.  

    It's not a problem if you **don't** intend to change the data, because converting between them is just copying the bytes without changing them.  

    **But** if you intend to change the data, you should study the operations that can corrupt it.  

    Reference: [godot-proposals/issues/9740](https://github.com/godotengine/godot-proposals/issues/9740#issuecomment-2484959346)

### Optional
```c++ title="C++"
std::optional<T>
```

```gdscript title="GDScript"
Variant
```

1. Transform optional
    - `std::optional<T>` => `Variant`

??? warning "Rework needed"

    As counterpart of C++ [`std::optional<T>`](https://en.cppreference.com/w/cpp/utility/optional), we use GDScript [`Variant`](https://docs.godotengine.org/en/stable/classes/class_variant.html). The idea was:  

    | Type               | Possible Values       |
    | ------------------ | --------------------- |
    | `std::optional<T>` | `T` or `std::nullopt` |
    | `Variant`          | `T` or `null`         |

    This design has a problem... What if `T` is actually a pointer? For example:  

    ```c++ title="C++"
    std::optional<int*> f(int mode) {
        if (mode == 0)
            return std::nullopt;

        if (mode == 1)
            return nullptr;

        static int x = 42;
        return &x;
    }
    ```

    Now we can't see he difference between "the value is null" and "there is no value".  

    | Return             | Converted to          |
    | ------------------ | --------------------- |
    | `std::nullopt`     | `null`                |
    | `nullptr`          | `null`                |
    | `T`                | `T`                   |

    
    To solve this I would need to create another class, to actually represent `std::optional<T>`.  

### Lambda Function

### Callback

### Others

|                  | C++                        | GDScript           |
| ---------------- | -------------------------- | ------------------ |
| Parameter Name   | `apiBase`                  | `api_base`         |
| Bool             | `bool`                     | `bool`             |
| Integer          | `int32_t`                  | `int`              |
| Float            | `float`                    | `float`            |
| String           | `std::string`              | `String`           |
| Vector           | `std::vector<T>`           | `Array[T]`         |
| Map              | `std::unordered_map<K, T>` | `Dictionary[K, T]` |

### Signals
You probably already noticed, but their SDK makes **heavy** use of callbacks and we just replicate their behaviour in this GDExtension.  