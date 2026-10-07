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
2. Add GPU access below if wanted, then click **Apply**. Enable **Autostart** on the Docker page if Norri should start with Unraid.
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

### Intel or AMD

If Unraid has `/dev/dri`, click **Add another Path, Port, Variable, Label or Device**, choose **Device**, and set **Value** to `/dev/dri`. Norri handles its access permissions.

### NVIDIA

Install Unraid's **Nvidia-Driver** plugin and confirm it detects your GPU. Switch the container editor to **Advanced View**:

1. Append `--runtime=nvidia` to **Extra Parameters**, keeping the existing parameters.
2. Add a **Variable** with **Key** `NVIDIA_VISIBLE_DEVICES` and **Value** your GPU UUID from the plugin, or `all`.

Add only the GPU configuration your server supports. Leave it out if no compatible GPU is available.

## Updates

The repository is `registry.norri.tv/norri/norri:latest`. Unraid checks its registry digest and offers an update when a newer image is published. **Check for Updates** detects it; **Update** pulls the new image and recreates the container with your existing settings and appdata. A container restart alone does not download a newer image.

Updates are applied when you choose them, unless you separately configure automatic updates. Keep both appdata folders. To back them up, stop Norri, copy both folders together, then start Norri again.

## Help

[Discord](https://discord.gg/YFwxqB3tQh) · [User guides](https://norri.tv/docs/) · [Apple apps](https://norri.tv/download/) · [Report an issue](https://issues.norri.tv/)

The MIT license covers these template files, not the Norri application or its bundled components.
