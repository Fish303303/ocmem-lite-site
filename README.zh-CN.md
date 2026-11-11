# OCMem — 官网

**OCMem** 的静态介绍页：给 [OpenCode](https://opencode.ai) 用的省 token 长期记忆插件。

- 纯静态：`index.html`（中文）+ `en.html`（英文）
- 不用 CDN、不需要构建、无外部资源
- 直接把仓库根目录作为 GitHub Pages 发布源

**线上地址**：https://fish303303.github.io/ocmem-site/ · **英文**：https://fish303303.github.io/ocmem-site/en.html

[English](./README.md)

## 本地预览

双击 `index.html`，或在目录里起个服务：

```bash
python -m http.server 8080
```

访问 http://localhost:8080

## 发布（GitHub Pages）

仓库 **Settings → Pages → Source 选 Deploy from a branch → 分支 `main`、目录 `/ (root)` → Save**。

地址：

```
https://<用户名>.github.io/<仓库名>/
```

## 文件说明

| 文件 | 作用 |
| --- | --- |
| `index.html` | 中文首页 |
| `en.html` | 英文首页 |
| `.nojekyll` | 跳过 Jekyll 处理 |
| `README.md` / `README.zh-CN.md` | 仓库说明 |

## 许可

MIT
