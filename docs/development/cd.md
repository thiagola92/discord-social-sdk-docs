---
icon: lucide/infinity
---

# Continous Development

## Steps
Repeat these steps every modification.  

!!! warning "If your Godot uses `double`, add `precision=double` to all scons commands"

```bash
# Generate GDExtension source code and documentations.
python scripts/main.py --code --docs

# Generate GDExtension library (run the ones that interest you).
scons custom_api_file=godot-cpp/extension_api.json target=template_debug platform=android arch=arm32
scons custom_api_file=godot-cpp/extension_api.json target=template_debug platform=android arch=arm64
scons custom_api_file=godot-cpp/extension_api.json target=template_debug platform=android arch=x86_32
scons custom_api_file=godot-cpp/extension_api.json target=template_debug platform=android arch=x86_64
scons custom_api_file=godot-cpp/extension_api.json target=template_debug platform=linux arch=x86_64
scons custom_api_file=godot-cpp/extension_api.json target=template_debug platform=windows arch=x86_64
scons custom_api_file=godot-cpp/extension_api.json target=template_release platform=android arch=arm32
scons custom_api_file=godot-cpp/extension_api.json target=template_release platform=android arch=arm64
scons custom_api_file=godot-cpp/extension_api.json target=template_release platform=android arch=x86_32
scons custom_api_file=godot-cpp/extension_api.json target=template_release platform=android arch=x86_64
scons custom_api_file=godot-cpp/extension_api.json target=template_release platform=linux arch=x86_64
scons custom_api_file=godot-cpp/extension_api.json target=template_release platform=windows arch=x86_64

# Open project.
godot ./demo/project.godot
```
