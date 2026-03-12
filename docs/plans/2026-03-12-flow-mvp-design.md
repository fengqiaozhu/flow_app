# Flow MVP 设计方案

**版本**: 1.0
**日期**: 2026-03-12
**状态**: 已确认

---

## 1. 项目概述

### 1.1 产品定位

**Flow** 是一款 HarmonyOS 原生 RSS 阅读应用，MVP 阶段聚焦核心订阅阅读功能，采用 HarmonyOS 原生设计风格。

### 1.2 核心设计决策

| 模块 | 决策 |
|------|------|
| AI 功能 | MVP 暂不实现，后续版本考虑 |
| 数据存储 | 纯本地存储 (RDB + Preferences) |
| UI 风格 | HarmonyOS 原生风格 |
| 导航结构 | 响应式（桌面/平板→侧边栏，手机→底部Tab+顶部Tab） |
| RSS 获取 | 混合模式（内置解析 + RSSHub 服务） |
| 离线缓存 | 自动缓存最新 N 篇 |
| 图片加载 | 用户可配置（设置中选择策略） |
| 列表视图 | 支持列表/卡片视图切换 |
| 国际化 | 中英双语 |

### 1.3 MVP 核心功能

- ✅ RSS/Atom/RSSHub 订阅管理
- ✅ 订阅分类管理
- ✅ 文章列表展示（列表/卡片视图）
- ✅ 文章阅读
- ✅ 收藏与稍后阅读
- ✅ 离线缓存
- ✅ 阅读设置（字体、主题等）
- ✅ OPML 导入导出
- ❌ AI 功能（后续版本）
- ❌ 云同步（后续版本）

### 1.4 核心设计原则

- **本地优先** - 所有数据存储在本地，无需账号，即开即用
- **响应式布局** - 适配手机、平板、桌面多种设备形态
- **开放兼容** - 支持标准 RSS/Atom + RSSHub 扩展
- **用户可控** - 图片加载、视图切换等均可配置

---

## 2. 应用架构

采用 **MVVM + Repository** 架构模式，分层清晰，便于测试和维护。

### 2.1 架构图

```
┌─────────────────────────────────────────────────────────┐
│                    Presentation Layer                    │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────┐  │
│  │   Pages     │  │  Components │  │   ViewModels    │  │
│  └─────────────┘  └─────────────┘  └─────────────────┘  │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│                    Business Layer                        │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────┐  │
│  │ FeedService │  │EntryService │  │  CacheService   │  │
│  │ SyncService │  │ ParseService│  │  SettingService │  │
│  └─────────────┘  └─────────────┘  └─────────────────┘  │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│                      Data Layer                          │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────┐  │
│  │  Repository │  │  RDB/Cache  │  │   Network API   │  │
│  └─────────────┘  └─────────────┘  └─────────────────┘  │
└─────────────────────────────────────────────────────────┘
```

### 2.2 核心模块职责

| 模块 | 职责 |
|------|------|
| FeedService | 订阅源管理（CRUD、刷新） |
| EntryService | 文章管理（列表、详情、已读、收藏） |
| ParseService | RSS/Atom 解析、RSSHub 路由处理 |
| CacheService | 离线缓存管理 |
| CategoryService | 分类管理 |
| SettingsService | 用户设置管理 |

---

## 3. 页面结构与导航

### 3.1 响应式导航设计

**手机端（竖屏）：**

```
┌─────────────────────────────┐
│ 顶部Tab: 全部 | 技术 | 设计  │  ← 订阅分类
├─────────────────────────────┤
│                             │
│      文章列表区域            │
│                             │
├─────────────────────────────┤
│ 📰订阅  🔍发现  ⭐收藏  ⚙设置 │  ← 底部主导航
└─────────────────────────────┘
```

**桌面/平板端：**

```
┌──────────┬────────────────────────────────┐
│ 订阅列表  │                                │
│ ├ 全部    │      文章列表 + 内容区          │
│ ├ 技术    │      （双栏布局）              │
│ ├ 设计    │                                │
│ └ ...    │                                │
├──────────┴────────────────────────────────┤
│ 📰订阅  🔍发现  ⭐收藏  ⚙设置              │
└────────────────────────────────────────────┘
```

### 3.2 核心页面

| 页面 | 功能 |
|------|------|
| MainPage | 主页面容器，处理导航切换 |
| SubscriptionPage | 订阅管理页 |
| DiscoverPage | 发现/添加订阅页 |
| StarredPage | 收藏页 |
| SettingsPage | 设置页 |
| EntryListPage | 文章列表页 |
| EntryDetailPage | 文章详情页 |

---

## 4. 数据模型设计

### 4.1 核心数据实体

