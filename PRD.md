# Flow App - 产品需求文档 (PRD)

## 1. Executive Summary

Flow 是一款 AI 驱动的信息聚合应用，专为 HarmonyOS 6.0 平台设计。该应用旨在将分散的信息源（RSS feeds、新闻通讯、播客、社交更新）整合到一个专注、无干扰的阅读空间中，通过 AI 技术重塑信息消费体验。

核心价值主张：通过智能聚合、AI 摘要、个性化推荐等功能，让用户能够高效地获取和处理感兴趣的内容，实现"信息过载到信息精选"的转变。

MVP 目标：构建一个功能完整的 HarmonyOS 原生应用，支持 RSS 订阅管理、内容阅读、AI 摘要翻译等核心功能，为用户提供流畅的信息消费体验。

---

## 2. Mission

### 产品使命

让每一位用户都能在信息洪流中找到属于自己的知识绿洲，通过 AI 技术赋能，实现高效、专注、个性化的信息获取体验。

### 核心原则

1. **用户至上**：本地优先架构，保护用户隐私，数据由用户掌控
2. **AI 赋能**：智能摘要、翻译、推荐，提升信息处理效率
3. **极简设计**：专注阅读体验，减少干扰，沉浸式界面
4. **开放生态**：支持多种数据源格式，兼容 RSS/RSSHub/OPML 标准
5. **跨平台一致**：保持与桌面端、移动端体验的一致性

---

## 3. Target Users

### 主要用户画像

| 画像 | 描述 | 技术水平 | 核心需求 |
|------|------|----------|----------|
| **信息工作者** | 需要跟踪行业动态、技术博客的职场人士 | 中高 | 高效获取关键信息，节省时间 |
| **内容创作者** | 需要收集素材、跟踪热点的创作者 | 中 | 多源聚合，灵感收集 |
| **技术爱好者** | 订阅大量技术博客、开源项目动态 | 高 | RSS 订阅管理，离线阅读 |
| **普通用户** | 希望统一管理新闻资讯的普通消费者 | 低 | 简单易用，智能推荐 |

### 用户痛点

- 信息源分散，需要在多个应用间切换
- 信息过载，难以筛选有价值内容
- 语言障碍，无法阅读外文内容
- 阅读体验差，广告干扰严重
- 缺乏个性化，推荐不精准

---

## 4. MVP Scope

### In Scope ✅

#### 核心功能
- [x] RSS/Atom feed 订阅管理（添加、删除、编辑、分类）
- [x] RSSHub 路由支持（rsshub:// 协议）
- [x] OPML 文件导入/导出
- [x] 文章列表展示与阅读
- [x] 文章收藏与稍后阅读
- [x] 阅读进度同步

#### AI 功能
- [x] 文章智能摘要生成
- [x] 多语言翻译（中/英/日）
- [x] AI 问答助手（基于阅读内容）
- [x] 个性化内容推荐

#### 用户体验
- [x] 明暗主题切换
- [x] 自定义阅读设置（字体、字号、行距）
- [x] 离线阅读支持
- [x] 下拉刷新与分页加载

#### 数据管理
- [x] 本地数据存储
- [x] 云端数据同步（可选）
- [x] 订阅源分组管理

### Out of Scope ❌

- [ ] 社交媒体信息流集成（Twitter/微博等）
- [ ] 播客播放功能
- [ ] Email Newsletter 集成
- [ ] 多设备实时同步
- [ ] 协作与分享功能
- [ ] 付费订阅与会员系统
- [ ] 第三方插件系统

---

## 5. User Stories

### 核心用户故事

**US-001: 订阅管理**
> 作为一名技术爱好者，我想要添加和管理我的 RSS 订阅源，以便在一个应用中统一阅读所有关注的内容。

*示例：用户通过输入 `https://example.com/feed.xml` 添加订阅，系统自动识别并解析 feed。*

**US-002: 智能摘要**
> 作为一名信息工作者，我想要快速了解文章核心内容，以便决定是否深入阅读。

