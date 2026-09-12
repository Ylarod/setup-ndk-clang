# ndk-clang

简体中文 | [English](README.md)

Android NDK Clang 预编译工具链和 GitHub Action，方便 CI/CD 集成。

本项目提供：

1. **预编译 Clang 工具链** - 从 AOSP 重新打包，方便下载
2. **GitHub Action** - 轻松在 CI/CD 工作流中使用

## 快速开始（GitHub Action）

```yml
steps:
  - uses: actions/checkout@v4
  - uses: Ylarod/setup-ndk-clang@v1
    with:
      ndk-version: r30
  - run: clang --version
```

## 功能特性

- 🚀 支持多个 NDK 版本（r30, r29, r28c, r27d, r26d, r25c）
- 💾 自动缓存，加快构建速度
- 🖥️ 跨平台支持（Linux、macOS、Windows）
- 📦 使用 zstd 压缩的可重现构建
- ⚡ 从 GitHub Releases 快速下载

## 使用方法

### 基础用法

```yml
steps:
  - uses: actions/checkout@v4
  - uses: Ylarod/setup-ndk-clang@v1
    with:
      ndk-version: r30
  - run: clang --version
```

### 使用安装路径

```yml
steps:
  - uses: actions/checkout@v4
  - uses: Ylarod/setup-ndk-clang@v1
    id: setup-clang
    with:
      ndk-version: r30
      add-to-path: false
  - run: ${{ steps.setup-clang.outputs.clang-path }}/bin/clang --version
```

### 启用本地缓存

```yml
steps:
  - uses: actions/checkout@v4
  - uses: Ylarod/setup-ndk-clang@v1
    with:
      ndk-version: r30
```

### 多版本工具链

```yml
steps:
  - uses: actions/checkout@v4

  - uses: Ylarod/setup-ndk-clang@v1
    id: clang-r30
    with:
      ndk-version: r30
      add-to-path: false

  - uses: Ylarod/setup-ndk-clang@v1
    id: clang-r26d
    with:
      ndk-version: r26d
      add-to-path: false

  - name: Build with r30
    run: ${{ steps.clang-r30.outputs.clang-path }}/bin/clang myapp.c -o myapp-r30

  - name: Build with r26d
    run: ${{ steps.clang-r26d.outputs.clang-path }}/bin/clang myapp.c -o myapp-r26d
```

## Action 输入参数

| 参数          | 说明                                               | 必填 | 默认值 |
| ------------- | -------------------------------------------------- | ---- | ------ |
| `ndk-version` | NDK 版本（例如：r30, r29, r28c, r27d, r26d, r25c） | 是   | -      |
| `add-to-path` | 是否将安装目录的 bin 文件夹添加到 PATH             | 否   | `true` |

## Action 输出参数

| 参数          | 说明                   |
| ------------- | ---------------------- |
| `clang-path`  | Clang 工具链的安装路径 |
| `ndk-version` | 使用的 NDK 版本        |

## 支持的版本

当前支持的 NDK 版本及其对应的 Clang 版本可以在 [`mapping.json`](mapping.json) 文件中查看。

支持的版本包括：

- r30 (clang r574158c)
- r29 (clang r563880c)
- r28c (clang r530567e)
- r27d (clang r522817d)
- r26d (clang r487747e)
- r25c (clang r450784d1)

## 平台支持

- Linux (x64)
- macOS (x64)
- Windows (x64)

## 直接下载（不使用 Action）

你也可以直接从 [GitHub Releases](https://github.com/Ylarod/setup-ndk-clang/releases/tag/prebuilt) 下载预编译工具链。

文件名格式：`clang-{platform}-ndk-{version}-{revision}.tar.zst`

示例：

```bash
# 下载 Linux 版本
wget https://github.com/Ylarod/setup-ndk-clang/releases/download/prebuilt/clang-linux-x86-ndk-r30-r574158c.tar.zst

# 解压
tar -I unzstd -xf clang-linux-x86-ndk-r30-r574158c.tar.zst

# 验证校验和
wget https://github.com/Ylarod/setup-ndk-clang/releases/download/prebuilt/clang-linux-x86-ndk-r30-r574158c.tar.zst.sha256
sha256sum -c clang-linux-x86-ndk-r30-r574158c.tar.zst.sha256
```

## 工作原理

### 预编译工具链

1. 从 AOSP 的 Clang 预编译仓库下载
2. 使用可重现的 tar 和 zstd 压缩重新打包
3. 发布到 GitHub Releases 并附带 SHA256 校验和
4. 当 `mapping.json` 更改时自动更新

### GitHub Action

1. 从 `mapping.json` 获取 NDK 版本到 Clang 版本的映射
2. 下载对应平台的预编译 Clang 工具链
3. 解压并缓存工具链到运行器的工具缓存
4. 可选地添加工具链的 bin 目录到 PATH

## 从源码构建

本仓库包含 GitHub Action 的源代码。构建方法：

```bash
npm install
npm run build
```

编译后的 action 将在 `dist/` 目录中。

## 许可证

Apache License 2.0

## 相关项目

- [setup-ndk](https://github.com/nttld/setup-ndk) - Setup Android NDK Action
- [Android Clang Prebuilts](https://android.googlesource.com/platform/prebuilts/clang/host/) - AOSP Clang 源码
