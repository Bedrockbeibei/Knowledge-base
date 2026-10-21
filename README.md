# 沉潜知识库

单文件 Neo-Brutalism 个人知识库（`index.html`）：文件夹树、文章阅读器、置顶、任务清单、本地导入（文件/文件夹），以及 GitHub 登录后的作者专属编辑与仓库同步。

## 一、部署到 GitHub Pages

1. 新建仓库（public），把 `index.html` 和 `.nojekyll` 上传到仓库根目录（README.md 可传可不传）。
2. 仓库 **Settings → Pages**，Source 选 `Deploy from a branch`，分支 `main`、目录 `/ (root)`，保存。
3. 等 1~2 分钟，访问 `https://<你的用户名>.github.io/<仓库名>/`。

> 页面完全自包含（Tailwind 已内联），无需构建、无需安装依赖。

> **打开是 404「There isn't a GitHub Pages site here」？** 说明 Pages 没开：进仓库 Settings → Pages，按上面第 2 步设置并保存，等 1~2 分钟再刷新。开了 Pages 仍 404，检查根目录有没有 `index.html`（必须叫这个名字），以及分支选的是不是 `main`。

## 二、开通 GitHub 登录（作者功能）

1. 打开 <https://github.com/settings/developers> → **New OAuth App**
   - Application name：随便填，如 `沉潜知识库`
   - Homepage URL：`https://<用户名>.github.io/<仓库名>/`
   - **Authorization callback URL**：`https://<用户名>.github.io/<仓库名>/`（必须完全一致，含结尾斜杠）
2. 创建后复制 **Client ID**。
3. 打开网站，点右上角 **GitHub 登录** → 自动弹出设置窗：
   - OAuth Client ID：粘贴上一步的 Client ID
   - 你的 GitHub 用户名（作者标识）：填你自己的登录名，**只有这个账号登录才会解锁编辑与同步**
   - 仓库所有者 / 仓库名 / 分支 / 仓库内存储目录（默认 `knowledge-base`）
   - 保存后再点「GitHub 登录」完成授权。

> 本地用 `file://` 双击打开时 OAuth 不可用（浏览器限制），可在设置里粘贴 **Personal Access Token**（勾选 `repo` 权限）直接连接。

## 三、作者功能说明

登录为作者后：

- 文章页右上出现 **编辑文章**，可直接改标题与 HTML 正文（内置文章的修改保存在浏览器本地）。
- 文件夹 / 文章面板出现 **☁ 上传**，把内容推到仓库。
- 工具栏出现 **同步到 GitHub**，一次同步全部文章。

### 仓库内路径规则

与知识库目录结构一一对应，不会全部堆在一个目录里：

```
knowledge-base/
├── 单片机与硬件/
│   ├── STC89C52 点亮第一个 LED：从接线到烧录.html
│   ├── 红外遥控器解码：NEC 协议时序图解.html
│   └── 开发环境/
│       ├── Keil μVision 新建工程最简流程.html
│       └── VS Code + PlatformIO 也能玩 51？环境搭建记录.html
├── 课程与数学/
│   └── ...
```

文件名取文章标题，非法字符（`\ / : * ? " < > |`）替换为 `_`；内容是带基础样式的独立 HTML，可在浏览器直接打开。

## 四、本地数据

数据分两层存放（均按浏览器 + 域名/文件路径隔离）：

**IndexedDB**（库名 `cqkb-db`，存放大块内容，配额随磁盘空间，通常几百 MB 起步）：

| 键 | 内容 |
| --- | --- |
| `custom` | 自定义文件夹 / 文章 / 目录结构 / 已删除的内置项（含内嵌的文件数据） |
| `edits` | 内置文章的编辑覆盖 |

**localStorage**（存放小配置）：

| 键 | 内容 |
| --- | --- |
| `cqkb-titles` | 标题覆盖 |
| `cqkb-pins` | 置顶（最多 3 篇） |
| `cqkb-tasks` | 任务清单 |
| `cqkb-gh-config` / `cqkb-gh-token` / `cqkb-gh-user` | GitHub 配置与登录态 |

- 单文件上限 **30MB**；导入前会按 `navigator.storage.estimate()` 预检磁盘余量，不足会自动跳过并提示。
- 从旧版本升级时，`cqkb-custom` / `cqkb-edits` 会在首次打开时自动迁入 IndexedDB 并清除旧键，无需手动操作。
- IndexedDB 不可用（极少数隐私模式）时自动退回 localStorage，此时仍受约 5MB 限制。
- 换设备或清缓存会丢失本地内容，重要内容建议同步到仓库做备份。令牌保存在 localStorage，公共电脑上用完请「退出登录」。
