# BongoCat for Linux Wayland

[简体中文](./README.zh-CN.md)

This personal fork of BongoCat runs on Linux Wayland and has been tested on Hyprland. It adds multiplayer across devices, per-device cat window placement, and a single transparent OBS feed that can include multiple cats.

![Three multiplayer cats on a Wayland desktop](./screenshots/multiplayer-three-cats.png)

## Build and run

Install Rust and `just` on Linux x86-64, then build from the repository root:

```sh
just build --environment development --target x86_64-unknown-linux-gnu
```

Run the versioned executable in `target/package/`. For version 2.4.1:

```sh
./target/package/BongoCat-2.4.1-linux-x64.bin
```

## Multiplayer

1. **Host a room.** On one device, open the server configuration in **Settings → Multiplayer**. Enable the server on this device, choose a listen mode that accepts LAN connections, note the port and room key, and apply the server settings.
2. **Join the room.** On each other device, open the connection settings under **Client**, enter the server address (for example, `ws://192.168.1.10:8787`) and room key, and connect. The host can also join by selecting its local server as the connection source.
3. **Share input.** Turn on input sharing on each device whose input you want the room to receive.
4. **Choose which cats to show.** Use the toggles under **Display devices** to show the local cat and up to two remote cats. The room list shows device names and sharing status automatically. Connection and display settings take effect immediately.

The first row in the status panel shows the local device ID and a copy button.

![Local device ID and copy button in the multiplayer status panel](./screenshots/multiplayer-local-device-id.png)

![Display toggles for local and remote devices](./screenshots/multiplayer-device-switches.png)

### Window visibility and placement

The visibility switch under **Model window** controls all cats. Turn it off to hide every cat. Turn it on to show the cats selected under **Display devices**.

With Wayland layer-shell, BongoCat saves each cat's window position by device ID in the local `window-state.json` on normal exit. It restores that position when you show the same device again. The compositor manages placement for ordinary Wayland windows.

## Transparent OBS feed

One V4L2 virtual camera combines the selected cats into a transparent 1280 × 720 feed at 30 FPS, using their relative positions on the local desktop. If a selected device goes offline, its spot stays empty.

1. Install the v4l2loopback driver and `v4l2loopback-ctl` through your distribution. The system also needs `pkexec`, a running graphical polkit authentication agent, and udev.
2. Open **Settings → Model window → Alpha output**, refresh detection, then install the virtual camera and authorize the system prompt. Enable Alpha output.
3. Add a **Video Capture Device** source in OBS and select **BongoCat Alpha**. For a Development build, select **BongoCat Alpha (Development)**.

Reinstall and authorize the virtual camera after reboot. When upgrading from an older Alpha output build, run the installation once to update the device record. Transparent output was verified with OBS 33.0.1 and v4l2loopback 0.15.4.

![Three cats in one transparent V4L2 frame](./screenshots/obs-alpha-composite.png)

## Credits and license

Based on [ayangweb/BongoCat](https://github.com/ayangweb/BongoCat) and [@xiaozhangup's Wayland work](https://github.com/ayangweb/BongoCat/pull/1114). The multiplayer room and key design draws on [JMTeCHNoLOGY/BongoCat](https://github.com/JMTeCHNoLOGY/BongoCat). Licensed under the [Apache License 2.0](./LICENSE).
