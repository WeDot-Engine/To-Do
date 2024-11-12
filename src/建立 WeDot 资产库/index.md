# 建立 WeDot 资产库

## 进度

- [ ] 建立资产库
  - [ ] 确定资产注册仓库结构
  - [ ] 资产注册信息编译成索引
  - [ ] 资产注册信息编译成网页
  - [ ] 同步 Godot 资产（可选）
  - [ ] 网页前端
  - [ ] 引擎 API 重写
  - [ ] 引擎 Markdown 解析支持（可选）

## 可行性分析

我们缺少资金来源和人员维护，我们最好能采用静态的方案。

使用静态方案的确可以实现在线浏览搜索下载、编辑器内资产商店浏览搜索下载；
但是难以做到在线资产注册。

## 方案

- 存储：
  - 用户提交合并请求到资产注册仓库。
- 网页：
  - 纯静态前端。
- 引擎商店：
  - 不太有办法兼容 API 了，顶多 `POST` 改 `GET`，网页返回 `JSON` 字符串后引擎分析。
- 注册：
  - 让用户手动以格式提交提交合并请求到资产注册仓库。
  - 网页或引擎辅助编译格式提交合并请求到资产注册仓库。

## [Godot Asset Library](https://github.com/godotengine/godot-asset-library) 分析

- 依赖：
  - **服务器依赖**：Godot 社区和开发者用 PHP 和 HTML + CSS 编写了 Godot Asset Library 的前后端。
  - **数据库依赖**：资产的托管地址和其它基本信息使用数据库存储。
- 状态：
  - **维护**：`This asset library backend and frontend is now in maintenance mode. Feel free to submit bug fixes and small improvements, but please refrain from working on large features. In the future, the Godot Foundation's asset store will deprecate this library.`

### Godot 资产信息模板

- Asset Name：
  - 你资产名称。应该是你资产的特有描述性标题。
- Category：
  - 你的资产所属的类别，将显示在搜索结果中。该类别分为 Addons（插件）和 Projects（项目）。在编辑器中，项目类型（模板、演示、项目）- 的资产将显示在项目管理器的 AssetLib 中，而插件类型的资源仅显示在项目内部中。
- Godot version：
  - 该资产适用的引擎版本。目前，无法让一个资产条目包含多个引擎版本的下载，所以你可能需要多次提交资产，为其支持的每个 Godot 版本添加一- 个条目。在处理主要的引擎版本时，这一点尤其重要，如 `Godot 2.x` 和 `Godot 3.x`。
- Version：
  - 资产的版本号。虽然你可以自由选择和使用你喜欢的任何版本控制方案，但如果你希望资产的版本控制方案清晰且一致，你可能需要查看诸如 - SemVer 之类的内容。请注意，系统内还有一个内部版本号，每次更改或更新资源下载地址时都会增加。
- Repository host：
  - 上传到 AssetLib 的资产并不直接托管在它上面。而是指向 GitHub、GitLab、Bitbucket 等第三方 Git 提供商托管的仓库。这就是你选择- 资产所用提供商的地方，这样网站就能据此计算出最终的下载链接。
- Repository URL：
  - 资产文件/网页的 URL。这将根据你选择的提供商而有所不同，但结构类似于 `https://github.com/<用户>/<项目>`。
- Issues URL：
  - 资源问题跟踪器的 URL。同样，根据托管仓库的不同而不同，但结构类似于 `https://github.com/<用户>/<项目>/issues`。如果你使用提- 供商的问题跟踪器，则可以将此字段留空，因为它是仓库的一部分。
- Download Commit：
  - 该资产的提交号。例如，`b1d3172f89b86e52465a74f63a74ac84c491d3e1`。网站以此计算出实际的下载 URL。
- Icon URL：
  - 资产图标的 URL（将在 AssetLib 搜索结果和资产页面中用作缩略图）。应该是 PNG 或 JPG 格式的图像。
  - 图标必须为正方形（纵横比为 1:1），最低分辨率为 128x128 像素。
  - > 如果是托管在 GitHub 上的图标，URL 必须是 `https://raw.githubusercontent.com/<用户>/<项目>/<分支>/Icon.png` 的形- 式。
- License：
  - 分发资产所使用的许可协议。列表中包含了 GPL（v2 和 v3）、MIT、BSD、Boost 等各种自由开源软件许可协议。访问 OpenSource.org 就- 可以获取此处列出协议的详细说明。
- Description：
  - 最后，你可以使用 Description 字段来查看资源，其功能和行为，更改日志等文本概述。将来，将支持使用 Markdown 格式，但目前只支持纯- 文本。
  - > 你还可以包含最多三个视频和/或图像预览，这些预览将显示在资源页面的底部。请使用每个预览提交框上的“启用”复选框从而启用它们。
- Type：
  - 图像或视频。
- Image/YouTube URL：
  - 图像或托管在 YouTube 上的视频的链接。
