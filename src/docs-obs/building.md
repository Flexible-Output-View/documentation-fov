# Building FOV

### Prerequisites

[Install git on your system](https://git-scm.com/install).

Then, clone the repository with all submodules:
```bash
git clone --recurse-submodules git@github.com:Flexible-Output-View/obs-studio-fov.git
```

**Or if the above does not work**, you can clone with HTTPS:
```bash
git clone --recurse-submodules https://github.com/Flexible-Output-View/obs-studio-fov.git
```

Then, navigate to the directory `obs-studio-fov`.

## Linux

### Install Dependencies

Run `./requirements.sh`.

> [!NOTE]
> Running `requirements.sh` will install all dependencies for Debian-based distributions.
>
> If you are building on another distribution, please consult the official [**OBS documentation for required packages here**](https://github.com/obsproject/obs-studio/wiki/Build-Instructions-For-Linux/a1d993d4a760ad4e0ce6c7788d527597b73a8953#dependencies).

### Building

You can run one of the following build scripts depending on your needs:

- `./build_portable_linux.sh`
  - Produces a **debug build** of FOV.
  - The executable will be located in `build/rundir/Debug/bin`.
- `./build_portable_linux_release.sh`
  - Produces a **release build** of FOV.
  - The executable will be located in `build/rundir/Release/bin`.


## macOS
Requires macOS 13.5 or later.

### Install Dependencies

Please install the following:
- CMake 3.30 (minimum: CMake 3.28)
- Xcode 15.4 or later

### Building

You can run one of the following build scripts depending on your needs:

- `./build_portable_macOS.sh`
  - Produces a **debug build** of FOV.
  - The executable will be located in `build_macos/frontend/Debug`.
- `./build_portable_macOS_release.sh`
  - Produces a **release build** of FOV.
  - The executable will be located in `build_macos/frontend/Release`.


## Windows
Requires Windows 10 1909+ (or Windows 11). Windows on ARM is not supported.

### Install Dependencies

#### The automated way
You can run the script `.\requirements.bat` in PowerShell as an administrator to install the required dependencies. This script uses `winget` to automatically install everything required.

#### The manual way
If you don't want to use the automated script, you need:

- [Microsoft VCRedist 2015+ x64](https://learn.microsoft.com/fr-fr/cpp/windows/latest-supported-vc-redist?view=msvc-170)
- [Install CMake from here](https://cmake.org) or [here if cmake.org is down](https://sourceforge.net/projects/cmake.mirror/files/v4.3.5/), version 3.28 or later.
- [Install Visual Studio 2022](https://aka.ms/vs/17/release/vs_community.exe) and select **Desktop development with C++**, then select the following individual components:
  - **Windows 11 SDK** (**10.0.22621.0** or later)
  - C++ ATL **v143** build tools (x86 & x64) (**17.13** or later)
  - MSVC **v143** - VS 2022 C++ x64/x86 build tools (**v14.44-17.14** or later)

### Building

You may need to reboot Windows if you just installed all dependencies.

You can run one of the following build scripts depending on your needs:

- `.\build_windows.bat`
  - Produces a **debug build** of FOV.
  - The executable will be located in `build_x64\rundir\Debug\bin\64bit`.
- `.\build_windows_release.bat`
  - Produces a **release build** of FOV.
  - The executable will be located in `build_x64\rundir\Release\bin\64bit`.


# Build Options
FOV offers the following build options:

| Option | Location | Description |
| :--- | :--- | :--- |
| `ENABLE_FOV_DEBUG_INGEST` | `./CMakeLists.txt:25` | Enable a debug route for FOVService. This allows the developer to redirect the output stream to a local URL that can be analyzed locally. |
| `FOV_INTEGRATION_TEST` | `./CMakeLists.txt:26` | Build the fov-integration-test plugin. This is used to run the automated end-to-end integration test. The plugin will set up a scene with two video sources and start a stream to a locally deployed fov-web instance. |
