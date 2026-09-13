# cjx132 博客使用指南

## 环境信息

- 博客目录: `/Users/cjx/Documents/cjx132-blog`
- Node.js: `~/tools/node/bin/` (已加入 ~/.zshrc 和 ~/.bashrc 的 PATH)
- Hexo: v7.1.1 + NexT 主题 v8.29.0 (Gemini 方案)

## GitHub 仓库

| 仓库 | 用途 |
|------|------|
| `cjx132/cjx132` | GitHub Pages 静态站点 (hexo deploy 推送) |
| `cjx132/picture-bed` | 图床 (167张图片, 已开启 Pages) |
| `cjx132/cjx132-blog-source` | Hexo 源码备份 |

## 日常使用

### 写新文章

```bash
cd ~/Documents/cjx132-blog
hexo new "文章标题"
```

会在 `source/_posts/文章标题.md` 创建文件，编辑内容后：

```bash
hexo clean && hexo generate && hexo deploy
```

### 本地预览

```bash
hexo server
# 打开 http://localhost:4000/cjx132/
```

### 上传图片到图床

把图片放到 picture-bed 仓库，然后推送到 GitHub：

```bash
cd /path/to/picture-bed
git add . && git commit -m "add image" && git push
```

在文章中引用图片：
```markdown
![描述](https://cjx132.github.io/picture-bed/图片名.png)
```

### 备份源码

```bash
cd ~/Documents/cjx132-blog
git add -A && git commit -m "update" && git push source master
```

## 关键配置文件

- `_config.yml` — Hexo 主配置 (URL, 部署目标等)
- `themes/next/_config.yml` — NexT 主题配置 (方案, 搜索, 代码高亮等)

## 注意事项

- 新终端窗口如果找不到 hexo 命令，执行 `source ~/.zshrc`
- `hexo deploy` 会覆盖 `cjx132/cjx132` 仓库的 master 分支（这是正常的）
- 317 篇旧文章已从 HTML 转换为 Markdown，保存在 `source/_posts/` 下
