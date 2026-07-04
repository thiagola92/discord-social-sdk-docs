---
icon: lucide/hammer
---

# Build

These instructions show how to build the GDExtension for Godot version `4.4` on Linux.  

!!! note "Version compatibility"
    GDExtensions targeting an earlier version of Godot should work in later minor versions, so GDExtension for `4.4` should work in any version above it.  

    The opposite is not guaranteed. For example, version `4.7` changed how to use [`memnew(RefCounted)`](https://github.com/godotengine/godot/pull/111965).  

    This means that our Python script generate an invalid C++ code for versions >= `4.7`, which block us from building the GDExtension with newer versions.  
    
    | Before | After |
    | ------ | ----- |
    | `DiscordObject *obj` | `Ref<DiscordObject> obj` |
    
    Making our Python script generate different code for specifics version would add a lot of complexity. So the recomentation is to built with old version because it will work in the newer.  
    

## Environment variables
The Python script expect that any tool can be accessed by it name alone, but you can also indicates the correct location throught environment variables:  

```bash
export CLANG_FORMAT=clang-format
export DOXYGEN=doxygen
export GODOT=godot
```

!!! warning
    Do not use relative paths because we use execute `$GODOT` from different directories.


!!! tip
    Save to an `.env` file so you can always load with `source .env`.

## Setup Repository
These will build for your current operating system (adapt it to your use case):  

```bash
# Clone repository, submodules and only file needed.
git clone --recurse-submodules --filter=blob:none https://github.com/thiagola92/discord-social-sdk.git
cd discord-social-sdk

# Manually download the DiscordSocialSdk zip to the project directory.

# Unzip libraries and headers to correct directories.
unzip DiscordSocialSdk*.zip -d /tmp/
cp -r /tmp/discord_social_sdk/lib/release/*.aar lib/android/
cp -r /tmp/discord_social_sdk/lib/release/*.xcframework lib/ios/
cp -r /tmp/discord_social_sdk/lib/release/*.so lib/linux/
cp -r /tmp/discord_social_sdk/lib/release/*.dylib lib/macos/
cp -r /tmp/discord_social_sdk/bin/release/*.dll lib/windows/
cp -r /tmp/discord_social_sdk/lib/release/*.lib lib/windows/
cp -r /tmp/discord_social_sdk/include/* include/
unzip lib/android/discord_partner_sdk.aar -d lib/android/aar
rm -rf /tmp/discord_social_sdk

# Generate GDExtension API files.
cd godot-cpp
$GODOT --dump-extension-api
scons custom_api_file=extension_api.json
cd ..

# Generate GDExtension source code.
python scripts/main.py --code

# Generate GDExtension library.
scons custom_api_file=godot-cpp/extension_api.json

# Open project, at least once, to be able to generate GDExtension documentation.
$GODOT ./demo/project.godot

# Generate GDExtension documentations.
python scripts/main.py --docs

# Link documentation to GDExtension library.
scons custom_api_file=godot-cpp/extension_api.json

# Open project (may need to open two times).
$GODOT ./demo/project.godot
```

!!! warning
    Make sure that Godot version match with `godot-cpp` repository

## Rebuild
Run these steps every time that you modify the Python code:  

```bash
python scripts/main.py --code --docs
scons custom_api_file=godot-cpp/extension_api.json
$GODOT ./demo/project.godot
```