*示例：用户打开一篇长文，AI 自动生成 3-5 句话的摘要，突出关键信息。*

**US-003: 跨语言阅读**
> 作为一名普通用户，我想要阅读外文文章的中文版本，以便突破语言障碍获取信息。

*示例：用户阅读英文技术文章时，点击翻译按钮即可查看中文译文。*

**US-004: 离线阅读**
> 作为一名通勤用户，我想要在没有网络时也能阅读已缓存的文章，以便充分利用碎片时间。

*示例：用户在地铁上打开应用，可以流畅阅读之前缓存的文章内容。*

**US-005: 订阅分类**
> 作为一名内容创作者，我想要将订阅源按主题分类管理，以便快速找到特定领域的内容。

*示例：用户创建"技术"、"设计"、"商业"三个分组，分别管理不同类型的订阅源。*

**US-006: AI 问答**
> 作为一名研究人员，我想要针对阅读内容提问，以便深入理解文章要点。

*示例：用户阅读一篇技术文章后，询问"这篇文章的主要贡献是什么？"，AI 基于内容回答。*

**US-007: OPML 迁移**
> 作为一名从其他 RSS 阅读器迁移的用户，我想要导入我的 OPML 订阅列表，以便快速恢复订阅。

*示例：用户从 Feedly 导出 OPML 文件，在 Flow 中一键导入所有订阅。*

**US-008: 个性化推荐**
> 作为一名普通用户，我想要系统推荐我可能感兴趣的内容，以便发现新的信息源。

*示例：基于用户阅读历史，系统推荐相似主题的优质订阅源。*

---

## 6. Core Architecture & Patterns

### 高层架构

```
┌─────────────────────────────────────────────────────────────┐
│                    Presentation Layer                        │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐  │
│  │   Pages     │  │  Components │  │   ViewModels        │  │
│  │  (ArkTS)    │  │  (ArkUI)    │  │   (State Mgmt)      │  │
│  └─────────────┘  └─────────────┘  └─────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    Business Logic Layer                      │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐  │
│  │ FeedService │  │ EntryService│  │   AIService         │  │
│  │ SyncService │  │ UserService │  │   RecommendService  │  │
│  └─────────────┘  └─────────────┘  └─────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    Data Layer                                │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐  │
│  │  Local DB   │  │ Remote API  │  │   Cache Layer       │  │
│  │ (RDB/Prefs) │  │  (HTTP)     │  │   (Memory)          │  │
│  └─────────────┘  └─────────────┘  └─────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

### 目录结构

```
entry/src/main/ets/
├── common/                    # 公共模块
│   ├── constants/            # 常量定义
│   ├── utils/                # 工具函数
│   └── types/                # 类型定义
├── components/               # 可复用组件
│   ├── base/                 # 基础组件
│   ├── feed/                 # Feed 相关组件
│   └── entry/                # Entry 相关组件
├── models/                   # 数据模型
│   ├── Feed.ets
│   ├── Entry.ets
│   └── Category.ets
├── services/                 # 业务服务
│   ├── FeedService.ets
│   ├── EntryService.ets
│   ├── AIService.ets
│   └── SyncService.ets
├── viewmodels/               # 视图模型
│   ├── FeedListViewModel.ets
│   └── EntryDetailViewModel.ets
├── pages/                    # 页面
│   ├── Index.ets             # 主页
│   ├── FeedListPage.ets      # 订阅列表
│   ├── EntryListPage.ets     # 文章列表
│   ├── EntryDetailPage.ets   # 文章详情
│   ├── DiscoverPage.ets      # 发现页面
│   └── SettingsPage.ets      # 设置页面
└── entryability/
    └── EntryAbility.ets      # 应用入口
