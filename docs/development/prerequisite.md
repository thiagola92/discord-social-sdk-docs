---
icon: lucide/lamp-desk
---

# Prerequisites
The following tools were used in the project, this doesn't mean that you need every single one:

- [Godot](https://godotengine.org/)
- [SCons](https://scons.org/)
- [Python](https://www.python.org/) *(>= 3.12)*
- [Clang](https://clang.llvm.org/)
- [Clang format](https://clang.llvm.org/docs/ClangFormat.html)
    - [VSCode extension](https://marketplace.visualstudio.com/items?itemName=xaver.clang-format)
- [Doxygen](https://www.doxygen.nl/)
- [Mingw64](https://www.mingw-w64.org/) *(cross compile for windows)*
- [OpenJDK](https://openjdk.org/) *(export for android)*
- [Android SDK](https://developer.android.com/tools/sdkmanager) *(cross compile for android)*
    - [Godot instructions](https://docs.godotengine.org/en/stable/tutorials/export/exporting_for_android.html#download-the-android-sdk)
- [Discord SDK for C++](https://discord.com/developers/docs/discord-social-sdk/getting-started/using-c++#step-4-download-the-discord-sdk-for-c++)

## Fedora Script

!!! warning
    Adapt it to your operating system

```bash
# Scons.
sudo dnf install python3-scons;

# Provide GNU static libraries (Fedora doesn't come with it).
sudo dnf install libstdc++-static;

# Clang.
sudo dnf install clang;

# Clang-format.
sudo dnf install clang-tools-extra;

# Doxygen.
sudo dnf install doxygen;

# Mingw64.
sudo dnf install mingw64-gcc-c++;
```
