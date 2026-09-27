# cjx132 博客使用指南

## 环境信息

- 博客目录: `/Users/cjx/Documents/cjx132-blog`
- Hexo: v7.1.1 + NexT 主题 v8.29.0 (Gemini 方案)
- Node.js: v20.17.0

## GitHub 仓库

| 仓库 | 用途 |
|------|------|
| `cjx132/cjx132` | GitHub Pages 静态站点 (hexo deploy 推送) |
| `cjx132/picture-bed` | 图床 (167+张图片, 已开启 Pages) |
| `cjx132/cjx132-blog-source` | Hexo 源码备份 (含317篇文章 + 191张内容图片) |

## 换电脑迁移步骤

### 1. 安装 Node.js (v20+)

从 https://nodejs.org 下载，或 `brew install node`

### 2. 配置 GitHub 认证

去 https://github.com/settings/tokens 创建 Personal Access Token（勾选 `repo` 权限），然后：

```bash
git config --global user.name "cjx132"
git config --global user.email "cjx132@users.noreply.github.com"
printf "protocol=https\nhost=github.com\nusername=cjx132\npassword=YOUR_TOKEN\n" | git credential approve
```

### 3. 克隆博客源码 + 主题 + 图床

```bash
# 博客源码（含317篇文章、191张内容图片、面试指南）
git clone https://github.com/cjx132/cjx132-blog-source.git ~/Documents/cjx132-blog

# NexT 主题（必须，源码仓库不含主题文件）
cd ~/Documents/cjx132-blog
git clone https://github.com/next-theme/hexo-theme-next.git themes/next

# 图床（如果需要管理博客截图）
git clone https://github.com/cjx132/picture-bed.git ~/Documents/picture-bed

# 安装依赖
cd ~/Documents/cjx132-blog
npm install
```

### 4. 验证

```bash
hexo server  # 打开 http://localhost:4000/cjx132/ 预览
```

## 日常使用

### 写新文章

```bash
cd ~/Documents/cjx132-blog
hexo new "文章标题"
# 编辑 source/_posts/文章标题.md
```

### 上传图片到图床

```bash
# 把图片复制到图床仓库，然后推送
cp /path/to/image.png ~/Documents/picture-bed/
cd ~/Documents/picture-bed
git add . && git commit -m "add image" && git push
```

在文章中引用：
```markdown
![描述](https://cjx132.github.io/picture-bed/image名.png)
```

### 本地预览

```bash
hexo server
# 打开 http://localhost:4000/cjx132/
```

### 发布

```bash
hexo clean && hexo generate && hexo deploy
# 等待1-2分钟，GitHub Pages 自动构建
# 访问 https://cjx132.github.io/cjx132/ 查看效果
```

### 备份源码（建议每次发布后执行）

```bash
cd ~/Documents/cjx132-blog
git add -A && git commit -m "update" && git push source main
```

## 关键配置文件

- `_config.yml` — Hexo 主配置 (URL, 部署目标等)
- `themes/next/_config.yml` — NexT 主题配置 (方案, 搜索, 代码高亮等)

## 注意事项

- 新终端窗口如果找不到 hexo 命令，执行 `source ~/.zshrc`
- `hexo deploy` 会覆盖 `cjx132/cjx132` 仓库的 master 分支（这是正常的）
- 317 篇旧文章已从 HTML 转换为 Markdown，保存在 `source/_posts/` 下
- 7 篇文章有 Typora 本地截图丢失（旧 Windows 电脑上的文件），待找回后补上
- 面试复习指南: https://cjx132.github.io/cjx132/interview-guide/