```

### 设计模式

| 模式 | 应用场景 | 说明 |
|------|----------|------|
| **MVVM** | 页面架构 | 分离视图与业务逻辑，便于测试和维护 |
| **Repository** | 数据访问 | 统一数据访问接口，支持多数据源 |
| **Observer** | 状态管理 | 使用 @State/@Observed 实现响应式更新 |
| **Singleton** | 服务管理 | 全局服务实例，如 AIService、SyncService |
| **Factory** | 组件创建 | 动态创建不同类型的列表项组件 |
| **Strategy** | 解析器 | 支持多种 feed 格式（RSS/Atom/JSON） |

---

## 7. Tools/Features

### 7.1 订阅管理模块

#### FeedService
- **Purpose**: 管理 RSS 订阅源的增删改查
- **Operations**:
  - `addFeed(url: string)`: 添加新订阅源
  - `removeFeed(feedId: string)`: 删除订阅源
  - `updateFeed(feed: Feed)`: 更新订阅源信息
  - `getFeedList()`: 获取所有订阅源
  - `refreshFeed(feedId: string)`: 刷新订阅源内容
- **Key Features**:
  - 自动识别 feed 格式（RSS/Atom/JSON）
  - 支持 RSSHub 路由解析
  - 订阅源图标自动获取

#### CategoryService
- **Purpose**: 管理订阅源分类
- **Operations**:
  - `createCategory(name: string)`: 创建分类
  - `assignFeed(feedId: string, categoryId: string)`: 分配订阅源到分类
  - `getCategories()`: 获取所有分类

### 7.2 内容阅读模块

#### EntryService
- **Purpose**: 管理文章内容的获取与存储
- **Operations**:
  - `getEntryList(feedId?: string)`: 获取文章列表
  - `getEntryDetail(entryId: string)`: 获取文章详情
  - `markAsRead(entryId: string)`: 标记已读
  - `toggleStar(entryId: string)`: 收藏/取消收藏
  - `cacheEntry(entryId: string)`: 缓存文章离线阅读
- **Key Features**:
  - 分页加载优化
  - 阅读进度保存
  - 离线内容缓存

### 7.3 AI 功能模块

#### AIService
- **Purpose**: 提供 AI 智能功能
- **Operations**:
  - `summarize(content: string)`: 生成文章摘要
  - `translate(content: string, targetLang: string)`: 翻译内容
  - `chat(question: string, context: string)`: AI 问答
  - `recommend(userHistory: Entry[])`: 个性化推荐
- **Key Features**:
  - 支持多种 AI 后端（云端/本地）
  - 上下文感知对话
  - 结果缓存优化

### 7.4 数据同步模块

#### SyncService
- **Purpose**: 本地与云端数据同步
- **Operations**:
  - `syncFeeds()`: 同步订阅源
  - `syncReadStatus()`: 同步阅读状态
  - `syncStarred()`: 同步收藏
  - `exportOPML()`: 导出 OPML
  - `importOPML(file: File)`: 导入 OPML
- **Key Features**:
  - 增量同步
  - 冲突解决
  - 后台自动同步

---

## 8. Technology Stack

### 核心技术

| 技术 | 版本 | 用途 |
|------|------|------|
| **HarmonyOS SDK** | 6.0.0(20) | 系统平台 |
| **ArkTS** | - | 开发语言 |
| **ArkUI** | - | UI 框架 |
| **Hvigor** | - | 构建系统 |
| **Hypium** | - | 测试框架 |

### 数据存储

| 技术 | 用途 |
|------|------|
| **RelationalStore (RDB)** | 结构化数据存储（订阅、文章） |
| **Preferences** | 用户设置、偏好存储 |
| **Cache** | 内存缓存、图片缓存 |

### 网络通信

| 技术 | 用途 |
|------|------|
| **@kit.NetworkKit** | HTTP 请求、WebSocket |
| **@kit.ArkWeb** | WebView 内容渲染 |
| **XML 解析** | RSS/Atom feed 解析 |

### AI 集成

| 方案 | 说明 |
|------|------|
| **云端 API** | 调用第三方 AI 服务（如 Claude API） |
| **本地模型** | 可选的端侧推理（未来规划） |

### 第三方库

```json
{
  "dependencies": {
    "@ohos/hypium": "latest",
    "@ohos/hamock": "latest"
  }
}
```

---

## 9. Security & Configuration

### 认证授权

- **本地认证**: 使用 HarmonyOS 生物识别 API（指纹/面容）
- **云端认证**: OAuth 2.0 / JWT Token
- **数据加密**: 敏感数据使用 HarmonyOS 加密存储

### 配置管理

```typescript
// 配置项示例
interface AppConfig {
  // API 配置
  apiBaseUrl: string;
  aiApiEndpoint: string;

