# BongoCat · Linux Wayland 版

[English](./README.md)

这是 BongoCat 的个人 fork，面向 Linux Wayland 桌面，已在 Hyprland 上验证。本 fork 新增了跨设备联机、按设备保存猫窗口位置，以及将多只猫合成为一路透明画面的 OBS 输出。

![Wayland 桌面上的三只联机猫](./screenshots/multiplayer-three-cats.png)

## 构建和运行

在 Linux x86-64 上安装 Rust 和 `just`，然后在仓库根目录构建：

```sh
just build --environment development --target x86_64-unknown-linux-gnu
```

运行 `target/package/` 下带版本号的可执行文件。以 2.4.1 为例：

```sh
./target/package/BongoCat-2.4.1-linux-x64.bin
```

## 跨设备联机

1. **创建房间。** 在一台设备上打开「设置 → 联机 → 服务端 → 配置服务端…」。开启「在本机运行联机服务」，选择允许局域网连接的监听方式，记下端口和房间密钥，再点击「应用服务端设置」。
2. **加入房间。** 在其他设备打开「设置 → 联机 → 客户端 → 设置连接…」，选择「手动指定地址」，输入服务端地址（例如 `ws://192.168.1.10:8787`）和房间密钥，点击「连接」。如果运行服务端的设备也要加入同一房间，可选「跟随本机的联机服务」。
3. **共享输入。** 在要共享的设备上开启「把我的输入发到房间」。
4. **选择要显示的猫。** 在「展示设备」中通过开关选择本机和最多两台远端设备。房间名单会自动显示设备名称和共享状态。连接和展示设置即时生效。

「状态」第一行显示本机设备 ID，可点击「复制」。

![联机状态中的本机设备 ID 和复制按钮](./screenshots/multiplayer-local-device-id.png)

![本机与远端设备的展示开关](./screenshots/multiplayer-device-switches.png)

### 窗口显示和位置

「模型窗口」中的显示总开关控制所有猫。关闭时隐藏全部猫，开启时按「展示设备」的选择显示。

在使用 Wayland layer-shell 时，正常退出会按设备 ID 将各只猫的窗口位置保存到本机的 `window-state.json`。再次展示同一设备时会恢复其位置。普通 Wayland 窗口的位置由合成器管理。

## 在 OBS 中使用透明画面

一台 V4L2 虚拟摄像头将选中的猫合成为一路 1280 × 720、30 FPS 的透明画面，沿用它们在本机的相对位置。某台已选设备离线时，对应位置留空。

1. 通过发行版的软件包安装 v4l2loopback 驱动和 `v4l2loopback-ctl`。系统还需提供 `pkexec`、正在运行的图形 polkit 授权代理和 udev。
2. 打开「设置 → 模型窗口 → Alpha 输出」，点击「重新检测」，再点击「安装虚拟摄像头」并完成系统授权。然后开启「启用 Alpha 输出」。
3. 在 OBS 中添加「视频采集设备」源，选择 **BongoCat Alpha**。开发构建对应 **BongoCat Alpha (Development)**。

系统重启后需重新安装虚拟摄像头并授权。从旧版 Alpha 输出升级时，也请执行一次安装以更新设备记录。透明输出已使用 OBS 33.0.1 和 v4l2loopback 0.15.4 验证。

![一帧 V4L2 透明画面中的三只猫](./screenshots/obs-alpha-composite.png)

## 来源与许可

基于 [ayangweb/BongoCat](https://github.com/ayangweb/BongoCat) 和 [@xiaozhangup 的 Wayland 支持](https://github.com/ayangweb/BongoCat/pull/1114)。联机的房间与密钥设计参考 [JMTeCHNoLOGY/BongoCat](https://github.com/JMTeCHNoLOGY/BongoCat)。本项目采用 [Apache License 2.0](./LICENSE)。
