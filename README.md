# Prism Launcher Multi (Windows)

An unofficial Windows build of [Prism Launcher 10.0.0](https://github.com/PrismLauncher/PrismLauncher/tree/10.0.0) that lets you launch the same instance again from the **Launch** button while it is running. The additional launch can use a saved account or a new offline nickname. There is no fixed limit on the number of windows; available memory and disk space still apply.

This is a patch-based project. It keeps the upstream source and this project's changes separate so you can review the changes and build the program yourself. It is not affiliated with the Prism Launcher team. The patch also allows creating offline accounts without first adding a Microsoft account.

## How it works

1. Start an instance as usual.
2. Select the running instance and click **Launch** again.
3. Choose a saved account or enter another offline nickname.
4. A fresh copy of that instance opens in a separate Prism Launcher process.

Each extra process gets its own instance files and settings under `PrismLauncher-Multi`, beside your normal Prism Launcher data folder. Assets, libraries, Java downloads and metadata are linked to the originals to save space. The helper does **not** copy `minecraft/saves`, `.minecraft/saves`, or `world` folders while a game is running. Other instance files are copied at launch time, so later changes in one copy do not automatically appear in another. Each copy can take considerable disk space; remove old copies when you no longer need them. Do not open the same world from two game windows at once.

## Download and install

Download the Windows ZIP from [Releases](../../releases), extract it, close Prism Launcher, and run `prismlauncher.exe` from the extracted folder. To retain your existing instances and accounts, use your existing Prism Launcher data folder (the usual Windows location is `%APPDATA%\PrismLauncher`). **Back up that folder before switching builds.** Do not copy or publish `accounts.json` or instance data with this project.

This project does not distribute account data or bundle a bypass server. Offline accounts only work where the game or server accepts them. Microsoft account sign-in depends on your own client ID when building (see below).

## Build from source

The [Windows build workflow](.github/workflows/build-windows.yml) builds the patched upstream source. On GitHub, open **Actions → Build Windows → Run workflow** and download the resulting artifact. A tagged release also attaches the ZIP to the release page.

To build locally, install Git, JDK 17, MSYS2 CLANG64 and the packages listed in the workflow. Then:

```sh
git clone --recurse-submodules --branch 10.0.0 https://github.com/PrismLauncher/PrismLauncher.git
cd PrismLauncher
git apply ../patches/PrismLauncher-10.0.0-multi.patch
cmake -S . -B build -G Ninja -DCMAKE_BUILD_TYPE=Release -DCMAKE_PREFIX_PATH=/clang64 -DLauncher_BUILD_PLATFORM=windows -DLauncher_UPDATER_GITHUB_REPO= -DLauncher_IMGUR_CLIENT_ID= -DLauncher_CURSEFORGE_API_KEY= -DLauncher_MSA_CLIENT_ID=
cmake --build build --parallel
```

Run those CMake commands in an MSYS2 CLANG64 shell and provide an install prefix if you want a portable distribution. The workflow contains the complete install and packaging steps, including Qt TLS/OpenSSL files. `Launcher_MSA_CLIENT_ID` must be set to your own registered Microsoft application ID if you need Microsoft account login; automated public builds leave it empty. Existing offline accounts do not require it.

## Scope and source

- Windows only. Other platforms retain their normal launch behavior.
- The extra launch is isolated by copying instance files. It does not make simultaneous access to one world safe.
- A running game's log and the extra launcher's log appear in their respective Prism Launcher processes.
- Based on Prism Launcher 10.0.0, under the [GPL-3.0 license](LICENSE). Changes are in [`patches/PrismLauncher-10.0.0-multi.patch`](patches/PrismLauncher-10.0.0-multi.patch).