  // 同步配置
  syncEnabled: boolean;
  syncInterval: number;

  // 阅读配置
  defaultFontSize: number;
  theme: 'light' | 'dark' | 'auto';

  // 缓存配置
  maxCacheSize: number;
  cacheExpireDays: number;
}
```

### 安全范围

**In Scope:**
- [x] HTTPS 强制通信
- [x] 敏感数据加密存储
- [x] Token 安全管理
- [x] 输入验证与过滤

**Out of Scope:**
- [ ] 端到端加密
- [ ] 自建服务器安全审计
- [ ] 企业级权限管理

---

## 10. API Specification

### Feed API

#### 获取订阅列表
```
GET /api/feeds

Response:
{
  "code": 0,
  "data": [
    {
      "id": "feed-001",
      "title": "Tech Blog",
      "url": "https://example.com/feed.xml",
      "icon": "https://example.com/icon.png",
      "unreadCount": 42,
      "lastUpdated": "2026-03-12T10:00:00Z"
    }
  ]
}
```

#### 添加订阅
```
POST /api/feeds
Content-Type: application/json

Request:
{
  "url": "https://example.com/feed.xml"
}

Response:
{
  "code": 0,
  "data": {
    "id": "feed-002",
    "title": "New Feed",
    "url": "https://example.com/feed.xml"
  }
}
```

### Entry API

#### 获取文章列表
```
GET /api/entries?feedId=feed-001&page=1&limit=20

Response:
{
  "code": 0,
  "data": {
    "entries": [
      {
        "id": "entry-001",
        "title": "Article Title",
        "summary": "Article summary...",
        "author": "Author Name",
        "publishedAt": "2026-03-12T08:00:00Z",
        "isRead": false,
        "isStarred": false
      }
    ],
    "hasMore": true,
    "nextPage": 2
  }
}
```

### AI API

#### 生成摘要
```
POST /api/ai/summarize
Content-Type: application/json

Request:
{
  "content": "Full article content...",
  "maxLength": 200
}

