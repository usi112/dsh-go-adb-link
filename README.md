## 兼容性

- 适配 **DSH Go 1.0.16**（引擎 `r15-dsh0.2.2-rc.1-node24.22.1`，Android arm64）。
- 1.0.16 新增了「本机 ADB shell 工具」和「Shizuku/本机 ADB 自更新」，两者都会碰到 adbd；实测升级后本插件的 `127.0.0.1:3455` 通道会自动恢复，`lastError` 为空，无需重连或重装。
- 1.0.15 可用；更早的版本没有验证。

## 安装

市场已提交收录（PR 审核中），收录前可以先用下面的方式，效果一样：

1. **npm 安装**（推荐；国内镜像已同步）

   ```sh
   dsh plugin --profile web add dsh-go-adb-link
   ```

   提示找不到版本时，加官方源：`dsh plugin --profile web add dsh-go-adb-link --registry=https://registry.npmjs.org/`

2. **GitHub 直接装**

   ```sh
   dsh plugin --profile web add github:edgy7493/dsh-go-adb-link
   ```

3. **离线 tarball**：从 [Releases](https://github.com/edgy7493/dsh-go-adb-link/releases) 下载
   `dsh-go-adb-link-1.2.4.tgz` 放到手机，然后
   `dsh plugin --profile web add /sdcard/Download/dsh-go-adb-link-1.2.4.tgz`

装完到 App 的「引擎」页重启一次引擎，输入框上方的状态条就会出现。

装完状态条立即出现在输入框上方；`lib/` 代码改动需要到 App 的「引擎」页重启引擎。

## 首次授权（只需一次）

第一次连接时手机会弹出「允许 USB 调试吗」。请点**允许**，并**勾选「始终允许使用这台计算机进行调试」**。
勾了之后重连不再弹窗，插件才能全自动；没勾的话每次重连都要再点一次。

## 端口不是 3455 也能用吗

- **同一个手机换了端口**：可以。点「连接」会全端口扫描（1–65535），发 ADB 握手找 adbd，
  不看端口号；扫描只认 `CNXN`/`AUTH` 应答，并过滤 TCP 自连造成的误报。
- **另一个手机打开了 adb**：插件跑在哪台手机的环境里就找哪台，不会去连别的设备的局域网端口。
- **只支持经典 ADB over TCP**（`adb tcpip`、Stellar 的 TCP 功能）。Android 11+ 原生
  「无线调试」是 TLS 配对端口，纯 ADB 握手探测不到。

自动重连先试记住的端口和 `3455`、`5555`；都不通且之前授权过，会在后台重扫。

## 数据与安全

- 运行记忆写在 `$DSH_HOME/dsh-go/adb-link/state.json`（端口、是否授权、是否自动重连），
  不在插件包内，卸载/升级不会带走或误发。
- 插件会执行本机的 `adb` 程序；缺 adb 时会调用 `apt-get install -y adb`。
- 不读取剪贴板、输入框、对话内容；只连 `127.0.0.1` 上识别出的 adbd。
- 本机 adb server（`127.0.0.1:5037`）与 bash 共享，所以插件连上后普通 `adb` 命令也能用。

## 卸载

```sh
dsh plugin --profile web remove dsh-go-adb-link
```

也可以直接在 App 的插件详情里卸载。

## 更新日志

- **1.2.4**：新增面向 AI 的 `adb` 工具（结构化参数、自动重连、原生结果卡）；提示词里也提到它。
- **1.2.3**：连接成功后注入「主动使用 adb」的系统提示（系统级操作用 adb，不先问用户）；
  保留原有的自动查找端口、自动重连与面板。
- 0.1.0：首个版本，状态条 + 面板 + 自动连接。

## License

MIT