```typescript
// 订阅源
interface Feed {
  id: string           // 主键
  title: string        // 标题
  url: string          // RSS URL
  icon?: string        // 图标 URL
  description?: string // 描述
  categoryId?: string  // 所属分类
  lastUpdated: number  // 最后更新时间
  unreadCount: number  // 未读数
  isActive: boolean    // 是否启用
}

// 文章
interface Entry {
  id: string           // 主键
  feedId: string       // 所属订阅源
  title: string        // 标题
  link: string         // 原文链接
  author?: string      // 作者
  summary?: string     // 摘要
  content?: string     // 正文内容
  publishedAt: number  // 发布时间
  isRead: boolean      // 已读
  isStarred: boolean   // 已收藏
  isCached: boolean    // 已缓存
}

// 分类
interface Category {
  id: string           // 主键
  name: string         // 分类名称
  icon?: string        // 图标
  sort: number         // 排序
}

// 用户设置
interface Settings {
  theme: 'light' | 'dark' | 'auto'
  language: 'zh' | 'en'
  imageLoadStrategy: 'always' | 'wifi' | 'manual'
  listViewMode: 'list' | 'card'
  cacheCount: number   // 缓存文章数 N
  fontSize: number     // 阅读字号
}
```

---

## 5. 目录结构

```
entry/src/main/ets/
├── common/
│   ├── constants/          # 常量定义
│   │   ├── AppConstants.ets
│   │   └── RouteConstants.ets
│   ├── enums/              # 枚举类型
│   ├── utils/              # 工具函数
│   │   ├── HttpUtil.ets
│   │   ├── DateUtil.ets
│   │   └── RssParser.ets
│   └── types/              # 类型定义
│
├── components/             # 可复用组件
│   ├── base/               # 基础组件
│   │   ├── TabBar.ets
│   │   └── SideBar.ets
│   ├── feed/               # 订阅相关组件
│   │   ├── FeedItem.ets
│   │   └── FeedCard.ets
│   └── entry/              # 文章相关组件
│       ├── EntryListItem.ets
│       └── EntryCardItem.ets
│
├── models/                 # 数据模型
│   ├── Feed.ets
│   ├── Entry.ets
│   ├── Category.ets
│   └── Settings.ets
│
├── services/               # 业务服务
│   ├── FeedService.ets
│   ├── EntryService.ets
│   ├── CategoryService.ets
│   ├── ParseService.ets
│   ├── CacheService.ets
│   └── SettingsService.ets
│
├── repository/             # 数据仓库
│   ├── FeedRepository.ets
│   ├── EntryRepository.ets
│   └── CategoryRepository.ets
│
├── viewmodels/             # 视图模型
│   ├── FeedListViewModel.ets
│   ├── EntryListViewModel.ets
│   └── SettingsViewModel.ets
│
├── pages/                  # 页面
│   ├── MainPage.ets        # 主页面容器
│   ├── SubscriptionPage.ets
│   ├── DiscoverPage.ets
│   ├── StarredPage.ets
│   ├── SettingsPage.ets
│   ├── EntryListPage.ets
│   └── EntryDetailPage.ets
│
└── entryability/
    └── EntryAbility.ets    # 应用入口
```

---

## 6. 数据库设计

使用 HarmonyOS **RelationalStore (RDB)** 存储结构化数据。

### 6.1 数据表

```sql
-- 订阅源表
CREATE TABLE feeds (
  id TEXT PRIMARY KEY,
  title TEXT NOT NULL,
  url TEXT NOT NULL UNIQUE,
  icon TEXT,
  description TEXT,
  category_id TEXT,
  last_updated INTEGER DEFAULT 0,
  unread_count INTEGER DEFAULT 0,
  is_active INTEGER DEFAULT 1
)

-- 文章表
CREATE TABLE entries (
  id TEXT PRIMARY KEY,
  feed_id TEXT NOT NULL,
  title TEXT NOT NULL,
  link TEXT,
  author TEXT,
  summary TEXT,
  content TEXT,
  published_at INTEGER,
  is_read INTEGER DEFAULT 0,
  is_starred INTEGER DEFAULT 0,
  is_cached INTEGER DEFAULT 0,
  FOREIGN KEY (feed_id) REFERENCES feeds(id)
)

-- 分类表
CREATE TABLE categories (
  id TEXT PRIMARY KEY,
  name TEXT NOT NULL,
  icon TEXT,
  sort INTEGER DEFAULT 0
)

-- 索引优化
CREATE INDEX idx_entries_feed_id ON entries(feed_id)
CREATE INDEX idx_entries_published_at ON entries(published_at)
CREATE INDEX idx_entries_is_starred ON entries(is_starred)
```

### 6.2 用户设置存储

用户设置使用 **Preferences** 存储（键值对，轻量级）。

---

## 7. 核心流程设计

### 7.1 添加订阅流程

```
用户输入 URL
    │
    ▼
┌─────────────────┐
│ 判断 URL 类型    │
└─────────────────┘
    │
    ├─── 普通 RSS ────→ 内置 Parser 解析
    │                        │
    ├─── RSSHub ────→ 调用 RSSHub 服务
    │                        │
    └─── 搜索关键词 ──→ 搜索订阅源
                             │
                             ▼
                    解析成功，获取 Feed 信息
                             │
                             ▼
                    保存到本地数据库
                             │
                             ▼
                    自动拉取最新文章
```