- Thumbnail URL：
  - 图片的 URL，这张图片将用作预览的缩略图。这个选项最终将被删除，缩略图将被自动计算出来。

### Asset library API

All POST APIs accept JSON-encoded or formdata-encoded bodies.
GET APIs accept standard query strings.

#### Core

The core part of the API is understood and used by the C++ frontend embedded in the Godot editor. It has to stay compatible with all versions of Godot.

- [`GET /configure`](#api-get-configure) - get details such as category and login URL
- [`GET /asset?…`](#api-get-asset) - list assets by filter
- [`GET /asset/{id}`](#api-get-asset-id) - get asset by id

#### Auth API

<div id="api-post-register"></div>

##### `POST /register`
```json
{
  "username": "(username)",
  "password": "(password)",
  "email": "(email)"
}
```
Successful result:
```json
{
  "username": "(username)",
  "registered": true
}
```


Register a user, given a username, password, and email.

<div id="api-post-login"></div>

##### `POST /login`
```json
{
  "username": "(username)",
  "password": "(password)"
}
```
Successful result:
```json
{
  "authenticated": true,
  "username": "(username)",
  "token": "(token)"
}
```

Login as a given user. Results in a token which can be used for authenticated requests.

<div id="api-post-logout"></div>

##### `POST /logout`
```json
{
  "token": "(token)"
}
```
Successful result:
```json
{
  "authenticated": false,
  "token": ""
}
```

Logout a user, given a token. The token is invalidated in the process.


<div id="api-post-change-password"></div>

##### `POST /change_password`
```json
{
  "token": "(token)",
  "old_password": "(password)",
  "new_password": "(new password)"
}
```
Successful result:
```json
{
  "token": ""
}
```

Change a user's password. The token is invalidated in the process.


<div id="api-get-configure"></div>

##### `GET /configure`
```http
?type=(any|addon|project)
&session
```
Example result:
```json
{
  "categories": [
    {
      "id": "1",
      "name": "2D Tools",
      "type": "0"
    },
    {
      "id": "2",
      "name": "Templates",
      "type": "1"
    },
  ],
  "token": "…",
  "login_url": "https://…"
}
```

Get a list of categories (needed for filtering assets) and potentially a login URL which can be given to the user in order to authenticate him in the engine (currently unused and not working).

#### Assets API

<div id="api-get-asset"></div>

##### `GET /asset?…`
```http
?type=(any|addon|project)
&category=(category id)
&support=(official|featured|community|testing)
&filter=(search text)
&user=(submitter username)
&cost=(license)
&godot_version=(major).(minor).(patch)
&max_results=(number 1…500)
&page=(number, pages to skip) OR &offset=(number, rows to skip)
&sort=(rating|cost|name|updated)
&reverse
```
Example response:
```json
{
  "result": [
    {
      "asset_id": "1",
      "title": "Snake",
      "author": "test",
      "author_id": "1",
      "category": "2D Tools",
      "category_id": "1",
      "godot_version": "2.1",
      "rating": "0",
      "cost": "GPLv3",
      "support_level": "testing",
      "icon_url": "https://….png",
      "version": "1",
      "version_string": "alpha",
      "modify_date": "2018-08-21 15:49:00"
    }
  ],
  "page": 0,
  "pages": 0,
  "page_length": 10,
  "total_items": 1
}
```

Get a list of assets.

Some notes:
- Leading and trailing whitespace in `filter` is trimmed on the server side.
- For legacy purposes, not supplying godot version would list only 2.1 assets, while not supplying type would list only addons.
- To specify multiple support levels, join them with `+`, e.g. `support=featured+community`.
- Godot version can be specified as you see fit, for example, `godot_version=3.1` or `godot_version=3.1.5`. Currently, the patch version is disregarded, but may be honored in the future.

<div id="api-get-asset-id"></div>

##### `GET /asset/{id}`
No query params.
Example result:
```json
{
  "asset_id": "1",
  "type": "addon",
  "title": "Snake",
  "author": "test",
  "author_id": "1",
  "version": "1",
  "version_string": "alpha",
  "category": "2D Tools",
  "category_id": "1",
  "godot_version": "2.1",
  "rating": "0",
  "cost": "GPLv3",
  "description": "Lorem ipsum…",
  "support_level": "testing",
  "download_provider": "GitHub",
  "download_commit": "master",
  "download_hash": "(sha256 hash of the downloaded zip)",
  "browse_url": "https://github.com/…",
  "issues_url": "https://github.com/…/issues",
  "icon_url": "https://….png",
  "searchable": "1",
  "modify_date": "2018-08-21 15:49:00",
  "download_url": "https://github.com/…/archive/master.zip",
  "previews": [
    {
      "preview_id": "1",
      "type": "video",
      "link": "https://www.youtube.com/watch?v=…",
      "thumbnail": "https://img.youtube.com/vi/…/default.jpg"
    },
    {
      "preview_id": "2",
      "type": "image",
      "link": "https://….png",
      "thumbnail": "https://….png"
    }
  ]
}
```

Notes:
- The `cost` field is the license. Other asset libraries may put the price there and supply a download URL which requires authentication.
- In the official asset library, the `download_hash` field is always empty and is kept for compatibility only. The editor will skip hash checks if `download_hash` is an empty string. Third-party asset libraries may specify a SHA-256 hash to be used by the editor to verify the download integrity.
- The download URL is generated based on the download commit and the browse URL.

<div id="api-post-asset-id-delete"></div>

##### `POST /asset/{id}/delete`
```json
{
  "token": "…"
}
```
Successful response:
```json
{
  "changed": true
}
```

Soft-delete an asset. Useable by moderators and the owner of the asset.

<div id="api-post-asset-id-undelete"></div>

##### `POST /asset/{id}/undelete`
```json
{
  "token": "…"
}
```
Successful response:
```json
{
  "changed": true
}
```

Revert a deletion of an asset. Useable by moderators and the owner of the asset.

<div id="api-post-asset-id-support-level"></div>

##### `POST /asset/{id}/support_level`
```json
{
  "support_level": "official|featured|community|testing",
  "token": "…"
}
```
Successful response:
```json
{
  "changed": true
}
```

API used by moderators to change the support level of an asset.

#### Asset edits API

<div id="api-post-asset-post-asset-id-post-asset-edit-id"></div>

##### `POST /asset`, `POST /asset/{id}`, `POST /asset/edit/{id}`
```json
{
  "token": "…",

  "title": "Snake",
  "description": "Lorem ipsum…",
  "category_id": "1",
  "godot_version": "2.1",
  "version_string": "alpha",
  "cost": "GPLv3",
  "download_provider": "GitHub",
  "download_commit": "master",
  "browse_url": "https://github.com/…",
  "issues_url": "https://github.com/…/issues",
  "icon_url": "https://….png",
  "download_url": "https://github.com/…/archive/master.zip",
  "previews": [
    {
      "enabled": true,
      "operation": "insert",
      "type": "image|video",
      "link": "…",
      "thumbnail": "…"
    },
    {
      "enabled": true,
      "operation": "update",
      "edit_preview_id": "…",
      "type": "image|video",
      "link": "…",
      "thumbnail": "…"
    },
    {
      "enabled": true,
      "operation": "delete",
      "edit_preview_id": "…"
    },
  ]
}
```
Successful result:
```json
{
  "id": "(id of the asset edit)"
}
```

Create a new edit or update an existing one. Fields are required when creating a new asset, and are optional otherwise. Same for previews -- required when creating a new preview, may be missing if updating one.

Notes:
- Not passing `"enabled": true` for previews will result in them not being included in the edit. This may be fixed in the future.
- `version_string` is free-form text, but `major.minor` or `major.minor.patch` format is best.
- Available download providers can be seen on the asset library fronted.

<div id="api-get-asset-edit-id"></div>

##### `GET /asset/edit/{id}`
No query params.
Example result:
```json
{
  "edit_id": "1",
  "asset_id": "1",
  "user_id": "1",
  "title": null,
  "description": null,
  "category_id": null,
  "godot_version": null,
  "version_string": null,
  "cost": null,
  "download_provider": null,
  "download_commit": null,
  "browse_url": "…",
  "issues_url": "…",
  "icon_url": null,
  "download_url": "…",
  "author": "test",
  "previews": [
    {
      "operation": "insert",
      "edit_preview_id": "60",
      "preview_id": null,
      "type": "image",
      "link": "…",
      "thumbnail": "…",
    },
    {
      "preview_id": "35",
      "type": "image",
      "link": "…",
      "thumbnail": "…"
    }
  ],
  "original": {
    … original asset fields …
  },
  "status": "new",
  "reason": "",
  "warning": "…"
}
```

Returns a previously-submitted asset edit. All fields with `null` are unchanged, and will stay the same as in the `original`.
The `previews` array is merged from the new and original previews.

<div id="api-post-asset-edit-id-review"></div>

##### `POST /asset/edit/{id}/review`
```json
{
  "token": "…"
}
```
Successful result: the asset edit, without the original asset.

Moderator-only. Put an edit in review. It is impossible to change it after this.

<div id="api-post-asset-edit-id-accept"></div>

##### `POST /asset/edit/{id}/accept`
```json
{
  "token": "…",
}
```
Successful result: the asset edit, without the original asset.

Moderator-only. Apply an edit previously put in review.

<div id="api-post-asset-edit-id-reject"></div>

##### `POST /asset/edit/{id}/reject`
```json
{
  "token": "…",
  "reason": "…"
}
```
Successful result: the asset edit, without the original asset.

Moderator-only. Reject an edit previously put in review.