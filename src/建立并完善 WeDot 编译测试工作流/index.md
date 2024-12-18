# 建立并完善 WeDot 编译测试工作流

## 进度

- [X] 测试工作流
  - [X] 环境配置
  - [X] 语法检查（可选）
  - [X] Android 编译
  - [X] iOS 编译
  - [X] Linux 编译
  - [X] macOS 编译
  - [X] Windows 编译
  - [X] Web 编译
- [ ] 编译发行工作流
  - [ ] 环境配置
  - [ ] 编译发行
  - [ ] 推动版本标签自动发布发行（可选）
  - [ ] 发布发行时自动触发网站、文档等发布发行（可选）
  - [ ] 发行到 Steam（可选）
  - [ ] 发行到 F-Droid（可选）
  - [ ] 发行到 itch.io（可选）
- [ ] 上游同步（可选）
  - [ ] 检查上游的提交并转发到 WeDot 仓库（可选）

## 方案

使用 GitHub Actions：

- 测试 WeDot 在不同平台下的编译。
- 编译 WeDot 的发行版本。

## Godot 测试及编译工作流分析

[**当对 `godotengine/godot` 仓库提交修改时**](https://github.com/godotengine/godot/blob/master/.github/workflows/runner.yml)：

- [语法检查](https://github.com/godotengine/godot/blob/master/.github/workflows/static_checks.yml)：仅检查本次提交修改的文件。尽量先执行语法检查，这样在语法检查不过关时跳过费资源的编译测试。
- [Android 编译](https://github.com/godotengine/godot/blob/master/.github/workflows/android_builds.yml)：
  - 支持多个目标平台编译，包括编辑器和不同架构的模板。
  - 缓存机制用于加速构建过程。
  - 设置 Java 和 Python 环境以支持构建需求。
  - 下载并解压预编译的 Android Swappy 帧率优化库。
  - 根据不同的目标执行相应的编译命令。
  - 生成 Godot 模板和编辑器，并上传构建产物作为 artifacts。
- [iOS 编译](https://github.com/godotengine/godot/blob/master/.github/workflows/ios_builds.yml)：
  - 在 macOS 最新版本上进行编译。
  - 缓存机制用于加速构建过程。
  - 设置 Python 和 SCons 环境以支持构建需求。
  - 执行 arm64 架构的编译任务。
  - 生成 Godot 模板，并上传构建产物作为 artifacts。
- [Linux 编译](https://github.com/godotengine/godot/blob/master/.github/workflows/linux_builds.yml)：
  - 支持多种构建配置，包括编辑器和不同类型的模板。
  - 缓存机制用于加速构建过程。
  - 设置 Python 和 SCons 环境以支持构建需求。
  - 安装必要的依赖项，如 Mesa Vulkan 驱动和 Wayland 扫描器。
  - 释放构建机器上的磁盘空间以确保有足够的空间进行构建。
  - 执行编译任务，支持多种编译选项，如启用 Mono、使用不同的编译器和链接器、启用调试符号等。
  - 生成 C# 绑定代码并构建 .NET 解决方案。
  - 进行单元测试、.NET 源代码生成器测试、文档更新检查、API 兼容性检查等。
  - 测试 Godot 项目和项目转换器。
  - 上传构建产物作为 artifacts。
- [macOS 编译](https://github.com/godotengine/godot/blob/master/.github/workflows/macos_builds.yml)：
  - 在最新版本的 macOS 上进行编译。
  - 缓存机制用于加速构建过程。
  - 设置 Python 和 SCons 环境以支持构建需求。
  - 安装 Vulkan SDK 以支持相关功能。
  - 分别编译 x86_64 和 arm64 架构的二进制文件。
  - 使用 `lipo` 工具将两个架构的二进制文件合并为通用二进制文件。
  - 生成 Godot 编辑器和模板，并上传构建产物作为 artifacts。
  - 执行单元测试以验证构建的正确性和稳定性。
- [Windows 编译](https://github.com/godotengine/godot/blob/master/.github/workflows/windows_builds.yml)：
  - 在最新版本的 Windows 10 上进行编译。
  - 缓存机制用于加速构建过程。
  - 设置 Python 和 SCons 环境以支持构建需求。
  - 下载并安装 Direct3D 12 SDK 组件。
  - 下载预编译的 ANGLE 静态库并解压到工作目录。
  - 根据编译器类型设置问题匹配器（MSVC 或 GCC）。
  - 执行编译任务，支持多种编译配置，如编辑器、模板、使用不同的编译器（MSVC、Clang、GCC）等。
  - 清理不必要的编译产物（如 `.exp`, `.lib`, `.pdb` 文件）。
  - 生成 Godot 编辑器和模板，并上传构建产物作为 artifacts。
  - 执行单元测试以验证构建的正确性和稳定性。
- [Web 编译](https://github.com/godotengine/godot/blob/master/.github/workflows/web_builds.yml)：
  - 在 Ubuntu 22.04 上进行 Web 平台的编译。
  - 缓存机制用于加速构建过程。
  - 设置 Emscripten 环境以支持 WebAssembly 编译。
  - 验证 Emscripten 环境是否正确安装。
  - 恢复 Godot 构建缓存以加快编译速度。
  - 设置 Python 和 SCons 环境以支持构建需求。
  - 执行编译任务，支持多线程和单线程两种配置。
  - 保存 Godot 构建缓存以便后续使用。
  - 生成 Web 平台的模板，并上传构建产物作为 artifacts。
