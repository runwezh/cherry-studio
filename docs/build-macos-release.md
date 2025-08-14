# Cherry Studio macOS 打包发布说明

> 本文档面向在 macOS 上为 Cherry Studio 生成发布版安装包（DMG/ZIP）的工程师，涵盖环境要求、构建命令、网络加速、签名与公证、常见问题与校验方式等。

## 1. 环境要求
- OS：macOS 11.0+（项目最低支持版本：`minimumSystemVersion: 20.1.0`）
- Node.js：>= 22（项目使用 Node v22）
- Yarn：4.9.1（项目已使用，仓库内置 .yarnrc.yml）
- Xcode Command Line Tools：`xcode-select -p` 可正常返回路径
- Python：3.x（供部分依赖安装/构建使用）

## 2. 构建目标与产物
- 构建工具：electron-builder（配置见 `electron-builder.yml`）
- macOS 目标：`dmg` 与 `zip`
- 产物输出路径：`dist/`
- 默认签名：若未配置证书，会降级为 ad-hoc 签名；默认不做 notarize（`mac.notarize: false`）。

文件命名示例（以 1.5.6 为例）：
- `dist/Cherry-Studio-<version>-arm64.dmg`
- `dist/Cherry-Studio-<version>-arm64.zip`
- 对应的 `.blockmap` 与 `latest-mac.yml` 会一并生成

## 3. 常用脚本
- 安装依赖：`yarn install`
- 类型检查与构建源码：`yarn build`（被下述打包脚本调用）
- 打包（双架构）：`yarn build:mac`
- 打包（arm64）：`yarn build:mac:arm64`
- 打包（x64）：`yarn build:mac:x64`

> 由于苹果芯片（Apple Silicon）机器上构建 arm64 成功率更高，建议优先使用 `build:mac:arm64`。

## 4. 快速构建（arm64 推荐）
1) 安装依赖（如网络受限，见第 6 节）：
```
yarn install
```

2) 构建打包（禁用自动证书发现，避免缺少签名证书导致失败）：
```
CSC_IDENTITY_AUTO_DISCOVERY=false yarn build:mac:arm64
```

构建成功后，产物位于 `dist/` 目录。

## 5. 构建 x64 或双架构
- x64：
```
CSC_IDENTITY_AUTO_DISCOVERY=false yarn build:mac:x64
```
- 双架构一次性构建：
```
CSC_IDENTITY_AUTO_DISCOVERY=false yarn build:mac
```

> 如果网络下载 Electron 二进制失败，请参考第 6 节加速或第 6.3 节的“手动预下载”。

## 6. 网络加速与下载失败处理
### 6.1 使用镜像加速 Electron 下载
设置 Electron 镜像与缓存目录（可持久化加速后续构建）：
```
export ELECTRON_MIRROR=https://npmmirror.com/mirrors/electron/
export ELECTRON_CACHE=$HOME/.electron
mkdir -p "$ELECTRON_CACHE"
```
随后执行安装/构建：
```
yarn install
CSC_IDENTITY_AUTO_DISCOVERY=false yarn build:mac:arm64
```

### 6.2 增大网络超时（Yarn）
```
yarn install --network-timeout 600000
```

### 6.3 手动预下载 Electron 二进制（推荐稳妥方案）
以 Electron v37.2.3 为例，分别是：
- arm64 包名：`electron-v37.2.3-darwin-arm64.zip`
- x64 包名：`electron-v37.2.3-darwin-x64.zip`

示例（arm64）：
```
mkdir -p "$HOME/.electron"
cd "$HOME/.electron"
curl -fL -o electron-v37.2.3-darwin-arm64.zip \
  https://npmmirror.com/mirrors/electron/v37.2.3/electron-v37.2.3-darwin-arm64.zip
```
构建时告知缓存目录与版本目录：
```
export ELECTRON_MIRROR=https://npmmirror.com/mirrors/electron/
export ELECTRON_CUSTOM_DIR=v37.2.3
export ELECTRON_CACHE=$HOME/.electron
CSC_IDENTITY_AUTO_DISCOVERY=false yarn build:mac:arm64
```

如需 x64，请将上方文件名替换为 `electron-v37.2.3-darwin-x64.zip` 并执行 `yarn build:mac:x64`。

## 7. 签名与公证（用于对外发布）
默认配置下，`electron-builder.yml` 将 `mac.notarize` 设为 `false`，同时项目在 `afterSign` 阶段调用 `scripts/notarize.js`。该脚本在检测到以下环境变量后会执行苹果公证：
- `APPLE_ID`：Apple 开发者账号邮箱
- `APPLE_APP_SPECIFIC_PASSWORD`：应用专用密码
- `APPLE_TEAM_ID`：开发者团队 Team ID

使用方式：
```
export APPLE_ID="your@apple.id"
export APPLE_APP_SPECIFIC_PASSWORD="xxxx-xxxx-xxxx-xxxx"
export APPLE_TEAM_ID="YOURTEAMID"

# 可选：继续使用镜像与缓存以加速
export ELECTRON_MIRROR=https://npmmirror.com/mirrors/electron/
export ELECTRON_CACHE=$HOME/.electron
export ELECTRON_CUSTOM_DIR=v37.2.3

# 构建并触发 afterSign 脚本中的 notarize
yarn build:mac:arm64
```

注意事项：
- 请勿将账号密码等敏感信息写入仓库；使用临时环境变量或本地 .env（勿提交）
- 若希望改为使用 electron-builder 内置 notarize 流程，可在 `electron-builder.yml` 中将 `mac.notarize` 设为 `true`

## 8. 产物校验与验证安装
- 计算 SHA256（示例）：
```
shasum -a 256 dist/Cherry-Studio-*.dmg dist/Cherry-Studio-*.zip
```
- 在本机验证安装：
  - 双击 `dist/*.dmg` 进行安装
  - 首次启动若提示“来自未识别开发者”，请在 系统设置 > 隐私与安全 允许运行
  - 如果已完成签名与公证，首次启动将不会出现此提示

## 9. 自动更新与发布
- 配置：见 `electron-builder.yml` 中 `publish: { provider: generic, url: https://releases.cherry-ai.com }`
- 对外发布时，请确保以下文件一并上传到发布服务器对应目录：
  - `Cherry-Studio-<version>-<arch>.dmg`
  - `Cherry-Studio-<version>-<arch>.dmg.blockmap`
  - `Cherry-Studio-<version>-<arch>.zip`
  - `Cherry-Studio-<version>-<arch>.zip.blockmap`
  - `latest-mac.yml`

## 10. 常见错误排查
- `connect ETIMEDOUT`：网络超时。请使用镜像（6.1）或手动预下载（6.3）。
- `HTTPError: 404 Not Found`：镜像路径不正确或版本尚未同步。建议手动预下载对应 zip 并设置 `ELECTRON_CACHE` 与 `ELECTRON_CUSTOM_DIR`。
- 缺少签名证书导致失败：设置 `CSC_IDENTITY_AUTO_DISCOVERY=false` 或配置有效的苹果开发者签名证书，并按第 7 节执行公证。

---

如需我帮你扩展到 x64/双架构、接入完整签名与公证流程、或自动化上传至发布服务器，请直接提出需求。