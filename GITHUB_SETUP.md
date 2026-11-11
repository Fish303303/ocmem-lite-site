# 发布前填好的信息（复制粘贴用）

## 仓库

- 仓库名：`ocmem-site`
- 描述（About）：
  > OCMem 官网：给 OpenCode 的省 token 长期记忆插件，本地优先、无向量模型，每次请求只多约 200 token。

## Topics（标签，逐个粘贴到 GitHub 仓库右上角 About 里的齿轮）

```
opencode
opencode-plugin
agent-memory
local-first
token-efficient
typescript
ai-agent
coding-agent
developer-tools
mit-license
github-pages
static-site
```

## Issue 标签（仓库 Settings → Labels 里建）

| 名称 | 颜色 | 说明 |
| --- | --- | --- |
| `bug` | `d73a4a` | 有问题，需要修 |
| `enhancement` | `a2eeef` | 新功能或改进 |
| `documentation` | `0075ca` | 文档相关 |
| `question` | `d876e1` | 使用上的疑问 |
| `good first issue` | `7057ff` | 适合新手 |
| `help wanted` | `008672` | 需要帮忙 |

## 本地已有的提交与标签

- 分支：`main`
- 提交：`chore: 初始化 OCMem 官网`
- 标签：`v0.1.0`（annotated）

## 你要练的 git 流程（按顺序敲）

```bash
cd C:\Users\1\ocmem-site

# 1. 看状态
git status
git log --oneline

# 2. 在 GitHub 网页上新建空仓库 ocmem-site（不要勾 README / .gitignore / license）

# 3. 关联远程（把 Fish303303 换成你的用户名）
git remote add origin https://github.com/Fish303303/ocmem-site.git

# 4. 确认关联
git remote -v

# 5. 推主线
git push -u origin main

# 6. 推标签
git push origin --tags

# 7. 之后改了网页，重复这三步
git add .
git commit -m "docs: 更新首页文案"
git push
```

## 需要你在网页上点的

1. 仓库 → Settings → Pages → Source 选 `Deploy from a branch`，分支 `main`、目录 `/ (root)`，Save
2. 仓库 → 右上角 About 齿轮 → 填描述 + 12 个 Topics
3. 仓库 → Settings → Labels → 建 6 个标签
4. 等 1 分钟，访问 `https://Fish303303.github.io/ocmem-site/`

## 注意

- 我没有建远程仓库，也没有 push，这一步留给你
- 这个站点是独立仓库，和插件本体 `ocmem` 仓库分开，别搞混
- 如果 push 时要密码，GitHub 现在只认 Personal Access Token，不认账号密码
