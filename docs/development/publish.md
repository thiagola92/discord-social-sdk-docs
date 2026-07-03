---
icon: lucide/package
---

# Publish
These steps are only for GDExtension owner:  

```bash
# Build for many platforms.
scons platform=linux    target=template_debug   arch=x86_64
scons platform=linux    target=template_release arch=x86_64
scons platform=windows  target=template_debug   arch=x86_64
scons platform=windows  target=template_release arch=x86_64
#scons platform=android  target=template_debug   arch=arm32
#scons platform=android  target=template_debug   arch=arm64
#scons platform=android  target=template_debug   arch=x86_32
#scons platform=android  target=template_debug   arch=x86_64
#scons platform=android  target=template_release arch=arm32
#scons platform=android  target=template_release arch=arm64
#scons platform=android  target=template_release arch=x86_32
#scons platform=android  target=template_release arch=x86_64

# Remove any old ZIP file.
rm discord_social_sdk.zip

# Generate new ZIP file.
zip -r discord_social_sdk.zip demo/addons/discord_social_sdk/**
```

- [Manually add as assets in Github release](https://github.com/thiagola92/discord-social-sdk/releases)  
- [Manually add in Godot Asset Library](https://godotengine.org/asset-library/asset)  
- [Manually add in Godot Asset Store](https://store.godotengine.org/)