Response:
{
  "code": 0,
  "data": {
    "summary": "AI generated summary..."
  }
}
```

---

## 11. Success Criteria

### MVP 成功定义

产品 MVP 阶段成功的标志是：用户能够流畅地完成"订阅-阅读-管理"的核心闭环，并愿意持续使用。

### 功能验收标准

- [ ] 用户能够成功添加 RSS 订阅源并获取内容
- [ ] 文章列表加载时间 < 2 秒
- [ ] AI 摘要生成时间 < 5 秒
- [ ] 翻译准确率 > 90%
- [ ] 离线阅读功能正常工作
- [ ] OPML 导入/导出功能正常
- [ ] 应用无崩溃，内存占用 < 200MB

### 质量指标

| 指标 | 目标值 |
|------|--------|
| 启动时间 | < 3 秒 |
| 页面切换流畅度 | 60 FPS |
| 崩溃率 | < 0.1% |
| 用户留存率（7日） | > 30% |
| 日活跃用户 | > 1000 |

### 用户体验目标

- 新用户 5 分钟内完成首次订阅
- 核心功能无需说明即可使用
- 错误提示清晰友好
- 支持无障碍访问

---

## 12. Implementation Phases

### Phase 1: 基础框架 (Week 1-2)

**Goal**: 搭建应用基础架构，实现核心数据模型

**Deliverables:**
- [ ] 项目初始化与目录结构
- [ ] 数据模型定义（Feed, Entry, Category）
- [ ] 本地数据库设计与实现
- [ ] 基础 UI 组件库
- [ ] 导航框架搭建

**Validation:**
- 数据库 CRUD 操作正常
- 基础组件可复用
- 导航跳转流畅

### Phase 2: 订阅与阅读 (Week 3-4)

**Goal**: 实现订阅管理和内容阅读核心功能

**Deliverables:**
- [ ] RSS/Atom feed 解析器
- [ ] 订阅源添加/删除/编辑
- [ ] 文章列表展示
- [ ] 文章详情页
- [ ] 阅读状态管理（已读/未读）
- [ ] 收藏功能

**Validation:**
- 能够成功订阅并获取内容
- 文章阅读体验流畅
- 状态同步正确

### Phase 3: AI 功能 (Week 5-6)

**Goal**: 集成 AI 智能功能

**Deliverables:**
- [ ] AI 服务接口设计
- [ ] 文章摘要生成
- [ ] 多语言翻译
- [ ] AI 问答助手
- [ ] 个性化推荐（基础版）

**Validation:**
- AI 功能响应时间符合预期
- 摘要/翻译质量达标
- 问答准确率 > 80%

### Phase 4: 完善与优化 (Week 7-8)

**Goal**: 完善功能，优化体验

**Deliverables:**
- [ ] OPML 导入/导出
- [ ] 离线阅读支持
- [ ] 明暗主题切换
- [ ] 设置页面完善
- [ ] 性能优化
- [ ] Bug 修复与测试

**Validation:**
- 所有 MVP 功能完整
- 性能指标达标
- 无严重 Bug

---

## 13. Future Considerations

### Post-MVP 增强

1. **社交功能**
   - 文章分享到社交平台
   - 阅读笔记与高亮
   - 社区讨论

2. **高级 AI 功能**
   - 语音朗读（TTS）
   - 智能标签分类
   - 内容去重

3. **多平台同步**
   - 跨设备实时同步
   - Web 版本
   - 桌面客户端

4. **内容扩展**
   - 播客支持
   - Newsletter 集成
   - 社交媒体信息流

5. **商业化**
   - 高级会员功能
   - 去广告订阅
   - 团队协作版

---

## 14. Risks & Mitigations

| 风险 | 影响 | 可能性 | 缓解策略 |
|------|------|--------|----------|
| **AI API 成本过高** | 高 | 中 | 实现结果缓存，优化调用频率，支持本地模型备选 |
| **RSS 源格式不统一** | 中 | 高 | 实现多格式解析器，建立兼容性测试集 |
| **性能瓶颈** | 高 | 中 | 采用虚拟列表，图片懒加载，后台预加载 |
| **用户隐私担忧** | 中 | 低 | 本地优先架构，透明数据政策，用户可控 |
| **HarmonyOS 生态限制** | 中 | 低 | 关注官方更新，保持技术储备 |

---

## 15. Appendix

### 参考文档

- [Folo 项目深度解析](https://notebooklm.google.com/notebook/db74418e-7e23-4225-a683-b2dff3f5807a)
- [HarmonyOS 开发文档](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides-V5/application-dev-guide-V5)
- [RSS 2.0 规范](https://www.rssboard.org/rss-specification)
- [RSSHub 文档](https://docs.rsshub.app/)

### 关键依赖

| 依赖 | 链接 | 用途 |
|------|------|------|
| HarmonyOS SDK | developer.huawei.com | 开发平台 |
| ArkUI | 内置 | UI 框架 |
| Hypium | 内置 | 测试框架 |

### 项目仓库结构

```
flow/
├── AppScope/           # 应用级配置
├── entry/              # 主入口模块
│   ├── src/main/
│   │   ├── ets/        # ArkTS 源码
│   │   └── resources/  # 资源文件
│   └── src/ohosTest/   # 测试代码
├── build-profile.json5 # 构建配置
└── oh-package.json5    # 依赖配置
```

---

*文档版本: 1.0*
*创建日期: 2026-03-12*
*最后更新: 2026-03-12*