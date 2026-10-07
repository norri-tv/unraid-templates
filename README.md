# Norri for Unraid

This Unraid Docker template installs the existing Norri Docker image. It includes the server, web interface, database and transcoder.

Unraid® is a registered trademark of Lime Technology, Inc. This application is not affiliated with, endorsed, or sponsored by Lime Technology, Inc.

## Install

### Community Apps

When Norri appears in Community Apps, open **Apps**, search for **Norri**, and click **Install**. If it is not listed yet, use the manual method below.

### Manual template

1. Download [norri.xml](https://raw.githubusercontent.com/norri-tv/unraid-templates/main/templates/norri.xml) and copy it to `/boot/config/plugins/dockerMan/templates-user/my-norri.xml` on your Unraid server.
2. Open **Docker → Add Container** and choose **Norri** from the user templates.

### Configure and start

1. Select your existing Unraid folders for **Movies**, **TV shows** and **Music** as needed. Leave unused Host Paths blank. Leave both appdata paths and the port at their defaults unless you need to change them.
2. For hardware transcoding, follow [GPU access](#gpu-access) for your hardware before clicking **Apply**. Skip this step if you do not want GPU acceleration. Enable **Autostart** on the Docker page if Norri should start with Unraid.
3. Open **WebUI**, create your administrator account, and add your libraries. The folder picker starts at `/media`, where your mappings appear as `movies`, `tv-shows`, `music`, and any other names you have added. Select the appropriate folder for each library.

Keep the appdata share on your SSD/cache pool. The two appdata folders store your settings and database and must remain separate from each other and your media.

## Media folders

Each Path mapping connects a local Unraid media folder to a named folder beneath `/media` in Norri. Your media stays where it is; nothing needs moving or copying.

**Movies**, **TV shows** and **Music** mappings are already provided. Set each **Host Path** to the local Unraid folder you want to use:

| Mapping name | Host Path example | Container Path |
| --- | --- | --- |
| Movies | `/mnt/user/movies` | `/media/movies` |
| TV shows | `/mnt/user/tv shows` | `/media/tv-shows` |
| Music | `/mnt/user/music` | `/media/music` |

Select your actual local folders; the Host Paths above are examples. **Leave unused Host Paths blank.** Unraid skips blank mappings, so they do not need deleting. You can remove an unused entry if you prefer.

You can add as many mappings as you need. For another media folder, click **Add another Path, Port, Variable, Label or Device**, choose **Path**, and fill in **Name**, **Host Path** (your local Unraid folder), and a unique **Container Path** beneath `/media`. Choose **Read Only** access.

In Norri, select `/media/movies` for your movie library, `/media/tv-shows` for your TV library, and `/media/music` for your music library. Only mappings with a Host Path selected appear in the folder picker.

## GPU access

Choose the instructions for the GPU you want Norri to use. The GPU must be available to Unraid's host driver, rather than reserved for a virtual machine. Hardware support depends on the GPU model and driver.

### Intel, including Arc, or AMD

1. Confirm Unraid has a working driver for your GPU and `/dev/dri` is present. You can check by opening Unraid's **Terminal** and running `ls -l /dev/dri`. If the folder is missing, resolve the host GPU/driver setup before continuing. A separate NVIDIA plugin is not needed for Intel or AMD.
2. In the Norri container editor, click **Add another Path, Port, Variable, Label or Device**. Choose **Device**, set **Name** to `GPU`, and set **Value** to `/dev/dri`. Click **Add**.

This exposes the host's graphics devices to Norri; Norri handles their access permissions. No GPU UUID or NVIDIA environment variables are needed for this route.

### NVIDIA

1. In Unraid **Apps**, install **Nvidia Driver** if it is not already installed. Complete the plugin's first-install instructions, including its Docker restart or reboot step. Open the installed plugin's settings and confirm your GPU appears under **Installed GPU(s)**.
2. Edit the Norri container and switch to **Advanced View**. Append `--runtime=nvidia` to **Extra Parameters**, with a space before it. Keep the existing parameters.
3. Click **Add another Path, Port, Variable, Label or Device**, choose **Variable**, and add each row below. **Name** is the label displayed in Unraid; **Key** must match exactly.

| Name | Key | Value |
| --- | --- | --- |
| NVIDIA GPU | `NVIDIA_VISIBLE_DEVICES` | `all`, or the UUID of the GPU you want to use |
| NVIDIA driver capabilities | `NVIDIA_DRIVER_CAPABILITIES` | `all` |

Use `all` for **NVIDIA_VISIBLE_DEVICES** to make all NVIDIA GPUs available; no ID lookup is needed. To select one GPU, copy its UUID from **Installed GPU(s)** in the Nvidia Driver plugin settings. Include the `GPU-` prefix and paste it without spaces. Alternatively, open Unraid's **Terminal** and run:

```sh
nvidia-smi --query-gpu=name,uuid --format=csv,noheader
```

Copy the UUID next to the GPU you want. Do not copy another user's GPU ID or an example ID. Setting both variables to `all` is valid; the runtime setting is still required for this setup.

The plugin's [setup guide](https://forums.unraid.net/topic/98978-plugin-nvidia-driver/) covers driver installation and supported cards. [NVIDIA's container reference](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/docker-specialized.html) explains device selection and driver capabilities.

### Apply and check

Click **Apply**, then open Norri's **Settings → Transcoding**. Keep **Auto-detect**, or choose the detected GPU you want to use. Play a video that needs conversion and check **Settings → Active Sessions** for its video encoder. Direct playback does not need video encoding, so it will not demonstrate GPU transcoding.

See [Hardware Transcoding](https://norri.tv/docs/advanced/hardware-transcoding/) for Norri's settings and playback checks. If you do not want GPU acceleration, omit the optional Device mapping, NVIDIA variables and `--runtime=nvidia` setting.

## Updates

The repository is `registry.norri.tv/norri/norri:latest`. Unraid checks its registry digest and offers an update when a newer image is published. **Check for Updates** detects it; **Update** pulls the new image and recreates the container with your existing settings and appdata. A container restart alone does not download a newer image.

Updates are applied when you choose them, unless you separately configure automatic updates. Keep both appdata folders. To back them up, stop Norri, copy both folders together, then start Norri again.

## Help

[Discord](https://discord.gg/YFwxqB3tQh) · [User guides](https://norri.tv/docs/) · [Apple apps](https://norri.tv/download/) · [Report an issue](https://issues.norri.tv/)

The MIT license covers these template files, not the Norri application or its bundled components.
