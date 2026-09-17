# Install FOV Software

We provide pre-built packages for **Windows (x64)**, **macOS (ARM)**, and **Linux (x64)**.

You can download all official versions from the [obs-studio-fov GitHub Releases page](https://github.com/Flexible-Output-View/obs-studio-fov/releases).

---

## Linux (x64)

> [!IMPORTANT]
> **Flatpak Prerequisite:** Flatpak must be installed and configured on your system to run the Linux package. Some distributions like **Ubuntu** require you to install Flatpak manually if it is not already present. Refer to the [official Flatpak setup guide](https://flatpak.org/setup/) for installation instructions specific to your Linux distribution.

### 1. Download
Download the Flatpak package from the releases page:
* **File:** [FOV-linux-x64-release.flatpak](https://github.com/Flexible-Output-View/obs-studio-fov/releases/latest/download/FOV-linux-x64-release.flatpak)

### 2. Install & Run
* **Via GUI:** Open the downloaded file using your desktop's package manager.
* **Via Terminal:** Run the following command to install it (remove `--user` for a global install):
  ```bash
  flatpak --user install FOV-linux-x64-release.flatpak
  ```

You can launch the application from your desktop's application menu or by running:
```bash
flatpak run com.flexible_output_view.FOV
```

---

## macOS (ARM)

> [!IMPORTANT]
> **Apple Silicon (M) Required & Unsigned:** This build is compiled natively for ARM64 macOS architectures (Intel Macs are not supported).
>
> Because the binary is unsigned, macOS Gatekeeper will block it by default on the first launch.

### 1. Download
Download the disk image from the releases page:
* **File:** [FOV-macos-arm64-release.dmg](https://github.com/Flexible-Output-View/obs-studio-fov/releases/latest/download/FOV-macos-arm64-release.dmg)

### 2. Run (Installation Optional)
1. **Mount the DMG:** Double-click the downloaded `.dmg` file to open it.
2. **Run or Move:** Installation is completely optional. You can run the application directly from the mounted disk image, or drag it to your **Applications** folder (or any folder like your Desktop) if you prefer to keep a local copy.
3. **Bypassing the Security Warning:** Because the app is unsigned, macOS will block it on the first launch. If a direct open is blocked, you can authorize it through your system preferences:
   * Try to open the app once (it will display a warning dialog that it cannot be opened).
   * Open your Mac's **System Settings** and go to **Privacy & Security**.
   * Scroll down the page until you find the notice stating that FOV was blocked from use.
   * Click the **"Open Anyway"** button.
   * Confirm your choice by clicking **Open** on the final prompt. You only need to perform this action the very first time you launch the application.

---

## Windows (x64)

> [!IMPORTANT]
> **Unsigned Portable Build:** This release is packaged as a portable ZIP archive and is **unsigned**.
>
> Windows SmartScreen may block the application on first launch.

### 1. Download
Download the portable ZIP archive from the releases page:
* **File:** [FOV-windows-x64-release.zip](https://github.com/Flexible-Output-View/obs-studio-fov/releases/latest/download/FOV-windows-x64-release.zip)

### 2. Run (No Installation Required)
1. **Extract:** Extract the contents of the ZIP archive anywhere on your system (e.g., your Desktop or a custom tools folder).
2. **Launch:** Open the application using the executable located at:
   * `Release\bin\64bit\obs64.exe`
3. **Windows SmartScreen:** Because the binary is unsigned, Windows may display a blue "Windows protected your PC" popup when you first run it.
   * **How to open it:** Click on the **"More info"** link in the warning text, and then click the **"Run anyway"** button that appears at the bottom.
   * You only need to do this the first time you launch the application.
