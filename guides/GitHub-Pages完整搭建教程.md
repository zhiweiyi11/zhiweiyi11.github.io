# GitHub Pages 个人博客完整搭建教程

适用对象：Windows，已有 Git、VS Code，使用 Obsidian / OneNote，准备记录 AI Infra 学习与生活。核对日期：2026-09-19。

## 1. 先选一条简单、可持续的路线

推荐起步方案：**Markdown + Jekyll + Minima + GitHub Pages 云端构建**。

你写 Markdown → Git 保存版本 → push 上传 GitHub → GitHub 构建网页 → Pages 展示博客。

这里的 Jekyll 在 GitHub 上运行，本机无需安装 Ruby、Bundler、Jekyll，也不需要购买服务器或域名。代价是本机暂时只预览 Markdown，完整网页效果要等云端构建完成。GitHub 支持从分支发布，也支持自定义 Actions；起步先用分支发布，等需要特殊插件时再迁移自定义 Actions。[官方发布源说明](https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site)

静态博客可以持续更新文章和栏目；“静态”指服务器直接提供构建好的页面，并不是内容不能更新。它本身不提供数据库、用户登录或公众号发布后台。

这次暂不采用 Hugo / VuePress：它们可以部署到 Pages，但你现在更需要先打通写作和发布闭环，无须同时学习多个框架。

## 2. 参考文档中需要修正的地方

| 原文说法或做法 | 本次采用的解释 |
| --- | --- |
| 仓库名字只能是 用户名.github.io | 这是用户站点的命名方式；项目站点也可以用普通仓库名，网址通常多一段仓库路径。 |
| Source 选 Master branch | 现在进入 Settings → Pages；分支名按实际设置。本教程使用 main。 |
| 必须安装 Ruby 才能写博客 | 不必。只有采用本地 Jekyll 构建/完整预览时，才需要相应运行环境。 |
| GitHub Pages 基于 Jekyll | Pages 是静态托管服务；内置支持 Jekyll，也可以托管其他工具生成的网页。 |
| Hugo 有 GitHub 官方主题 | Hugo 的主题应到 Hugo 主题生态查找；Pages 负责托管构建产物。 |
| --watch 自动刷新浏览器 | watch 主要负责监测变化并重新生成文件；浏览器实时刷新是另一项功能。 |
| 修改 __config.yml | 正确文件名是 `_config.yml`，只有一个前导下划线。 |

