# OCMem

**给 [OpenCode](https://opencode.ai) 用的省 token 长期记忆插件。**
本地优先，不装向量模型，不需要 API key —— 每次请求多花约 **200 token**，而不是 1–2k。

[English](./README.md) · [中文首页](https://fish303303.github.io/ocmem-site/) · [English site](https://fish303303.github.io/ocmem-site/en.html)

```
opencode plugin add opencode-ocmem
```

---

## 为什么还要再造一个记忆插件

大多数记忆插件要么每轮都塞一大堆检索结果，要么一上来就要下载 500MB 的向量模型。

OCMem 反着来：**记忆应该小、便宜、不折腾。**

- **AI 抽取，规则兜底。** 每条消息交给一个小模型判断有没有长期偏好，失败时退回本地正则。
- **固定大小的摘要。** 当前主题最多 6 条事实，写进 `AGENTS.md` 的托管区块，跟着系统提示走，
  不额外调工具、不做向量检索。
- **主题分组。** 只展开当前主题（coding / computer / work / life），其他主题一行带过
  （`other topics: stocks 3`）。
- **按需检索。** 一个 `memory` 工具（`add` / `search` / `list` / `forget`）处理剩下的需求。

| | OCMem | 常见记忆插件 |
| --- | --- | --- |
| 每次请求固定开销 | 约 200 token（实测） | 1–2k（估算） |
| 向量模型 | 无 | 100–500 MB |
| 外部 API | 无 | 常常需要 |
| 存储 | 本地 JSON | SQLite / 云端 |
| 安装 | 一条命令 | 下模型、配 key |

> 其他插件 1–2k 的数字是按它们的文档和注入格式估算的，不是基准测试。OCMem 自己的数字是实测的，方法见下。

---

## 实测成本

方法：两个全新的 `opencode run` 会话，同一句提示，一个 `AGENTS.md` 里带 OCMem 摘要块，一个不带，
prompt token 从 OpenCode 的会话数据库里读。

| 运行 | Prompt tokens |
| --- | --- |
| 带摘要块 | 6,406 |
| 不带 | 6,316 |
| **差值** | **90** |

当时摘要块是 169 个字符。`memory` 工具的 schema 会一直注册（约 100 token），
所以单次总开销约 **200 token**。长会话里前缀会被缓存（`cache.read`），实际计费比标称更低。

想自己复现：

```bash
# 终端 1：装了 OCMem 的目录
opencode run "列出这个文件夹里的文件"

# 终端 2：没有摘要块的目录
opencode run "列出这个文件夹里的文件"
```

然后对比会话库里的 prompt token。

---

## 它到底做什么

1. **自动捕获** —— 像长期偏好的句子（「我习惯…」「以后都…」「不要…」「默认…」）自动保存；
   提问、一次性命令、测试闲聊会被过滤。
2. **去重** —— 字符二元组 Jaccard ≥ 0.75 视为近似重复，自动合并。
3. **摘要** —— 当前主题最多 6 条事实，加一行其余主题索引，写进 `AGENTS.md` 的
   `<!-- ocmem:begin --> … <!-- ocmem:end -->` 之间。
4. **短期回忆** —— 保留上次会话最后几条非提问消息，放在「上次聊到」，新会话能接着说。
5. **检索工具** —— `memory` 支持 `search`（二元组 + 时间衰减 + 使用频次打分）、`list`、`forget`。

---

## 安装

需要 Node.js 18+ 和 OpenCode。

```bash
opencode plugin add opencode-ocmem
```

就这一步。重启 OpenCode 生效。

---

## 用法

正常聊天就行，长期偏好会被自动记下来：

```
你：我习惯用 pnpm，不要随便加新依赖。
你：记住：我的时区是 Asia/Shanghai。
```

需要手动控制时：

```bash
opencode
memory list
memory search 股票
memory forget <id>
memory add "手机上回答要短"
```

摘要会自动重写，长这样：

```md
<!-- ocmem:begin -->
## 记忆（自动管理）
- 习惯用 pnpm，不加依赖前先问
- 时区 Asia/Shanghai
other topics: 股票 3
<!-- ocmem:end -->
```

---

## 存储

| 内容 | 位置 |
| --- | --- |
| 事实 | `~/.local/share/ocmem/memories.json` |
| 最近消息 | `~/.local/share/ocmem/recent.json` |
| 摘要 | `~/.config/opencode/AGENTS.md` 里的托管区块 |

全是本机明文 JSON。数据不出机器，删掉区块或文件就能清空。

---

## 限制

- 基于规则的捕获比较保守，换个说法的偏好可能漏掉。可以明确说「记住…」，或直接调 `memory` 工具。
- 没有语义检索（故意的）。中英文都靠字符二元组。
- 摘要每个主题最多 6 条；更早的事实还能搜到，但不会注入。
- 不做多机同步，每台机器一份。

---

## 常见问题

**支持中文吗？**
支持。检索和去重都走字符二元组，中文不需要分词。

**会和已有的 `AGENTS.md` 打架吗？**
不会。只写自己的 `<!-- ocmem:begin -->` 区块，其他内容不动。

**怎么关掉？**
`opencode plugin remove opencode-ocmem`，再删掉那个区块。

**数据会上传吗？**
插件本身完全不发网络请求。

---

## 路线图

- [ ] 按项目隔离记忆
- [ ] 记忆库导出 / 导入
- [ ] 支持「置顶」，让某条事实不因摘要轮换而消失
- [ ] 适配其他 coding agent

---

## 开发

```bash
git clone https://github.com/Fish303303/ocmem.git
cd ocmem
npm install
npm run build
```

入口是 `index.ts`，存储是纯 JSON，测试不需要联网。

---

## 关于本仓库

这个仓库是项目的**官网**：

| 文件 | 作用 |
| --- | --- |
| `index.html` | 中文首页 |
| `en.html` | 英文首页 |
| `.nojekyll` | 跳过 Jekyll 处理 |
| `README.md` | 本文档的英文版 |

用 GitHub Pages 从 `main` 分支根目录发布：
https://fish303303.github.io/ocmem-site/

---

## 许可

MIT
