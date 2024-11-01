# 建立 WeDot-doc

## 进度

- [X] Godot-docs
  - [X] 格式转换
- [ ] mdbook
  - [X] 翻译
  - [X] Pjax
  - [ ] 翻译提示
  - [ ] 版本控制
- [ ] WeDot-docs
  - [ ] 编译发行
  - [ ] 引擎对接

## 方案

`Wedot-Engine/WeDot-docs` 采用 mdbook 生成文档并发布到网页。
`Wedot-Engine/WeDot-docs` 文档采用 markdown 编写。

仓库同时托管在 `Wedot-Engine/WeDot-docs` 和以 git module 的方式存在 `Wedot-Engine/WeDot` 内。

### 工作流

**开发者->持续集成->文档**：
开发者在对引擎的贡献时同步向 git module 提交文档更改。
git module 更改由持续集成提交到 `Wedot-Engine/WeDot-docs`。

**社区维护者->文档和教程**：
社区维护者直接向 `Wedot-Engine/WeDot-docs` 提交文档更改。

**持续集成->合并**：
当提交被合并前，持续集成检查文档是否存在语法错误。

**持续集成->发行**：
每周使用 mdbook 编译并部署。

**引擎编译**：
当引擎编译时，引擎直接读取 markdown 文档，这样能直接获得脱离文档网站的完整离线文档；
或在引擎编译前用脚本将文档转换为 xml 格式。

## Godot-doc 分析

Godot-doc 使用基于 Python 的文档生成工具 Sphinx 生成文档。
Godot-doc 使用 restructuredtext 编写。

### Godot-doc 工作流

**开发者->引擎->持续集成->文档**：
在开发者完成对引擎中`类`相关的 `doc/classes/` 内 xml 文档贡献后，每个星期六晚上（欧盟时间），
持续集成使用脚本将引擎的 xml 文档转换为 .rst 文档向 `godotengine/godot-docs` 提交合并请求。

**社区维护者->文档和教程**：
社区维护者直接向 `godotengine/godot-docs` 提交文档更改。

**持续集成->合并**：
当提交被合并前，持续集成检查文档是否存在语法错误。

**持续集成->发行**：
每个星期一（UTC），持续集成使用 Sphinx 将文档编译成网页部署，用其他脚本编译 epub 发行。