参见 [Pages 类型与工作方式](https://docs.github.com/en/pages/getting-started-with-github-pages/what-is-github-pages) 和 [Pages 与 Jekyll](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/about-github-pages-and-jekyll)。

## 3. 已经为你准备了什么

当前本地目录：

```text
C:\Users\Micha\Documents\ChatGPT\创建个人Blog网站
```

```text
_config.yml              博客名称、地址、主题、导航
index.md                 首页和文章列表
ai-infra.html            自动列出 ai-infra 分类文章
life.html                自动列出 life 分类文章
archives.html            按年份归档
about.md                 关于页面
_posts/                  已准备发布的文章
assets/images/           文章图片
templates/paper-note.md  论文笔记模板，不生成网站页面
guides/                  本教程，不生成网站页面
.gitignore               排除本地配置、构建缓存和草稿
```

主题使用 Minima，自带文章页、代码高亮样式和适配手机的布局；工程启用了 RSS 与 SEO 标签插件。栏目页随文章分类自动更新。模板和教程通过 `exclude` 排除在站点构建之外，但提交到公开仓库后仍可在仓库中阅读。[Minima 配置说明](https://raw.githubusercontent.com/jekyll/minima/2.5-stable/README.md)

配置暂按当前已连接账号 `zhiweiyi11` 填写，作者显示名暂用 Michael。若使用其他账号，请先修改配置和下文仓库地址。**这只是本地起步工程，目前没有上线，也没有完成云端构建验证。**

## 4. 第一次搭建：先把网站发布成功

### 第一步：创建仓库

1. 登录 GitHub，确认头像菜单中的用户名。
2. 打开 https://github.com/new 。
3. Owner 选择你的个人账号。
4. Repository name 填 `zhiweiyi11.github.io`；其他账号必须替换用户名。
5. 选择 **Public**。本教程按 GitHub Free 的公开仓库方案操作。
6. **不要勾选初始化 README、.gitignore、License**，因为本地文件已经准备好。
7. 点击 Create repository。

如果同名仓库已经存在，先查看里面是否已有网站，不要覆盖；把仓库链接发给我，后续按已有仓库处理。用户站点的预期地址是 `https://zhiweiyi11.github.io/`，此时还不代表它已经可访问。[GitHub Pages 快速入门](https://docs.github.com/en/pages/quickstart)

### 第二步：检查博客配置

用 VS Code 打开上述本地文件夹，编辑 `_config.yml`：

```yaml
title: "Michael 的学习笔记"
description: "AI Infra · 论文阅读 · 开源实践 · 生活记录"
author: "Michael"
url: "https://zhiweiyi11.github.io"
github_username: zhiweiyi11
baseurl: ""
```

`url` 是站点域名；用户站点的 `baseurl` 保持空字符串。YAML 使用空格缩进，不要用 Tab。若以后使用名为 blog 的项目仓库，通常改为 `baseurl: "/blog"`；本文图片写法按用户站点设计，迁移到项目站点时也要相应调整。

修改 `about.md` 的自我介绍，按需要修改第一篇示例文章。不要把整个科研笔记库复制进公开仓库，只放准备公开的内容。

### 第三步：第一次提交并上传

在 VS Code 菜单“终端 → 新建终端”中使用 PowerShell，逐行执行。

```powershell
Set-Location -LiteralPath 'C:\Users\Micha\Documents\ChatGPT\创建个人Blog网站'
git status
git remote -v
```

本次检查时，这里已存在一个尚无提交、没有远程地址的 Git 仓库，所以不用再次 `git init`。如果你以后在另一个全新文件夹操作，才先执行 `git init -b main`。

先检查提交身份：

```powershell
git config user.name
git config user.email
```

若没有输出，再设置此仓库的身份；以下两项请换成自己的值，不要照抄示例邮箱：

```powershell
git config user.name "你的显示名"
git config user.email "GitHub设置中显示的noreply邮箱或你已验证的邮箱"
```

noreply 邮箱可在 GitHub Settings → Emails 查看。提交身份用于记录作者，和登录认证是两件事。

确认当前目录只有本教程准备的博客文件后：

```powershell
git add .
git diff --cached --stat
git commit -m "Create personal blog"
git branch -M main
git remote add origin https://github.com/zhiweiyi11/zhiweiyi11.github.io.git
git push -u origin main
```

首次 push 若弹出 Git Credential Manager 登录窗口，按提示在浏览器登录。如果需要命令行 HTTPS 凭据，GitHub 账户密码不能代替访问令牌；也可以使用已配置的 SSH。不要把密码、Token 发到聊天中。此时 Codex 已连接 GitHub，并不保证本机 Git 已登录。[GitHub 认证说明](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/about-authentication-to-github)

这些命令适用于上一步新建的**空仓库**。若 `origin already exists`，先运行 `git remote -v` 核对，不要盲目重设。若 push 提示远程有本地没有的提交，停止并保留错误信息，不使用强制推送。

### 第四步：启用 Pages

回到 GitHub 仓库页面：

1. Settings → 左侧 Pages。
2. Build and deployment → Source 选择 **Deploy from a branch**。
3. Branch 选择 **main**。
4. Folder 选择 **/(root)**。
5. 点击 Save。

本工程使用 GitHub 内置 Jekyll 构建，因此此处不要选一个尚未配置工作流的 GitHub Actions，也不要新建 `.nojekyll` 文件。虽然选择分支发布，构建部署记录仍会出现在 Actions 中。

### 第五步：验收网站

打开仓库 Actions，查看 Pages 的构建和部署记录。首次发布可能需要数分钟，不要把尚在运行当作失败。

成功后从 Settings → Pages 提供的站点链接打开，并核对：

- 首页能打开，能看到“从这里开始：我的学习博客”。
- 点击文章可以进入正文，返回首页正常。
- AI Infra、生活记录、归档、关于四个导航可以打开。
- AI Infra 暂时显示空状态，因为论文模板还没有发布。
- 生活记录中有示例文章。
- 在手机上打开检查阅读和导航。
- `/feed.xml` 能显示 RSS 内容。

至此才算第一阶段完成。如果失败，把 Actions 失败步骤的报错文本发给我，我会和你一起定位。

## 5. 日常写文章与同步更新

### 创建第一篇 AI Infra 笔记

复制 `templates/paper-note.md` 到 `_posts/`，例如命名：

```text
2026-09-20-kv-cache-reading.md
```

文件名必须是 `年-月-日-英文短标题.md`。文章开头保留 YAML 信息，并将日期改为实际发布日期：

```yaml
---
layout: post
title: "KV Cache：我的阅读笔记"
date: 2026-09-20 10:00:00 +0800
categories: [ai-infra]
tags: [论文阅读, 推理优化, KVCache]
---
```

再在下方编写 Markdown。标题允许中文；英文文件名主要方便分享网址。日期在未来的文章默认不会显示，因此不要直接使用尚未到来的示例日期。分类填写 `ai-infra` 会进入技术栏目，`life` 会进入生活栏目。分类和标签用于组织内容，本工程没有额外制作标签列表页面。[Jekyll 文章规范](https://jekyllrb.com/docs/posts/)

### 插入图片

把图片保存为 `assets/images/kv-cache.png`，文中写：

```markdown
![KV Cache 示意图](/assets/images/kv-cache.png)
```

这是站点根目录路径，适合本教程的用户站点。不能写 `D:\...`，否则读者电脑无法访问。文件名大小写必须与图片一致。图片要和文章一起提交；大量原始旅拍不适合直接放入 Git，先缩小尺寸、压缩，再放入正文需要的照片。

### 预览与发布

VS Code 的 `Ctrl+Shift+V` 可以预览 Markdown 正文，但它不会执行 Jekyll 模板，也不保证根路径图片在编辑器里显示。不要把 Live Server 当成 Jekyll 构建工具。

开始一轮编辑前，如果曾在 GitHub 网页或其他电脑修改过内容，先在工作区干净时执行：

```powershell
git pull --ff-only
```

完成编辑后按实际文件路径提交，例如：

```powershell
git status
git add _posts/2026-09-20-kv-cache-reading.md
git add assets/images/kv-cache.png
git diff --cached
git commit -m "Add KV Cache reading note"
git push
```

未添加图片时跳过对应的 `git add`。push 后 GitHub 会再次构建发布，无须重新设置 Pages。

如果 pull 拒绝合并，或你已有未提交的修改，先不要继续套命令，保留当前工作并把 `git status` 与报错发给我。修改旧文章也遵循同样流程；已经分享过的文章尽量不要更改日期或文件名，以免网址变化。

## 6. 与 Obsidian、OneNote 配合

推荐把“私人研究笔记”和“公开博客”分开：平时在原笔记库收集，准备发表时整理成独立 Markdown，复制必要图片到博客工程。也可以把博客文件夹单独打开为 Obsidian 库；`.obsidian/` 已加入忽略规则。

Obsidian 的 `[[双链]]` 和 `![[图片.png]]` 不是通用 Markdown 写法。可在 Settings → Files and links 关闭 Use Wikilinks，但关闭设置不会自动转换历史内容。[Obsidian 官方说明](https://obsidian.md/help/links)

发布前逐项处理：

- `![[图片.png]]` 改成前文的标准图片链接，并复制图片文件。
- `[[另一篇笔记]]` 改成已发布文章的网址或 Jekyll 文章链接；不要只指向私人库文件。
- Dataview、笔记嵌入、块引用链接等功能需要转换成普通正文。
- Callout 先改成普通引用块；Mermaid、数学公式需要另外配置网页渲染，当前起步工程没有接入，不能假定直接可用。
- OneNote 内容先复制、整理成 Markdown，并单独保存图片。当前工程没有 OneNote 自动导出或同步组件。

草稿可放在本地 `_drafts/`，本工程的 Git 忽略规则默认不上传它。不要强制添加不准备公开的草稿；“Jekyll 不渲染草稿”不等于“公开仓库看不到草稿”。

## 7. 新增栏目，不必重建网站

目前先用两个大类：`ai-infra` 与 `life`，再用标签区分论文、开源、读书、旅行、健身。

需要独立“读书”栏目时：

1. 复制 `life.html` 为 `reading.html`。
2. 修改页头为 `title: 读书`、`permalink: /reading/`。
3. 把筛选条件 `post.categories contains 'life'` 改成 `post.categories contains 'reading'`。
4. 在 `_config.yml` 的 `header_pages` 列表增加 `- reading.html`。
5. 新文章使用 `categories: [life, reading]`，让它同时进入生活和读书两个栏目。
6. 提交这些改动并 push。

后续可按实际需求增加搜索、数学公式、评论、独立域名和更丰富的主题。评论等动态能力需要外部服务，不是 Pages 自带数据库。

## 8. 微信公众号和知乎：分发如何安排

要区分两种同步：

| 同步目标 | 当前方案 |
| --- | --- |
| 本地文章 → GitHub 博客 | Git push 后自动构建发布，首次启用 Pages 后即可使用。 |
| 博客 → 微信公众号 / 知乎 | 当前工程未实现，不会因为 push 而自动发布。 |

建议先采用一个 Markdown 主稿、多平台适配：完成博客发布后，把正文转成目标编辑器适合的内容，检查图片、代码、公式和引用，再分别发布并记录链接。将本地图片路径改为目标平台上传后的图片；平台间样式通常需要调整。

第二阶段再选分发工具。应先核验工具对你的实际账号、目标平台和草稿功能的支持，再决定是否接入；不要预设所有账号都能通过官方 API 全自动群发，也不要把登录 Cookie 当成长期稳定接口。此阶段目标是“一份原稿，减少重复排版”，之后再评估自动化。

## 9. 常见问题

| 现象 | 先检查什么 |
| --- | --- |
| 网站 404 | 仓库名、Pages 开关、main 分支、root 目录、首次构建是否完成。 |
| Actions 红色失败 | 打开失败步骤；优先看 YAML 缩进、Liquid 语法、主题和插件错误。 |
| 首页出现仓库说明而不是博客 | `index.md` 是否位于发布根目录，Pages 是否选错目录。 |
| 文章不出现 | `_posts` 路径、日期文件名、Front Matter、未来日期、是否设了 `published: false`。 |
| 文章没进栏目 | 分类是否准确写成 `ai-infra` 或 `life`，大小写是否一致。 |
| 图片不显示 | 是否提交图片、大小写、根路径、是否残留 Obsidian 嵌入语法。 |
| 样式丢失 | Pages 构建是否成功，`baseurl` 是否正确，是否使用了与主题版本不符的配置。 |
| push 身份验证失败 | 确认 Git 登录账号和仓库权限；不要使用 GitHub 密码替代 Token。 |
| push 提示 non-fast-forward | 远程已有不同提交；先保留修改并诊断，不要 force push。 |
| 修改后看不到更新 | 检查对应提交的部署状态，再刷新浏览器；不要只看旧的绿色记录。 |

## 10. 现在一起操作的检查点

第一轮只完成三件事：确认 GitHub 账号 → 创建空的公开仓库 → 告诉我仓库链接。

我已准备好本地源码；下一轮可以针对你的实际仓库继续检查配置、提交上传，并一起确认 Pages 第一次部署成功。完成后，我们再用你的一篇真实 AI Infra 笔记演练“整理文章 → 图片处理 → 分类 → 发布 → 修改更新”。

验收标准不是只创建了仓库，而是：公开网址可读、文章链接有效、新增一篇 Markdown 后能自动更新网站。
