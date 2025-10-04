### 本地运行

- 先安装依赖（仅首次或依赖变更时执行）
```
# macOS 推荐使用 Homebrew 的 Ruby
export PATH="/opt/homebrew/opt/ruby/bin:$PATH"
# 安装 Bundler（如未安装）
gem install bundler:2.6.9
# 安装项目依赖
bundle install
```
- 启动本地预览（默认 http://127.0.0.1:4000/）
```
export PATH="/opt/homebrew/opt/ruby/bin:$PATH"
bundle exec jekyll serve --host 127.0.0.1 --port 4000 --livereload
```
- 如遇到图片处理报错，需安装 ImageMagick：
```
brew install imagemagick
```

### 修改主页（homepage）

- 主页内容主要来自以下位置：
  - `_pages/about.md`：关于页主体内容（首页通常链接到此页）。
  - `_data/socials.yml`、`_data/repositories.yml`：社交链接、项目/仓库展示。
  - `assets/img/prof_pic*.png`：头像图片（替换同名文件即可）。
  - `_config.yml`：站点标题、描述、主题色、搜索、评论等全局设置（已设置 `url: https://baleyang.github.io`，`baseurl:` 留空）。
- 不要改动（避免破坏主题/部署）：
  - `.github/workflows/` 下的工作流文件（CI/CD 部署）。
  - `_includes/` 与 `_layouts/` 中的主题基础结构（除非清楚影响）。
  - `assets/js/` 与 `_sass/` 中的通用脚本与样式（非必要不改）。

小技巧：
- 文本内容建议优先改 `_pages/`、`_posts/`、`_projects/` 下的 Markdown。
- 图片替换保持文件名不变，可避免额外改动引用路径。

### 推送到 GitHub（部署）

- 检查更改并提交：
```
git status
git add -A
git commit -m "docs: 更新主页内容与说明"
```
- 推送到远端（将触发 GitHub Actions 部署到 gh-pages）：
```
git push origin main
```
- 稍等几分钟，在 Actions 看到构建成功后，访问：
```
https://baleyang.github.io
```

### 常见问题

- 本地 `bundle` 报 Ruby 版本问题：请使用 Homebrew 安装的 Ruby 并导出 PATH。
- 本地启动 404：确认 `_config.yml` 中 `url` 与 `baseurl` 配置已按用户主页设置（`baseurl` 需留空）。
- Jupyter/图片构建失败：安装 `imagemagick` 与 `jupyter`（CI 已自动安装，若本地需要可 `pip3 install jupyter nbconvert`）。

### 导航菜单（上栏）增删与排序

- 隐藏某菜单：打开对应 `_pages/*.md` 文件，将 front matter 设置 `nav: false`（或删除该文件）。例如隐藏 Blog：
```yaml
---
layout: default
permalink: /blog/
title: blog
nav: false
---
```
- 新增菜单：在 `_pages/` 下新建文件，设置 `nav: true`、`nav_order`（越小越靠左）：
```yaml
---
layout: page
permalink: /links/
title: links
nav: true
nav_order: 3
---
页面内容...
```
- 外链菜单：`permalink` 写完整 URL 即可（自动识别外链）：
```yaml
---
layout: none
permalink: https://example.com
title: external
nav: true
nav_order: 9
---
```
- 下拉菜单：用一个带 `dropdown: true` 的页面并声明 `children`（可用 `divider` 分隔）：
```yaml
---
layout: page
title: more
nav: true
nav_order: 8
dropdown: true
children:
  - title: bookshelf
    permalink: /books/
  - title: divider
  - title: blog
    permalink: /blog/
---
```
- 修改显示文案：改相应页面的 `title:`。
- 排序：统一通过各页面的 `nav_order:` 控制，数字越小越靠左。

说明：导航渲染逻辑见 `_includes/header.liquid`，仅会显示 `nav: true` 且非自动生成的页面。

### 各页面/功能编辑详解

- About（首页）
  - 文件：`_pages/about.md`
  - 关键 front matter：`subtitle`、`profile.align`、`profile.image`/`image_circular`、`profile.more_info`、`selected_papers`、`social`、`announcements`、`latest_posts`。
  - 正文：从分隔线 `---` 之后直接使用 Markdown 编辑个人简介。

- Blog（博客）
  - 列表页：`_pages/blog.md` 控制是否在导航显示（`nav`）、排序（`nav_order`）、分页参数（`pagination`）。
  - 文章：`_posts/YYYY-MM-DD-title.md`，常用 front matter：`title`、`description`、`tags`、`categories`、`thumbnail`、`redirect`（外链）。
  - 全局：`_config.yml` 中 `blog_name`、`blog_description`、`display_tags`、`display_categories`。

- Publications（论文）
  - 数据：`_bibliography/papers.bib`（可加 `pdf`, `code`, `website`, `selected`, `preview` 等字段）。
  - 页面：`_pages/publications.md` 可按需调整布局。
  - 配置：`_config.yml` → `scholar` 区块；`enable_publication_badges`、`enable_publication_thumbnails` 开关。

- Projects（项目）
  - 数据：`_projects/*.md`，常用 front matter：`title`、`description`、`img`（卡片背景）、`importance`（排序）、`category`、`related_publications`。
  - 列表页：`_pages/projects.md` 为项目总览页。

- Repositories（GitHub 仓库）
  - 数据：`_data/repositories.yml` 设置 `github_users` 与 `github_repos`。
  - 配置：`_config.yml` → `repo_trophies.enabled`、`repo_theme_light/dark`。
  - 页面：`_pages/repositories.md`。

- News（动态）
  - 数据：`_news/*.md`。
  - 页面：`_pages/news.md`。

- CV（简历）
  - 推荐：`assets/json/resume.json`（JSON Resume 标准）。若无该文件，使用 `_data/cv.yml` 作为回退。
  - 页面：`_pages/cv.md`。

- Profiles/People（多人/团队页，可选）
  - 页面：`_pages/profiles.md`。
  - 相关数据：如需同类“合作者/成员”数据，可参考 `_data/coauthors.yml` 的组织方式。
