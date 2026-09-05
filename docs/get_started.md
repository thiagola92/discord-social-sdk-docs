---
icon: lucide/rocket
---

# Get Started
!!! warning
	Prerequisites:

	- [Access](../access.md)
	- [Installation](../installation.md)

## Usage
This GDExtension is a wrapper around the C++ SDK, which means that each GDScript method it's just calling the C++ counterpart.  

All that you need to do is read the official C++ documentation and convert the code to GDScript. For example, I was able to convert their conclusion code from [*Getting Started with C++*](https://discord.com/developers/docs/discord-social-sdk/getting-started/using-c++) to GDScript:  

=== "C++"

    ```c++ linenums="1"
    #define DISCORDPP_IMPLEMENTATION
    #include "discordpp.h"
    #include <iostream>
    #include <thread>
    #include <atomic>
    #include <string>
    #include <functional>
    #include <csignal>

    // Replace with your Discord Application ID
    const uint64_t APPLICATION_ID = 1349146942634065960;

    // Create a flag to stop the application
    std::atomic<bool> running = true;

    // Signal handler to stop the application
    void signalHandler(int signum) {
        running.store(false);
    }

    int main() {
        std::signal(SIGINT, signalHandler);
        std::cout << "🚀 Initializing Discord SDK...\n";

        // Create our Discord Client
        auto client = std::make_shared<discordpp::Client>();

        // Set up logging callback
        client->AddLogCallback([](auto message, auto severity) {
        std::cout << "[" << EnumToString(severity) << "] " << message << std::endl;
        }, discordpp::LoggingSeverity::Info);

        // Set up status callback to monitor client connection
        client->SetStatusChangedCallback([client](discordpp::Client::Status status, discordpp::Client::Error error, int32_t errorDetail) {
        std::cout << "🔄 Status changed: " << discordpp::Client::StatusToString(status) << std::endl;

        if (status == discordpp::Client::Status::Ready) {
            std::cout << "✅ Client is ready! You can now call SDK functions.\n";

            // Access initial relationships data
            std::cout << "👥 Friends Count: " << client->GetRelationships().size() << std::endl;

            // Configure rich presence details
            discordpp::Activity activity;
            activity.SetType(discordpp::ActivityTypes::Playing);
            activity.SetState("In Competitive Match");
            activity.SetDetails("Rank: Diamond II");

            // Update rich presence
            client->UpdateRichPresence(activity, [](discordpp::ClientResult result) {
            if(result.Successful()) {
                std::cout << "🎮 Rich Presence updated successfully!\n";
            } else {
                std::cerr << "❌ Rich Presence update failed";
            }
            });

        } else if (error != discordpp::Client::Error::None) {
            std::cerr << "❌ Connection Error: " << discordpp::Client::ErrorToString(error) << " - Details: " << errorDetail << std::endl;
        }
        });

        // Generate OAuth2 code verifier for authentication
        auto codeVerifier = client->CreateAuthorizationCodeVerifier();

        // Set up authentication arguments
        discordpp::AuthorizationArgs args{};
        args.SetClientId(APPLICATION_ID);
        args.SetScopes(discordpp::Client::GetDefaultPresenceScopes());
        args.SetCodeChallenge(codeVerifier.Challenge());

        // Begin authentication process
        client->Authorize(args, [client, codeVerifier](auto result, auto code, auto redirectUri) {
        if (!result.Successful()) {
            std::cerr << "❌ Authentication Error: " << result.Error() << std::endl;
            return;
        } else {
            std::cout << "✅ Authorization successful! Getting access token...\n";

            // Exchange auth code for access token
            client->GetToken(APPLICATION_ID, code, codeVerifier.Verifier(), redirectUri,
            [client](discordpp::ClientResult result,
            std::string accessToken,
            std::string refreshToken,
            discordpp::AuthorizationTokenType tokenType,
            int32_t expiresIn,
            std::string scope) {
                std::cout << "🔓 Access token received! Establishing connection...\n";
                // Next Step: Update the token and connect
                client->UpdateToken(discordpp::AuthorizationTokenType::Bearer,  accessToken, [client](discordpp::ClientResult result) {
                if(result.Successful()) {
                    std::cout << "🔑 Token updated, connecting to Discord...\n";
                    client->Connect();
                }
                });
            });
        }
        });

        // Keep application running to allow SDK to receive events and callbacks
        while (running) {
        discordpp::RunCallbacks();
        std::this_thread::sleep_for(std::chrono::milliseconds(10));
        }

        return 0;
    }
    ```

=== "GDScript"

    ```gdscript linenums="1"
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

## That's it!
- Read the official documentation
- Convert the C++ examples for GDScript

At first it may sound confuse when I tell you to "convert" C++ to GDSCript, but get easier once you see the [examples](examples/index.md). When in doubt, you can even look at section [`C++` to `GDScript`](cpp_to_gdscript.md).  

> Why don't you write a good "Get Started"?

Discord understand their methods better than me so instead of giving you a `Ctrl+C` or a worst explanation, I'm just going to point you to the right information.  

!!! tip

    **`F1` is your best friend!**

    You can probably find the class that you are looking for by searching part of it name.  

    ---

    I extracted their official documentation to classes/methods using [Doxygen](https://www.doxygen.nl/), so you probably don't need to check [Discord Social SDK Reference](https://discord.com/developers/docs/social-sdk/index.html) to know how they work.  

    --- 

    We attempt to follow [GDScript style guide](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_styleguide.html), so you can probably guess the names of methods, properties, enums, methods, params...
