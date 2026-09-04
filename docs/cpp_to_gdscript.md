---
icon: lucide/arrow-right-left
---

# `C++` to `GDScript`

## Namespace
|                  | C++                          | GDScript                                     |
| ---------------- | ---------------------------- | -------------------------------------------- |
| Functions        | `discordpp::MethodExample()` | `Discord.method_example()`                   |
| Classes          | `discordpp::ClassExample`    | `DiscordClassExample`                        |

??? note "GDScript doesn't have namespace concept"

    To avoid any naming conflict with others classes, my solution was to:

    - Put the static functions into `Discord` class
    - Create a class with "Discord" prefix for each class

## Names
|                  | C++                        | GDScript                                     |
| ---------------- | -------------------------- | -------------------------------------------- |
| Class Name       | `ClassExample`             | `DiscordClassExample`                        |
| Method Name      | `MethodExample`            | `method_example` <br> `method_example_discord` |
| Parameter Name   | `paramExample`             | `param_example`                              |

??? warning "Conflict with existing names"

    There is cases where the method name is already being used by Godot [Object](https://docs.godotengine.org/en/stable/classes/class_object.html#class-object-method-connect):  

	```c++ title="C++"
	discordpp::Client::Connect()
	discordpp::Client::Disconnect()
	```

    In this cases, my solution was to add the suffix `_discord()` to their name:  

	```c++ title="C++"
	DiscordClient.connect_discord()
	DiscordClient.disconnect_discord()
	```

## Basic Types
|                  | C++                         | GDScript                                     |
| ---------------- | --------------------------- | -------------------------------------------- |
| Bool             | `bool`                      | `bool`                                       |
| Integer          | `int8_t`<br>`uint8_t`<br>`int16_t`<br>`uint16_t`<br>`int32_t`<br>`uint32_t`<br>`int64_t`<br>`uint64_t` | `int` |
| Float            | `float`                     | `float`                                      |
| String           | `std::string`               | `String`                                     |

??? danger "Operating over integers is dangerous"

    Godot only works with **signed 64-bit integer**, so we always convert integers to `int64_t` when receiving from SDK. But when sending back to the SDK we have to convert it to the original type again, this can cause problems **if** you operated over the integer.  
    
    For example, these both have the same bits:  

    | `int64_t` | `uint64_t`           |
    | --------- | -------------------- |
    | -1        | 18446744073709551615 |

    Adding 1 to them would reflect in different values:    

    | `int64_t` | `uint64_t`           |
    | --------- | -------------------- |
    | 0         | 18446744073709551616 |

    When sending back to the SDK it would receive 0 instead of the expected value.  

    **Note**: It's not a problem if you didn't operate over the value because it will be just copying the bits without changing them (reference: [godot-proposals/issues/9740](https://github.com/godotengine/godot-proposals/issues/9740#issuecomment-2484959346)).  

    ??? tip "Tip"

        Converting a bigger type to a smaller type means truncating until match the smallest size. For example:  

        |         | `uint16_t`          | `uint8_t` |
        | ------- | ------------------- | --------- |
        | Decimal | 32896               | 128       |
        | Binary  | 1000 0000 1000 0000 | 1000 0000 |

        In this case, we lost information during conversion because a number bigger than `uint8_t` limit was converted to a number smaller than it limit.  

        You can reduce your problem by clamping the value so at least we know if reached minimum or maximum values. For example:  
        
        ```gdscript title="GDScript"
        # For unsigned types.
        value = clampi(value, 0, UINT8_MAX)
        value = clampi(value, 0, UINT16_MAX)
        value = clampi(value, 0, UINT32_MAX)

        # For signed types.
        value = clampi(value, INT8_MIN, INT8_MAX)
        value = clampi(value, INT16_MIN, INT16_MAX)
        value = clampi(value, INT32_MIN, INT32_MAX)
        ```

        **Note**: This doesn't prevent you from corrupting data when operating over it.  

## Complex Types
|                  | C++                         | GDScript                                     |
| ---------------- | --------------------------- | -------------------------------------------- |
| Vector           | `std::vector<T>`            | `Array[T]`                                   |
| Map              | `std::unordered_map<K, T>`  | `Dictionary[K, T]`                           |
| Optional         | `std::optional<T>`          | `Variant`                                    |

??? warning "`Variant` problem"

    As counterpart of C++ [`std::optional<T>`](https://en.cppreference.com/w/cpp/utility/optional), we used GDScript [`Variant`](https://docs.godotengine.org/en/stable/classes/class_variant.html). The idea was:  

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

    Now we can't see the difference between "the value is null" and "there is no value".  

    | Return             | Converted to          |
    | ------------------ | --------------------- |
    | `std::nullopt`     | `null`                |
    | `nullptr`          | `null`                |
    | `T`                | `T`                   |

    
    To solve this I would need to create a class to represent `std::optional<T>`.  

## Enum
|                  | C++                                | GDScript                                     |
| ---------------- | ---------------------------------- | -------------------------------------------- |
| Enum Type        | `discordpp::Class::Example`        | `DiscordClassExample.Enum`                   |
| Enum Value       | `discordpp::Class::Example::Value` | `DiscordClassExample.VALUE`                  |

??? note "Why not `DiscordClass.Example`?"

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

## Method Call
|                  | C++                        | GDScript                                     |
| ---------------- | -------------------------- | -------------------------------------------- |
| Default          | `variable.Example()` or <br> `variable->Example()` | `variable.example()`                      |

??? warning "Function overloading"

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

## Lambda Function
```c++ title="C++"
[](auto message, auto severity) {
  //
}
```

```gdscript title="GDScript"
func(message, severity):
    pass
```

---

```c++ title="C++"
[](std::string message, discordpp::LoggingSeverity severity) {
  //
}
```

```gdscript title="GDScript"
func(message: String, severity: DiscordLoggingSeverity.Enum):
    pass
```

---

```c++ title="C++"
[client](auto message, auto severity) {
  //
}
```

```gdscript title="GDScript"
(func(message, severity, client):
    pass
).bind(client)
```
