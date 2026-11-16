# XunCloud 账户接入

状态菜单的 `Connect cloud account` 打开本机 GUI 的 `?cloud=join`。用户填服务地址、普通账号和密码；本机 core 自动完成个人目录和设备授权。该入口只接受回环地址配置，不将服务器管理 API key 交给普通用户。

## 打包新版内核

先在相邻 `xun-cloud` 仓库构建与目标机器架构匹配的二进制：

```sh
go generate ./lib/api/auto
go build -o bin/syncthing ./cmd/syncthing
```

在本项目 Xcode 构建设置/命令行传入：

```text
XUNCLOUD_CORE_BINARY=/绝对路径/xun-cloud/bin/syncthing
```

资源脚本将该文件复制到构建产物的 `Contents/Resources/syncthing/syncthing`，而不是下载上游二进制。不自动修改已安装 App 或正在运行的服务。通用包应由发行方提供同时支持目标架构的内核；arm64 开发构建不能当作 universal 发行包。

未传此参数时保留原有打包行为，**上游 core 不具备本功能**。当前包装器已禁止 core 自行升级，但自定义发行包仍应审查其 Sparkle 更新源，避免整个 App 被上游包替换。

服务器需有效 HTTPS 证书；私有 CA 可以通过启动环境 `XUNCLOUD_CA_FILE` 指定，系统公信证书不需额外设置。服务端部署及权限边界见相邻 `xun-cloud/docs/cloud-accounts.md`。

本机只有 Command Line Tools，没有完整 Xcode；修改已做 XML/脚本检查、Swift 类型检查、Objective-C 语法检查和内核编译。Objective-C 检查仅报告既有的旧通知 API 弃用警告。完整 App/DMG、签名、公证和交互验收尚未运行。
