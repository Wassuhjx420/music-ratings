# Xan's Music Ratings

一个 iOS 风格的交互式个人乐评网页应用，收录 880+ 张专辑/单曲，时间跨度 1960s–2026。

**无需服务器**，直接在浏览器中打开即可使用。所有编辑自动保存到浏览器本地存储，支持 JSON 导入导出实现跨设备同步。

## 截图

<!-- TODO: 添加截图 -->

## 功能

- **浏览与搜索** — 按年份浏览，支持标题/艺术家搜索，200ms debounce
- **筛选** — 按类型（EP、Mixtape、Reissue 等）和分数范围（90+、80+、&lt;50、NR）筛选
- **编辑** — 点击卡片即可编辑标题、艺术家、分数、评论、标签、音轨评分等
- **AOTY** — 年度专辑标记，任意条目均可标记为 AOTY
- **拖拽排序** — 同组内卡片可拖拽排序，FLIP 动画平滑过渡
- **主题切换** — 亮/暗模式 + 纯色/毛玻璃两种风格独立切换
- **数据管理** — 导出/导入 JSON 实现跨设备迁移，支持从 txt 源文件重新生成
- **年份管理** — 侧边栏 hover 显示删除按钮，一键删除整年数据

## 使用方式

### 方式一：直接打开（推荐）

```bash
# 双击打开 index.html 即可
open index.html
```

首次打开会加载嵌入的初始数据，后续编辑自动保存到 `localStorage`。

### 方式二：重新生成数据

```bash
# 从源 txt 文件重新解析并生成
node gen.js
```

读取 `Xan's Music Ratings 26.05.21.txt`（需自行放置在项目根目录），生成 JSON 并嵌入到根目录 `index.html`，同时写入 `data.json` 作为备份。

### 数据加载优先级

```
localStorage > __MUSIC_DATA__（嵌入数据）> data.json
```

## 项目结构

```
├── index.html             # 入口文件，包含 __MUSIC_DATA__ 占位
├── css/
│   ├── base.css           # CSS 变量、Reset、主题（亮/暗/毛玻璃）
│   ├── layout.css         # 侧边栏、工具栏、内容区布局
│   └── components.css     # 卡片、弹窗、标签、按钮、动画
├── js/
│   ├── state.js           # 全局状态变量
│   ├── i18n.js            # 中英文翻译字典、t() 函数、applyI18nToDOM()
│   ├── utils.js           # escapeHtml、getScoreClass、generateId
│   ├── filter.js          # 筛选/搜索逻辑、entry 索引
│   ├── modal.js           # 编辑弹窗逻辑
│   ├── render.js          # 侧边栏 + 内容区渲染、vol 合并、拖拽排序
│   └── app.js             # 初始化、主题切换、数据持久化、导入导出
├── gen.js                 # txt → JSON 解析器，嵌入数据到 index.html
└── CLAUDE.md              # Claude Code 开发指南
```

JS 加载顺序：`state.js` → `i18n.js` → `utils.js` → `filter.js` → `modal.js` → `render.js` → `app.js`

## 数据结构

```json
{
  "meta": {},
  "sections": [
    {
      "id": "vol1-2025",
      "title": "2025 Vol.1",
      "groups": [
        {
          "name": "January",
          "entries": [
            {
              "id": "abc123",
              "title": "Album Title",
              "artist": "Artist Name",
              "score": 85,
              "scoreNote": "",
              "date": "2025-01-15",
              "tags": ["EP"],
              "review": "写几句评论",
              "isAoty": false,
              "notes": "",
              "tracks": [{ "name": "Song 1", "score": 9 }]
            }
          ]
        }
      ]
    }
  ]
}
```

- `score`：整数 0–100 或 `null`（未评分）
- `scoreNote`：特殊标记，如 `"NR"`、`"Top1"`
- `tags`：`["EP","Mixtape","Reissue","Soundtrack","Live","Compilation","Unofficial","DJ Mix","Video"]`
- `isAoty`：年度专辑标记，渲染时动态归入 AOTY 组
- `tracks`：可选的音轨评分数组

## 技术栈

- 纯原生 HTML / CSS / JavaScript，无任何框架或依赖
- iOS 15 设计语言
- localStorage 持久化
- Pointer Events 拖拽 + FLIP 动画
