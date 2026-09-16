# Install FOV Software

We provide pre-built packages for **Windows (x64)**, **macOS (ARM)**, and **Linux (x64)**.

You can download all official versions from the [obs-studio-fov GitHub Releases page](https://github.com/Flexible-Output-View/obs-studio-fov/releases).

---

## Linux (x64)

### 1. Download
Download the Flatpak package from the releases page:
* **File:** `FOV-linux-x64-release.flatpak`

### 2. Install & Run
* **Via GUI:** Open the downloaded file using your desktop's package manager.
* **Via Terminal:** Run the following command to install it (remove --user for a global install):
  ```bash
  flatpak --user install FOV-linux-x64-release.flatpak
  ```

You can launch the application from your desktop's application menu or by running:
```bash
flatpak run com.flexible_output_view.FOV
```

---

## macOS (ARM)

### 1. Download
Download the disk image from the releases page:
* **File:** `FOV-macos-arm64-release.dmg`

### 2. Install & Run
Open the downloaded `.dmg` file to run or install the application on your system.

---

## Windows (x64)

### 1. Download
Download the portable ZIP archive from the releases page:
* **File:** `FOV-windows-x64-release.zip`

### 2. Install & Run
Extract the contents of the ZIP archive. You can then run the application using the executable located at:
* `Release\bin\64bit\obs64.exe`
