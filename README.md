# OCMem 官网（GitHub Pages）

OCMem 的静态介绍页：给 [OpenCode](https://opencode.ai) 用的省 token 长期记忆插件。

- 纯静态，单文件 `index.html`，CSS 内联，无外部依赖
- 直接把仓库根目录作为 GitHub Pages 发布源即可

## 本地预览

双击 `index.html`，或在仓库目录执行：

```bash
python -m http.server 8080
```

然后访问 http://localhost:8080

## 发布到 GitHub Pages

仓库设置 → Pages → Source 选 `Deploy from a branch` → 分支 `main`、目录 `/ (root)` → Save。

等一分钟，地址是：

```
https://<你的用户名>.github.io/<仓库名>/
```

## 许可

MIT
