# My Wiki

个人知识库，用 [MkDocs Material](https://squidfunk.github.io/mkdocs-material/) 构建，部署在 GitHub Pages。

**在线访问**：[https://Kenny-bi.github.io/-my-wiki/](https://Kenny-bi.github.io/-my-wiki/)

## 内容

- **Learn Claude Code** — Agent Harness 逐行拆解笔记（s01-s05）

## 本地预览

```bash
pip install mkdocs-material
mkdocs serve
```

浏览器打开 `localhost:8000/my-wiki/` 查看。

## 部署到 GitHub Pages

```bash
mkdocs gh-deploy
```

## 更新流程

1. 在 `docs/` 里新增或修改 `.md` 文件
2. 在 `mkdocs.yml` 的 `nav` 中添加新页面
3. 提交源代码：`git add . && git commit -m "update" && git push`
4. 重新部署：`mkdocs gh-deploy`
