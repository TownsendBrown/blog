
# Installing RevolverLLM

Revolver is an open-source desktop application for running AI models locally. It supports GGUF models on Windows, Linux, and macOS, with additional MLX support on Apple Silicon.

## Download

Download the latest version for your operating system:

**[RevolverLLM GitHub Releases](https://github.com/TownsendBrown/revolverllm/releases)**

## Windows

1. Download `Revolver-<version>-win-x64.exe`.
2. Run the installer and follow the setup instructions.
3. If Windows SmartScreen blocks the unsigned installer, select **More info → Run anyway** after verifying the download.
4. Launch Revolver from the Start menu.

For GPU acceleration, install the latest compatible NVIDIA or Vulkan GPU drivers.

## Linux

1. Download `Revolver-<version>-linux-x64.AppImage`.
2. Open a terminal in the download directory.
3. Make the AppImage executable and launch it:

```bash
chmod +x Revolver-*-linux-x64.AppImage
./Revolver-*-linux-x64.AppImage
```

Alternatively, use the `.deb` package when available.

For GPU acceleration, ensure your NVIDIA or Vulkan drivers are installed.

## macOS

Currently supports Apple Silicon (M-series) Macs.

1. Download `Revolver-<version>-arm64.dmg`.
2. Open the DMG and drag Revolver into Applications.
3. Launch Revolver.
4. If macOS blocks the application, navigate to **System Settings → Privacy & Security → Open Anyway** after verifying the download.
5. Complete the initial runtime installation.

## First-Time Setup

After launching Revolver:

1. Install the recommended inference runtime. Revolver downloads the appropriate llama.cpp or MLX backend.
2. Open the Models tab and download a model from Hugging Face, or import an existing model.
3. Open the Servers tab and create a new server.
4. Select your model, configure GPU settings, and start the server.
5. Open Chat and begin using your model.

Runtimes are downloaded on first use, so an internet connection is required for initial setup.

## Additional Information

Website: https://revolverllm.com/

Source code: https://github.com/TownsendBrown/revolverllm

Revolver runs inference locally. Your downloaded models remain on your device.