### 7.2 文章刷新流程

```
触发刷新（手动/自动）
    │
    ▼
遍历活跃订阅源
    │
    ▼
请求 RSS Feed
    │
    ▼
解析新文章
    │
    ▼
对比已有文章，过滤重复
    │
    ▼
保存新文章到数据库
    │
    ▼
更新未读数
    │
    ▼
触发离线缓存（最新 N 篇）
```

### 7.3 离线缓存流程

```
文章保存成功
    │
    ▼
检查是否在最新 N 篇范围内
    │
    ├─── 是 ───→ 下载正文内容 + 图片
    │                   │
    │                   ▼
    │              保存到本地
    │                   │
    │                   ▼
    │              标记 is_cached = 1
    │
    └─── 否 ───→ 跳过
```

---

## 8. 错误处理与边界情况

### 8.1 网络错误处理

| 场景 | 处理方式 |
|------|----------|
| RSS 请求失败 | 提示用户，显示上次缓存内容，标记该订阅源异常 |
| RSSHub 服务不可用 | 降级提示，建议用户更换 RSSHub 实例或稍后重试 |
| 图片加载失败 | 显示占位图，点击可重试 |
| 解析失败 | 记录日志，提示用户该订阅源格式不支持 |

### 8.2 数据边界情况

| 场景 | 处理方式 |
|------|----------|
| 无订阅源 | 显示引导页面，推荐热门订阅源 |
| 无文章 | 显示空状态提示 |
| 文章内容为空 | 显示摘要或提示"点击查看原文" |
| 存储空间不足 | 清理最旧的缓存文章，提示用户 |

### 8.3 用户操作边界

| 场景 | 处理方式 |
|------|----------|
| 删除订阅源 | 二次确认，同时删除该订阅源下所有文章 |
| OPML 导入失败 | 显示具体错误原因（格式错误/网络问题） |
| 全部标记已读 | 二次确认，避免误操作 |

---

## 9. 性能优化策略

### 9.1 列表渲染优化

| 优化点 | 实现方式 |
|--------|----------|
| 虚拟列表 | 使用 `List` + `LazyForEach` 懒加载，只渲染可见区域 |
| 图片懒加载 | 使用 `LazyLoad` 组件，进入视口才加载图片 |
| 列表项复用 | 使用 `ListItem` 的 `cachedCount` 预缓存 |

### 9.2 数据加载优化

| 优化点 | 实现方式 |
|--------|----------|
| 分页加载 | 文章列表按 20 条分页，滚动到底部加载更多 |
| 增量刷新 | 只拉取上次更新后的新文章，避免全量 |
| 后台预加载 | 应用启动时后台刷新订阅源 |
| 缓存策略 | 已读文章内容缓存 7 天，未读文章长期缓存 |

### 9.3 内存优化

| 优化点 | 实现方式 |
|--------|----------|
| 图片缓存限制 | 图片缓存上限 100MB，LRU 淘汰策略 |
| 文章内容压缩 | 缓存时压缩 HTML 内容 |
| 及时释放 | 页面销毁时释放相关数据引用 |

---

## 10. 实现计划

### Phase 1：基础框架（预计 1 周）

- [ ] 项目结构搭建
- [ ] 数据库表创建与 Repository 实现
- [ ] 基础 UI 组件库
- [ ] 导航框架（响应式布局）
- [ ] 主题与国际化支持

### Phase 2：订阅管理（预计 1 周）

- [ ] RSS/Atom 解析器实现
- [ ] RSSHub 路由支持
- [ ] 订阅源 CRUD 功能
- [ ] 分类管理
- [ ] OPML 导入导出

### Phase 3：文章阅读（预计 1 周）

- [ ] 文章列表页（列表/卡片视图）
- [ ] 文章详情页
- [ ] 已读/未读状态管理
- [ ] 收藏功能
- [ ] 下拉刷新与分页加载

### Phase 4：完善与优化（预计 1 周）

- [ ] 离线缓存功能
- [ ] 设置页面完善
- [ ] 性能优化
- [ ] 错误处理与边界情况
- [ ] 测试与 Bug 修复

---

## 附录

### A. 参考资料

- [PRD 文档](../PRD.md)
- [Folo 项目分析](https://notebooklm.google.com/notebook/db74418e-7e23-4225-a683-b2dff3f5807a)
- [HarmonyOS 开发文档](https://notebooklm.google.com/notebook/5c999611-ade3-4964-a026-9f579bb669af)
- [RSS 2.0 规范](https://www.rssboard.org/rss-specification)
- [RSSHub 文档](https://docs.rsshub.app/)

### B. 术语表

| 术语 | 说明 |
|------|------|
| Feed | 订阅源，RSS/Atom 数据源 |
| Entry | 文章，订阅源中的单条内容 |
| RSSHub | 开源的 RSS 生成工具，支持各种网站 |
| OPML | 订阅列表交换格式 |
| RDB | RelationalStore，HarmonyOS 关系型数据库 |
| Preferences | HarmonyOS 轻量级键值对存储 |