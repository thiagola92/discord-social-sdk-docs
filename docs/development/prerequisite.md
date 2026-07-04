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

# OpenJDK.
sudo dnf install java-latest-openjdk-devel;

# Android SDK.
mkdir -p $HOME/Android/Sdk;
curl -fl https://dl.google.com/android/repository/commandlinetools-linux-14742923_latest.zip -o commandlinetools.zip;
unzip commandlinetools.zip;
mv cmdline-tools $HOME/Android/Sdk;
mkdir $HOME/Android/Sdk/comdline-tools/latest
mv $HOME/Android/Sdk/comdline-tools/bin $HOME/Android/Sdk/comdline-tools/latest;
mv $HOME/Android/Sdk/comdline-tools/lib $HOME/Android/Sdk/comdline-tools/latest;
mv $HOME/Android/Sdk/comdline-tools/NOTICE.txt $HOME/Android/Sdk/comdline-tools/latest;
mv $HOME/Android/Sdk/comdline-tools/source.properties $HOME/Android/Sdk/comdline-tools/latest;
echo "PATH=\"\$HOME/Android/SDK/cmdline-tools/latest/bin:\$PATH\"" >> $HOME/.bashrc;
sdkmanager --sdk_root=$HOME/Android/Sdk "platform-tools" "build-tools;35.0.1" "platforms;android-35" "cmdline-tools;latest" "cmake;3.10.2.4988404" "ndk;28.1.13356709"
rm commandlinetools.zip;
```
