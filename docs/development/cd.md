---
icon: lucide/infinity
---

# Continous Development

## Steps
Repeat these steps every modification.  

```bash
# Generate GDExtension source code and documentations.
python3 scripts/main.py --code --docs

# Generate GDExtension library (run the ones that interest you).
scons platform=android target=template_debug   arch=arm32
scons platform=android target=template_debug   arch=arm64
scons platform=android target=template_debug   arch=x86_32
scons platform=android target=template_debug   arch=x86_64
scons platform=linux   target=template_debug   arch=x86_64
scons platform=windows target=template_debug   arch=x86_64
scons platform=android target=template_release arch=arm32
scons platform=android target=template_release arch=arm64
scons platform=android target=template_release arch=x86_32
scons platform=android target=template_release arch=x86_64
scons platform=linux   target=template_release arch=x86_64
scons platform=windows target=template_release arch=x86_64

# Open project.
godot ./demo/project.godot
```