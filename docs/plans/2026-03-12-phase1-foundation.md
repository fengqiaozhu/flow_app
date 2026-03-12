# Flow MVP Phase 1: 基础框架 Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** 搭建 Flow 应用的基础架构，包括目录结构、数据模型、数据库、基础组件和导航框架。

**Architecture:** 采用 MVVM + Repository 架构，数据层使用 RelationalStore (RDB) 存储结构化数据，Preferences 存储用户设置。UI 层采用 HarmonyOS 原生风格，响应式布局适配多设备。

**Tech Stack:** HarmonyOS 6.0.0 SDK, ArkTS, ArkUI, RelationalStore, Preferences

---

## Task 1: 创建目录结构

**Files:**
- Create: `entry/src/main/ets/common/constants/AppConstants.ets`
- Create: `entry/src/main/ets/common/constants/RouteConstants.ets`
- Create: `entry/src/main/ets/common/enums/ThemeMode.ets`
- Create: `entry/src/main/ets/common/utils/Logger.ets`
- Create: `entry/src/main/ets/common/types/index.ets`

**Step 1: 创建 common/constants 目录和 AppConstants.ets**

```typescript
// entry/src/main/ets/common/constants/AppConstants.ets

/**
 * 应用全局常量
 */
export class AppConstants {
  // 应用名称
  static readonly APP_NAME: string = 'Flow';

  // 数据库配置
  static readonly DB_NAME: string = 'flow.db';
  static readonly DB_VERSION: number = 1;

  // 日志域
  static readonly LOG_DOMAIN: number = 0x0000;

  // 默认配置
  static readonly DEFAULT_PAGE_SIZE: number = 20;
  static readonly DEFAULT_CACHE_COUNT: number = 50;
  static readonly MAX_CACHE_SIZE_MB: number = 100;

  // RSS 配置
  static readonly RSS_REQUEST_TIMEOUT: number = 30000; // 30秒
  static readonly RSS_REFRESH_INTERVAL: number = 30 * 60 * 1000; // 30分钟
}
```

**Step 2: 创建 RouteConstants.ets**

```typescript
// entry/src/main/ets/common/constants/RouteConstants.ets

/**
 * 路由常量
 */
export class RouteConstants {
  // 主页面
  static readonly PAGE_MAIN: string = 'pages/MainPage';
  static readonly PAGE_INDEX: string = 'pages/Index';

  // 功能页面
  static readonly PAGE_SUBSCRIPTION: string = 'pages/SubscriptionPage';
  static readonly PAGE_DISCOVER: string = 'pages/DiscoverPage';
  static readonly PAGE_STARRED: string = 'pages/StarredPage';
  static readonly PAGE_SETTINGS: string = 'pages/SettingsPage';

  // 详情页面
  static readonly PAGE_ENTRY_LIST: string = 'pages/EntryListPage';
  static readonly PAGE_ENTRY_DETAIL: string = 'pages/EntryDetailPage';
}
```

**Step 3: 创建 ThemeMode 枚举**

```typescript
// entry/src/main/ets/common/enums/ThemeMode.ets

/**
 * 主题模式枚举
 */
export enum ThemeMode {
  LIGHT = 'light',
  DARK = 'dark',
  AUTO = 'auto'
}

/**
 * 语言枚举
 */
export enum Language {
  ZH = 'zh',
  EN = 'en'
}

/**
 * 图片加载策略
 */
export enum ImageLoadStrategy {
  ALWAYS = 'always',   // 始终加载
  WIFI = 'wifi',       // 仅 WiFi
  MANUAL = 'manual'    // 手动
}

/**
 * 列表视图模式
 */
export enum ListViewMode {
  LIST = 'list',
  CARD = 'card'
}
```

**Step 4: 创建 Logger 工具类**

```typescript
// entry/src/main/ets/common/utils/Logger.ets

import { hilog } from '@kit.PerformanceAnalysisKit';
import { AppConstants } from '../constants/AppConstants';

/**
 * 日志工具类
 */
export class Logger {
  private static domain: number = AppConstants.LOG_DOMAIN;

  static info(tag: string, format: string, ...args: string[]): void {
    hilog.info(this.domain, tag, format, ...args);
  }

  static debug(tag: string, format: string, ...args: string[]): void {
    hilog.debug(this.domain, tag, format, ...args);
  }

  static warn(tag: string, format: string, ...args: string[]): void {
    hilog.warn(this.domain, tag, format, ...args);
  }

  static error(tag: string, format: string, ...args: string[]): void {
    hilog.error(this.domain, tag, format, ...args);
  }
}
```

**Step 5: 创建类型定义文件**

```typescript
// entry/src/main/ets/common/types/index.ets

/**
 * 通用结果类型
 */
export interface Result<T> {
  success: boolean;
  data?: T;
  error?: string;
}

/**
 * 分页数据
 */
export interface PageData<T> {
  items: T[];
  hasMore: boolean;
  nextPage: number;
  total: number;
}

/**
 * 网络状态
 */
export enum NetworkStatus {
  ONLINE = 'online',
  OFFLINE = 'offline',
  UNKNOWN = 'unknown'
}
```

**Step 6: Commit**

```bash
git add entry/src/main/ets/common/
git commit -m "$(cat <<'EOF'
feat: add project constants and utility classes

- Add AppConstants for global configuration
- Add RouteConstants for navigation
- Add enums for ThemeMode, Language, ImageLoadStrategy, ListViewMode
- Add Logger utility class
- Add common type definitions

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
EOF
)"
```

---

## Task 2: 创建数据模型

**Files:**
- Create: `entry/src/main/ets/models/Feed.ets`
- Create: `entry/src/main/ets/models/Entry.ets`
- Create: `entry/src/main/ets/models/Category.ets`
- Create: `entry/src/main/ets/models/Settings.ets`

**Step 1: 创建 Feed 模型**

```typescript
// entry/src/main/ets/models/Feed.ets

/**
 * 订阅源数据模型
 */
export interface Feed {
  id: string;
  title: string;
  url: string;
  icon?: string;
  description?: string;
  categoryId?: string;
  lastUpdated: number;
  unreadCount: number;
  isActive: boolean;
  createdAt: number;
}

/**
 * Feed 创建参数
 */
export interface FeedCreateParams {
  url: string;
  title?: string;
  categoryId?: string;
}

/**
 * Feed 更新参数
 */
export interface FeedUpdateParams {
  id: string;
  title?: string;
  categoryId?: string;
  isActive?: boolean;
}

/**
 * Feed 工具函数
 */
export class FeedUtils {
  static create(id: string, url: string, title: string): Feed {
    return {
      id,
      title,
      url,
      lastUpdated: 0,
      unreadCount: 0,
      isActive: true,
      createdAt: Date.now()
    };
  }

  static generateId(url: string): string {
    // 使用 URL 的简单哈希作为 ID
    return `feed_${url.hashCode()}`;
  }
}

// String 扩展：简单哈希函数
declare global {
  interface String {
    hashCode(): string;
  }
}

String.prototype.hashCode = function(): string {
  let hash = 0;
  for (let i = 0; i < this.length; i++) {
    const char = this.charCodeAt(i);
    hash = ((hash << 5) - hash) + char;
    hash = hash & hash;
  }
  return Math.abs(hash).toString(36);
};
```

**Step 2: 创建 Entry 模型**

```typescript
// entry/src/main/ets/models/Entry.ets

/**
 * 文章数据模型
 */
export interface Entry {
  id: string;
  feedId: string;
  title: string;
  link: string;
  author?: string;
  summary?: string;
  content?: string;
  publishedAt: number;
  isRead: boolean;
  isStarred: boolean;
  isCached: boolean;
  createdAt: number;
}

/**
 * Entry 查询参数
 */
export interface EntryQueryParams {
  feedId?: string;
  categoryId?: string;
  isRead?: boolean;
  isStarred?: boolean;
  page: number;
  pageSize: number;
}

/**
 * Entry 工具函数
 */
export class EntryUtils {
  static create(
    id: string,
    feedId: string,
    title: string,
    link: string,
    publishedAt: number
  ): Entry {
    return {
      id,
      feedId,
      title,
      link,
      publishedAt,
      isRead: false,
      isStarred: false,
      isCached: false,
      createdAt: Date.now()
    };
  }

  static generateId(feedId: string, link: string): string {
    return `entry_${feedId}_${link.hashCode()}`;
  }
}
```

**Step 3: 创建 Category 模型**

```typescript
// entry/src/main/ets/models/Category.ets

/**
 * 分类数据模型
 */
export interface Category {
  id: string;
  name: string;
  icon?: string;
  sort: number;
  createdAt: number;
}

/**
 * Category 创建参数
 */
export interface CategoryCreateParams {
  name: string;
  icon?: string;
}

/**
 * Category 工具函数
 */
export class CategoryUtils {
  static create(id: string, name: string, sort: number = 0): Category {
    return {
      id,
      name,
      sort,
      createdAt: Date.now()
    };
  }

  static generateId(): string {
    return `category_${Date.now().toString(36)}`;
  }
}
```

**Step 4: 创建 Settings 模型**

```typescript
// entry/src/main/ets/models/Settings.ets
import { ThemeMode, Language, ImageLoadStrategy, ListViewMode } from '../common/enums/ThemeMode';

/**
 * 用户设置数据模型
 */
export interface Settings {
  theme: ThemeMode;
  language: Language;
  imageLoadStrategy: ImageLoadStrategy;
  listViewMode: ListViewMode;
  cacheCount: number;
  fontSize: number;
  autoRefresh: boolean;
  showUnreadCount: boolean;
}

/**
 * 默认设置
 */
export class DefaultSettings {
  static get(): Settings {
    return {
      theme: ThemeMode.AUTO,
      language: Language.ZH,
      imageLoadStrategy: ImageLoadStrategy.WIFI,
      listViewMode: ListViewMode.LIST,
      cacheCount: 50,
      fontSize: 16,
      autoRefresh: true,
      showUnreadCount: true
    };
  }
}
```

**Step 5: Commit**

```bash
git add entry/src/main/ets/models/
git commit -m "$(cat <<'EOF'
feat: add data models for Feed, Entry, Category and Settings

- Add Feed model with CRUD params and utility functions
- Add Entry model with query params and utility functions
- Add Category model with create params
- Add Settings model with default values

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
EOF
)"
```

---

## Task 3: 创建数据库帮助类

**Files:**
- Create: `entry/src/main/ets/data/DatabaseHelper.ets`

**Step 1: 创建 DatabaseHelper**

```typescript
// entry/src/main/ets/data/DatabaseHelper.ets

import { relationalStore } from '@kit.ArkData';
import { AppConstants } from '../common/constants/AppConstants';
import { Logger } from '../common/utils/Logger';

const TAG = 'DatabaseHelper';

/**
 * 数据库表定义
 */
export class DbSchema {
  // 订阅源表
  static readonly TABLE_FEEDS = 'feeds';
  static readonly CREATE_FEEDS = `
    CREATE TABLE IF NOT EXISTS feeds (
      id TEXT PRIMARY KEY,
      title TEXT NOT NULL,
      url TEXT NOT NULL UNIQUE,
      icon TEXT,
      description TEXT,
      category_id TEXT,
      last_updated INTEGER DEFAULT 0,
      unread_count INTEGER DEFAULT 0,
      is_active INTEGER DEFAULT 1,
      created_at INTEGER
    )
  `;

  // 文章表
  static readonly TABLE_ENTRIES = 'entries';
  static readonly CREATE_ENTRIES = `
    CREATE TABLE IF NOT EXISTS entries (
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
      created_at INTEGER,
      FOREIGN KEY (feed_id) REFERENCES feeds(id)
    )
  `;

  // 分类表
  static readonly TABLE_CATEGORIES = 'categories';
  static readonly CREATE_CATEGORIES = `
    CREATE TABLE IF NOT EXISTS categories (
      id TEXT PRIMARY KEY,
      name TEXT NOT NULL,
      icon TEXT,
      sort INTEGER DEFAULT 0,
      created_at INTEGER
    )
  `;

  // 索引
  static readonly INDEX_ENTRIES_FEED_ID = 'idx_entries_feed_id';
  static readonly INDEX_ENTRIES_PUBLISHED = 'idx_entries_published_at';
  static readonly INDEX_ENTRIES_STARRED = 'idx_entries_is_starred';

  static getCreateIndexStatements(): string[] {
    return [
      `CREATE INDEX IF NOT EXISTS ${DbSchema.INDEX_ENTRIES_FEED_ID} ON entries(feed_id)`,
      `CREATE INDEX IF NOT EXISTS ${DbSchema.INDEX_ENTRIES_PUBLISHED} ON entries(published_at)`,
      `CREATE INDEX IF NOT EXISTS ${DbSchema.INDEX_ENTRIES_STARRED} ON entries(is_starred)`
    ];
  }
}

/**
 * 数据库帮助类
 */
export class DatabaseHelper {
  private static instance: DatabaseHelper;
  private rdbStore: relationalStore.RdbStore | null = null;
  private context: Context | null = null;

  private constructor() {}

  static getInstance(): DatabaseHelper {
    if (!DatabaseHelper.instance) {
      DatabaseHelper.instance = new DatabaseHelper();
    }
    return DatabaseHelper.instance;
  }

  /**
   * 初始化数据库
   */
  async init(context: Context): Promise<relationalStore.RdbStore> {
    if (this.rdbStore) {
      return this.rdbStore;
    }

    this.context = context;
    const config: relationalStore.StoreConfig = {
      name: AppConstants.DB_NAME,
      securityLevel: relationalStore.SecurityLevel.S1
    };

    try {
      this.rdbStore = await relationalStore.getRdbStore(context, config);
      await this.createTables();
      Logger.info(TAG, 'Database initialized successfully');
      return this.rdbStore;
    } catch (err) {
      Logger.error(TAG, 'Failed to initialize database: %{public}s', JSON.stringify(err));
      throw err;
    }
  }

  /**
   * 创建数据表
   */
  private async createTables(): Promise<void> {
    if (!this.rdbStore) {
      throw new Error('Database not initialized');
    }

    // 创建表
    await this.rdbStore.executeSql(DbSchema.CREATE_FEEDS);
    await this.rdbStore.executeSql(DbSchema.CREATE_ENTRIES);
    await this.rdbStore.executeSql(DbSchema.CREATE_CATEGORIES);

    // 创建索引
    for (const sql of DbSchema.getCreateIndexStatements()) {
      await this.rdbStore.executeSql(sql);
    }

    Logger.info(TAG, 'Tables created successfully');
  }

  /**
   * 获取 RdbStore 实例
   */
  getStore(): relationalStore.RdbStore {
    if (!this.rdbStore) {
      throw new Error('Database not initialized. Call init() first.');
    }
    return this.rdbStore;
  }

  /**
   * 关闭数据库
   */
  async close(): Promise<void> {
    if (this.rdbStore) {
      await relationalStore.deleteRdbStore(this.context!, AppConstants.DB_NAME);
      this.rdbStore = null;
      Logger.info(TAG, 'Database closed');
    }
  }
}
```

**Step 2: Commit**

```bash
git add entry/src/main/ets/data/
git commit -m "$(cat <<'EOF'
feat: add DatabaseHelper with RDB schema

- Add DbSchema with table definitions for feeds, entries, categories
- Add indexes for query optimization
- Add DatabaseHelper singleton for database management
- Support init, createTables, getStore, close operations

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
EOF
)"
```

---

## Task 4: 创建 Repository 层

**Files:**
- Create: `entry/src/main/ets/repository/FeedRepository.ets`
- Create: `entry/src/main/ets/repository/EntryRepository.ets`
- Create: `entry/src/main/ets/repository/CategoryRepository.ets`

**Step 1: 创建 FeedRepository**

```typescript
// entry/src/main/ets/repository/FeedRepository.ets

import { relationalStore } from '@kit.ArkData';
import { DatabaseHelper, DbSchema } from '../data/DatabaseHelper';
import { Feed, FeedCreateParams, FeedUpdateParams } from '../models/Feed';
import { Logger } from '../common/utils/Logger';

const TAG = 'FeedRepository';

/**
 * 订阅源数据仓库
 */
export class FeedRepository {
  private static instance: FeedRepository;
  private db: DatabaseHelper;

  private constructor() {
    this.db = DatabaseHelper.getInstance();
  }

  static getInstance(): FeedRepository {
    if (!FeedRepository.instance) {
      FeedRepository.instance = new FeedRepository();
    }
    return FeedRepository.instance;
  }

  /**
   * 插入订阅源
   */
  async insert(feed: Feed): Promise<void> {
    const store = this.db.getStore();
    const valueBucket: relationalStore.ValuesBucket = {
      id: feed.id,
      title: feed.title,
      url: feed.url,
      icon: feed.icon || null,
      description: feed.description || null,
      category_id: feed.categoryId || null,
      last_updated: feed.lastUpdated,
      unread_count: feed.unreadCount,
      is_active: feed.isActive ? 1 : 0,
      created_at: feed.createdAt
    };

    await store.insert(DbSchema.TABLE_FEEDS, valueBucket);
    Logger.info(TAG, 'Feed inserted: %{public}s', feed.id);
  }

  /**
   * 更新订阅源
   */
  async update(params: FeedUpdateParams): Promise<void> {
    const store = this.db.getStore();
    const valueBucket: relationalStore.ValuesBucket = {};

    if (params.title !== undefined) valueBucket.title = params.title;
    if (params.categoryId !== undefined) valueBucket.category_id = params.categoryId;
    if (params.isActive !== undefined) valueBucket.is_active = params.isActive ? 1 : 0;

    const predicates = new relationalStore.RdbPredicates(DbSchema.TABLE_FEEDS);
    predicates.equalTo('id', params.id);

    await store.update(valueBucket, predicates);
    Logger.info(TAG, 'Feed updated: %{public}s', params.id);
  }

  /**
   * 删除订阅源
   */
  async delete(id: string): Promise<void> {
    const store = this.db.getStore();
    const predicates = new relationalStore.RdbPredicates(DbSchema.TABLE_FEEDS);
    predicates.equalTo('id', id);

    await store.delete(predicates);
    Logger.info(TAG, 'Feed deleted: %{public}s', id);
  }

  /**
   * 查询所有订阅源
   */
  async getAll(): Promise<Feed[]> {
    const store = this.db.getStore();
    const predicates = new relationalStore.RdbPredicates(DbSchema.TABLE_FEEDS);
    predicates.orderByAsc('created_at');

    const resultSet = await store.query(predicates);
    const feeds: Feed[] = [];

    while (resultSet.goToNextRow()) {
      feeds.push(this.mapToFeed(resultSet));
    }
    resultSet.close();

    return feeds;
  }

  /**
   * 根据 ID 查询
   */
  async getById(id: string): Promise<Feed | null> {
    const store = this.db.getStore();
    const predicates = new relationalStore.RdbPredicates(DbSchema.TABLE_FEEDS);
    predicates.equalTo('id', id);

    const resultSet = await store.query(predicates);
    let feed: Feed | null = null;

    if (resultSet.goToNextRow()) {
      feed = this.mapToFeed(resultSet);
    }
    resultSet.close();

    return feed;
  }

  /**
   * 根据分类查询
   */
  async getByCategory(categoryId: string): Promise<Feed[]> {
    const store = this.db.getStore();
    const predicates = new relationalStore.RdbPredicates(DbSchema.TABLE_FEEDS);
    predicates.equalTo('category_id', categoryId);
    predicates.orderByAsc('created_at');

    const resultSet = await store.query(predicates);
    const feeds: Feed[] = [];

    while (resultSet.goToNextRow()) {
      feeds.push(this.mapToFeed(resultSet));
    }
    resultSet.close();

    return feeds;
  }

  /**
   * 更新未读数
   */
  async updateUnreadCount(id: string, count: number): Promise<void> {
    const store = this.db.getStore();
    const valueBucket: relationalStore.ValuesBucket = {
      unread_count: count
    };

    const predicates = new relationalStore.RdbPredicates(DbSchema.TABLE_FEEDS);
    predicates.equalTo('id', id);

    await store.update(valueBucket, predicates);
  }

  /**
   * 更新最后更新时间
   */
  async updateLastUpdated(id: string, timestamp: number): Promise<void> {
    const store = this.db.getStore();
    const valueBucket: relationalStore.ValuesBucket = {
      last_updated: timestamp
    };

    const predicates = new relationalStore.RdbPredicates(DbSchema.TABLE_FEEDS);
    predicates.equalTo('id', id);

    await store.update(valueBucket, predicates);
  }

  /**
   * 映射结果集到 Feed 对象
   */
  private mapToFeed(resultSet: relationalStore.ResultSet): Feed {
    return {
      id: resultSet.getString(resultSet.getColumnIndex('id')),
      title: resultSet.getString(resultSet.getColumnIndex('title')),
      url: resultSet.getString(resultSet.getColumnIndex('url')),
      icon: resultSet.getString(resultSet.getColumnIndex('icon')) || undefined,
      description: resultSet.getString(resultSet.getColumnIndex('description')) || undefined,
      categoryId: resultSet.getString(resultSet.getColumnIndex('category_id')) || undefined,
      lastUpdated: resultSet.getLong(resultSet.getColumnIndex('last_updated')),
      unreadCount: resultSet.getLong(resultSet.getColumnIndex('unread_count')),
      isActive: resultSet.getLong(resultSet.getColumnIndex('is_active')) === 1,
      createdAt: resultSet.getLong(resultSet.getColumnIndex('created_at'))
    };
  }
}
```

**Step 2: 创建 EntryRepository**

```typescript
// entry/src/main/ets/repository/EntryRepository.ets

import { relationalStore } from '@kit.ArkData';
import { DatabaseHelper, DbSchema } from '../data/DatabaseHelper';
import { Entry, EntryQueryParams } from '../models/Entry';
import { Logger } from '../common/utils/Logger';
import { AppConstants } from '../common/constants/AppConstants';

const TAG = 'EntryRepository';

/**
 * 文章数据仓库
 */
export class EntryRepository {
  private static instance: EntryRepository;
  private db: DatabaseHelper;

  private constructor() {
    this.db = DatabaseHelper.getInstance();
  }

  static getInstance(): EntryRepository {
    if (!EntryRepository.instance) {
      EntryRepository.instance = new EntryRepository();
    }
    return EntryRepository.instance;
  }

  /**
   * 批量插入文章
   */
  async insertBatch(entries: Entry[]): Promise<void> {
    if (entries.length === 0) return;

    const store = this.db.getStore();
    for (const entry of entries) {
      const valueBucket: relationalStore.ValuesBucket = {
        id: entry.id,
        feed_id: entry.feedId,
        title: entry.title,
        link: entry.link || null,
        author: entry.author || null,
        summary: entry.summary || null,
        content: entry.content || null,
        published_at: entry.publishedAt,
        is_read: entry.isRead ? 1 : 0,
        is_starred: entry.isStarred ? 1 : 0,
        is_cached: entry.isCached ? 1 : 0,
        created_at: entry.createdAt
      };

      try {
        await store.insert(DbSchema.TABLE_ENTRIES, valueBucket);
      } catch (err) {
        // 忽略重复插入错误
        Logger.warn(TAG, 'Skip duplicate entry: %{public}s', entry.id);
      }
    }

    Logger.info(TAG, 'Batch inserted %{public}d entries', entries.length.toString());
  }

  /**
   * 查询文章列表
   */
  async query(params: EntryQueryParams): Promise<Entry[]> {
    const store = this.db.getStore();
    const predicates = new relationalStore.RdbPredicates(DbSchema.TABLE_ENTRIES);

    // 条件过滤
    if (params.feedId) {
      predicates.equalTo('feed_id', params.feedId);
    }
    if (params.isRead !== undefined) {
      predicates.equalTo('is_read', params.isRead ? 1 : 0);
    }
    if (params.isStarred !== undefined) {
      predicates.equalTo('is_starred', params.isStarred ? 1 : 0);
    }

    // 排序和分页
    predicates.orderByDesc('published_at');
    const offset = (params.page - 1) * params.pageSize;
    predicates.limitAs(params.pageSize);
    predicates.offsetAs(offset);

    const resultSet = await store.query(predicates);
    const entries: Entry[] = [];

    while (resultSet.goToNextRow()) {
      entries.push(this.mapToEntry(resultSet));
    }
    resultSet.close();

    return entries;
  }

  /**
   * 根据 ID 查询
   */
  async getById(id: string): Promise<Entry | null> {
    const store = this.db.getStore();
    const predicates = new relationalStore.RdbPredicates(DbSchema.TABLE_ENTRIES);
    predicates.equalTo('id', id);

    const resultSet = await store.query(predicates);
    let entry: Entry | null = null;

    if (resultSet.goToNextRow()) {
      entry = this.mapToEntry(resultSet);
    }
    resultSet.close();

    return entry;
  }

  /**
   * 标记已读
   */
  async markAsRead(id: string): Promise<void> {
    const store = this.db.getStore();
    const valueBucket: relationalStore.ValuesBucket = {
      is_read: 1
    };

    const predicates = new relationalStore.RdbPredicates(DbSchema.TABLE_ENTRIES);
    predicates.equalTo('id', id);

    await store.update(valueBucket, predicates);
    Logger.info(TAG, 'Entry marked as read: %{public}s', id);
  }

  /**
   * 切换收藏状态
   */
  async toggleStar(id: string): Promise<boolean> {
    const store = this.db.getStore();

    // 先查询当前状态
    const entry = await this.getById(id);
    if (!entry) return false;

    const newStarred = !entry.isStarred;
    const valueBucket: relationalStore.ValuesBucket = {
      is_starred: newStarred ? 1 : 0
    };

    const predicates = new relationalStore.RdbPredicates(DbSchema.TABLE_ENTRIES);
    predicates.equalTo('id', id);

    await store.update(valueBucket, predicates);
    Logger.info(TAG, 'Entry starred: %{public}s -> %{public}s', id, newStarred.toString());

    return newStarred;
  }

  /**
   * 全部标记已读
   */
  async markAllAsRead(feedId?: string): Promise<void> {
    const store = this.db.getStore();
    const valueBucket: relationalStore.ValuesBucket = {
      is_read: 1
    };

    const predicates = new relationalStore.RdbPredicates(DbSchema.TABLE_ENTRIES);
    if (feedId) {
      predicates.equalTo('feed_id', feedId);
    }

    await store.update(valueBucket, predicates);
    Logger.info(TAG, 'All entries marked as read');
  }

  /**
   * 获取未读数量
   */
  async getUnreadCount(feedId?: string): Promise<number> {
    const store = this.db.getStore();
    const predicates = new relationalStore.RdbPredicates(DbSchema.TABLE_ENTRIES);
    predicates.equalTo('is_read', 0);

    if (feedId) {
      predicates.equalTo('feed_id', feedId);
    }

    const resultSet = await store.query(predicates, ['COUNT(*) as count']);
    let count = 0;

    if (resultSet.goToNextRow()) {
      count = resultSet.getLong(resultSet.getColumnIndex('count'));
    }
    resultSet.close();

    return count;
  }

  /**
   * 获取收藏列表
   */
  async getStarred(page: number = 1): Promise<Entry[]> {
    return this.query({
      isStarred: true,
      page,
      pageSize: AppConstants.DEFAULT_PAGE_SIZE
    });
  }

  /**
   * 删除订阅源的所有文章
   */
  async deleteByFeedId(feedId: string): Promise<void> {
    const store = this.db.getStore();
    const predicates = new relationalStore.RdbPredicates(DbSchema.TABLE_ENTRIES);
    predicates.equalTo('feed_id', feedId);

    await store.delete(predicates);
    Logger.info(TAG, 'Entries deleted for feed: %{public}s', feedId);
  }

  /**
   * 映射结果集到 Entry 对象
   */
  private mapToEntry(resultSet: relationalStore.ResultSet): Entry {
    return {
      id: resultSet.getString(resultSet.getColumnIndex('id')),
      feedId: resultSet.getString(resultSet.getColumnIndex('feed_id')),
      title: resultSet.getString(resultSet.getColumnIndex('title')),
      link: resultSet.getString(resultSet.getColumnIndex('link')) || '',
      author: resultSet.getString(resultSet.getColumnIndex('author')) || undefined,
      summary: resultSet.getString(resultSet.getColumnIndex('summary')) || undefined,
      content: resultSet.getString(resultSet.getColumnIndex('content')) || undefined,
      publishedAt: resultSet.getLong(resultSet.getColumnIndex('published_at')),
      isRead: resultSet.getLong(resultSet.getColumnIndex('is_read')) === 1,
      isStarred: resultSet.getLong(resultSet.getColumnIndex('is_starred')) === 1,
      isCached: resultSet.getLong(resultSet.getColumnIndex('is_cached')) === 1,
      createdAt: resultSet.getLong(resultSet.getColumnIndex('created_at'))
    };
  }
}
```

**Step 3: 创建 CategoryRepository**

```typescript
// entry/src/main/ets/repository/CategoryRepository.ets

import { relationalStore } from '@kit.ArkData';
import { DatabaseHelper, DbSchema } from '../data/DatabaseHelper';
import { Category, CategoryCreateParams } from '../models/Category';
import { Logger } from '../common/utils/Logger';

const TAG = 'CategoryRepository';

/**
 * 分类数据仓库
 */
export class CategoryRepository {
  private static instance: CategoryRepository;
  private db: DatabaseHelper;

  private constructor() {
    this.db = DatabaseHelper.getInstance();
  }

  static getInstance(): CategoryRepository {
    if (!CategoryRepository.instance) {
      CategoryRepository.instance = new CategoryRepository();
    }
    return CategoryRepository.instance;
  }

  /**
   * 插入分类
   */
  async insert(category: Category): Promise<void> {
    const store = this.db.getStore();
    const valueBucket: relationalStore.ValuesBucket = {
      id: category.id,
      name: category.name,
      icon: category.icon || null,
      sort: category.sort,
      created_at: category.createdAt
    };

    await store.insert(DbSchema.TABLE_CATEGORIES, valueBucket);
    Logger.info(TAG, 'Category inserted: %{public}s', category.id);
  }

  /**
   * 更新分类
   */
  async update(id: string, name: string): Promise<void> {
    const store = this.db.getStore();
    const valueBucket: relationalStore.ValuesBucket = {
      name
    };

    const predicates = new relationalStore.RdbPredicates(DbSchema.TABLE_CATEGORIES);
    predicates.equalTo('id', id);

    await store.update(valueBucket, predicates);
    Logger.info(TAG, 'Category updated: %{public}s', id);
  }

  /**
   * 删除分类
   */
  async delete(id: string): Promise<void> {
    const store = this.db.getStore();
    const predicates = new relationalStore.RdbPredicates(DbSchema.TABLE_CATEGORIES);
    predicates.equalTo('id', id);

    await store.delete(predicates);
    Logger.info(TAG, 'Category deleted: %{public}s', id);
  }

  /**
   * 查询所有分类
   */
  async getAll(): Promise<Category[]> {
    const store = this.db.getStore();
    const predicates = new relationalStore.RdbPredicates(DbSchema.TABLE_CATEGORIES);
    predicates.orderByAsc('sort');

    const resultSet = await store.query(predicates);
    const categories: Category[] = [];

    while (resultSet.goToNextRow()) {
      categories.push(this.mapToCategory(resultSet));
    }
    resultSet.close();

    return categories;
  }

  /**
   * 根据 ID 查询
   */
  async getById(id: string): Promise<Category | null> {
    const store = this.db.getStore();
    const predicates = new relationalStore.RdbPredicates(DbSchema.TABLE_CATEGORIES);
    predicates.equalTo('id', id);

    const resultSet = await store.query(predicates);
    let category: Category | null = null;

    if (resultSet.goToNextRow()) {
      category = this.mapToCategory(resultSet);
    }
    resultSet.close();

    return category;
  }

  /**
   * 更新排序
   */
  async updateSort(id: string, sort: number): Promise<void> {
    const store = this.db.getStore();
    const valueBucket: relationalStore.ValuesBucket = {
      sort
    };

    const predicates = new relationalStore.RdbPredicates(DbSchema.TABLE_CATEGORIES);
    predicates.equalTo('id', id);

    await store.update(valueBucket, predicates);
  }

  /**
   * 映射结果集到 Category 对象
   */
  private mapToCategory(resultSet: relationalStore.ResultSet): Category {
    return {
      id: resultSet.getString(resultSet.getColumnIndex('id')),
      name: resultSet.getString(resultSet.getColumnIndex('name')),
      icon: resultSet.getString(resultSet.getColumnIndex('icon')) || undefined,
      sort: resultSet.getLong(resultSet.getColumnIndex('sort')),
      createdAt: resultSet.getLong(resultSet.getColumnIndex('created_at'))
    };
  }
}
```

**Step 4: Commit**

```bash
git add entry/src/main/ets/repository/
git commit -m "$(cat <<'EOF'
feat: add repository layer for Feed, Entry and Category

- Add FeedRepository with CRUD operations
- Add EntryRepository with query, markAsRead, toggleStar operations
- Add CategoryRepository with CRUD operations
- All repositories use singleton pattern

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
EOF
)"
```

---

## Task 5: 创建 Preferences 帮助类

**Files:**
- Create: `entry/src/main/ets/data/PreferencesHelper.ets`

**Step 1: 创建 PreferencesHelper**

```typescript
// entry/src/main/ets/data/PreferencesHelper.ets

import { preferences } from '@kit.ArkData';
import { Logger } from '../common/utils/Logger';
import { Settings, DefaultSettings } from '../models/Settings';
import { ThemeMode, Language, ImageLoadStrategy, ListViewMode } from '../common/enums/ThemeMode';

const TAG = 'PreferencesHelper';
const SETTINGS_FILE = 'flow_settings';

/**
 * 设置存储键
 */
export class SettingsKey {
  static readonly THEME = 'theme';
  static readonly LANGUAGE = 'language';
  static readonly IMAGE_LOAD_STRATEGY = 'image_load_strategy';
  static readonly LIST_VIEW_MODE = 'list_view_mode';
  static readonly CACHE_COUNT = 'cache_count';
  static readonly FONT_SIZE = 'font_size';
  static readonly AUTO_REFRESH = 'auto_refresh';
  static readonly SHOW_UNREAD_COUNT = 'show_unread_count';
}

/**
 * Preferences 帮助类
 */
export class PreferencesHelper {
  private static instance: PreferencesHelper;
  private preferences: preferences.Preferences | null = null;
  private context: Context | null = null;

  private constructor() {}

  static getInstance(): PreferencesHelper {
    if (!PreferencesHelper.instance) {
      PreferencesHelper.instance = new PreferencesHelper();
    }
    return PreferencesHelper.instance;
  }

  /**
   * 初始化
   */
  async init(context: Context): Promise<void> {
    try {
      this.context = context;
      this.preferences = await preferences.getPreferences(context, SETTINGS_FILE);
      Logger.info(TAG, 'Preferences initialized successfully');
    } catch (err) {
      Logger.error(TAG, 'Failed to initialize preferences: %{public}s', JSON.stringify(err));
      throw err;
    }
  }

  /**
   * 获取所有设置
   */
  async getSettings(): Promise<Settings> {
    if (!this.preferences) {
      throw new Error('Preferences not initialized');
    }

    const defaults = DefaultSettings.get();

    return {
      theme: await this.preferences.get(SettingsKey.THEME, defaults.theme) as ThemeMode,
      language: await this.preferences.get(SettingsKey.LANGUAGE, defaults.language) as Language,
      imageLoadStrategy: await this.preferences.get(
        SettingsKey.IMAGE_LOAD_STRATEGY,
        defaults.imageLoadStrategy
      ) as ImageLoadStrategy,
      listViewMode: await this.preferences.get(
        SettingsKey.LIST_VIEW_MODE,
        defaults.listViewMode
      ) as ListViewMode,
      cacheCount: await this.preferences.get(SettingsKey.CACHE_COUNT, defaults.cacheCount) as number,
      fontSize: await this.preferences.get(SettingsKey.FONT_SIZE, defaults.fontSize) as number,
      autoRefresh: await this.preferences.get(SettingsKey.AUTO_REFRESH, defaults.autoRefresh) as boolean,
      showUnreadCount: await this.preferences.get(
        SettingsKey.SHOW_UNREAD_COUNT,
        defaults.showUnreadCount
      ) as boolean
    };
  }

  /**
   * 保存主题
   */
  async setTheme(theme: ThemeMode): Promise<void> {
    await this.put(SettingsKey.THEME, theme);
  }

  /**
   * 保存语言
   */
  async setLanguage(language: Language): Promise<void> {
    await this.put(SettingsKey.LANGUAGE, language);
  }

  /**
   * 保存图片加载策略
   */
  async setImageLoadStrategy(strategy: ImageLoadStrategy): Promise<void> {
    await this.put(SettingsKey.IMAGE_LOAD_STRATEGY, strategy);
  }

  /**
   * 保存列表视图模式
   */
  async setListViewMode(mode: ListViewMode): Promise<void> {
    await this.put(SettingsKey.LIST_VIEW_MODE, mode);
  }

  /**
   * 保存缓存数量
   */
  async setCacheCount(count: number): Promise<void> {
    await this.put(SettingsKey.CACHE_COUNT, count);
  }

  /**
   * 保存字体大小
   */
  async setFontSize(size: number): Promise<void> {
    await this.put(SettingsKey.FONT_SIZE, size);
  }

  /**
   * 保存自动刷新
   */
  async setAutoRefresh(enabled: boolean): Promise<void> {
    await this.put(SettingsKey.AUTO_REFRESH, enabled);
  }

  /**
   * 保存显示未读数
   */
  async setShowUnreadCount(show: boolean): Promise<void> {
    await this.put(SettingsKey.SHOW_UNREAD_COUNT, show);
  }

  /**
   * 通用保存方法
   */
  private async put(key: string, value: preferences.ValueType): Promise<void> {
    if (!this.preferences) {
      throw new Error('Preferences not initialized');
    }

    await this.preferences.put(key, value);
    await this.preferences.flush();
    Logger.info(TAG, 'Setting saved: %{public}s', key);
  }

  /**
   * 清除所有设置
   */
  async clear(): Promise<void> {
    if (!this.preferences) {
      throw new Error('Preferences not initialized');
    }

    await this.preferences.clear();
    await this.preferences.flush();
    Logger.info(TAG, 'All settings cleared');
  }
}
```

**Step 2: Commit**

```bash
git add entry/src/main/ets/data/PreferencesHelper.ets
git commit -m "$(cat <<'EOF'
feat: add PreferencesHelper for user settings

- Add SettingsKey constants for preference keys
- Add PreferencesHelper singleton for settings management
- Support all settings operations (get/set/clear)
- Use Preferences API for lightweight key-value storage

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
EOF
)"
```

---

## Task 6: 创建基础 UI 组件

**Files:**
- Create: `entry/src/main/ets/components/base/TabBar.ets`
- Create: `entry/src/main/ets/components/base/EmptyState.ets`
- Create: `entry/src/main/ets/components/base/LoadingView.ets`

**Step 1: 创建 TabBar 组件**

```typescript
// entry/src/main/ets/components/base/TabBar.ets

/**
 * 底部标签栏数据项
 */
export interface TabBarItem {
  id: string;
  label: string;
  icon: Resource;
  activeIcon: Resource;
}

/**
 * 底部标签栏组件
 */
@Component
export struct TabBar {
  @Prop items: TabBarItem[] = [];
  @Link currentIndex: number;
  onTabChange?: (index: number) => void;

  build() {
    Row() {
      ForEach(this.items, (item: TabBarItem, index: number) => {
        Column() {
          Image(this.currentIndex === index ? item.activeIcon : item.icon)
            .width(24)
            .height(24)
            .objectFit(ImageFit.Contain)

          Text(item.label)
            .fontSize(12)
            .fontColor(this.currentIndex === index ? $r('app.color.primary') : $r('app.color.text_secondary'))
            .margin({ top: 4 })
        }
        .width('25%')
        .height('100%')
        .justifyContent(FlexAlign.Center)
        .onClick(() => {
          if (this.currentIndex !== index) {
            this.currentIndex = index;
            this.onTabChange?.(index);
          }
        })
      })
    }
    .width('100%')
    .height(56)
    .backgroundColor($r('app.color.background'))
    .border({ width: { top: 0.5 }, color: $r('app.color.divider') })
  }
}
```

**Step 2: 创建 EmptyState 组件**

```typescript
// entry/src/main/ets/components/base/EmptyState.ets

/**
 * 空状态组件
 */
@Component
export struct EmptyState {
  @Prop icon?: Resource;
  @Prop title: string = '暂无数据';
  @Prop description: string = '';
  @Prop actionText: string = '';
  onAction?: () => void;

  build() {
    Column() {
      if (this.icon) {
        Image(this.icon)
          .width(80)
          .height(80)
          .objectFit(ImageFit.Contain)
          .opacity(0.5)
      }

      Text(this.title)
        .fontSize(16)
        .fontColor($r('app.color.text_secondary'))
        .margin({ top: 16 })

      if (this.description) {
        Text(this.description)
          .fontSize(14)
          .fontColor($r('app.color.text_hint'))
          .margin({ top: 8 })
          .textAlign(TextAlign.Center)
      }

      if (this.actionText && this.onAction) {
        Button(this.actionText)
          .type(ButtonType.Normal)
          .backgroundColor($r('app.color.primary'))
          .fontColor(Color.White)
          .borderRadius(20)
          .height(40)
          .margin({ top: 24 })
          .onClick(() => this.onAction?.())
      }
    }
    .width('100%')
    .height('100%')
    .justifyContent(FlexAlign.Center)
    .padding(32)
  }
}
```

**Step 3: 创建 LoadingView 组件**

```typescript
// entry/src/main/ets/components/base/LoadingView.ets

/**
 * 加载视图组件
 */
@Component
export struct LoadingView {
  @Prop message: string = '加载中...';

  build() {
    Column() {
      LoadingProgress()
        .width(48)
        .height(48)
        .color($r('app.color.primary'))

      Text(this.message)
        .fontSize(14)
        .fontColor($r('app.color.text_secondary'))
        .margin({ top: 16 })
    }
    .width('100%')
    .height('100%')
    .justifyContent(FlexAlign.Center)
    .backgroundColor($r('app.color.background'))
  }
}
```

**Step 4: Commit**

```bash
git add entry/src/main/ets/components/base/
git commit -m "$(cat <<'EOF'
feat: add base UI components

- Add TabBar for bottom navigation
- Add EmptyState for empty data display
- Add LoadingView for loading indicator

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
EOF
)"
```

---

## Task 7: 创建主页面框架

**Files:**
- Create: `entry/src/main/ets/pages/MainPage.ets`
- Modify: `entry/src/main/ets/entryability/EntryAbility.ets`
- Modify: `entry/src/main/module.json5`

**Step 1: 创建 MainPage**

```typescript
// entry/src/main/ets/pages/MainPage.ets

import { TabBar, TabBarItem } from '../components/base/TabBar';
import { DatabaseHelper } from '../data/DatabaseHelper';
import { PreferencesHelper } from '../data/PreferencesHelper';
import { Logger } from '../common/utils/Logger';

const TAG = 'MainPage';

/**
 * 主页面容器
 */
@Entry
@Component
struct MainPage {
  @State currentIndex: number = 0;
  @State isInitialized: boolean = false;
  @State initError: string = '';

  private tabItems: TabBarItem[] = [
    {
      id: 'subscription',
      label: '订阅',
      icon: $r('app.media.ic_subscription'),
      activeIcon: $r('app.media.ic_subscription_filled')
    },
    {
      id: 'discover',
      label: '发现',
      icon: $r('app.media.ic_discover'),
      activeIcon: $r('app.media.ic_discover_filled')
    },
    {
      id: 'starred',
      label: '收藏',
      icon: $r('app.media.ic_star'),
      activeIcon: $r('app.media.ic_star_filled')
    },
    {
      id: 'settings',
      label: '设置',
      icon: $r('app.media.ic_settings'),
      activeIcon: $r('app.media.ic_settings_filled')
    }
  ];

  async aboutToAppear(): Promise<void> {
    try {
      // 初始化数据库
      await DatabaseHelper.getInstance().init(getContext(this));
      Logger.info(TAG, 'Database initialized');

      // 初始化 Preferences
      await PreferencesHelper.getInstance().init(getContext(this));
      Logger.info(TAG, 'Preferences initialized');

      this.isInitialized = true;
    } catch (err) {
      this.initError = '初始化失败: ' + JSON.stringify(err);
      Logger.error(TAG, 'Init failed: %{public}s', this.initError);
    }
  }

  build() {
    Column() {
      if (this.initError) {
        // 初始化失败显示错误
        Column() {
          Text('初始化失败')
            .fontSize(20)
            .fontWeight(FontWeight.Bold)
            .fontColor($r('app.color.error'))

          Text(this.initError)
            .fontSize(14)
            .fontColor($r('app.color.text_secondary'))
            .margin({ top: 16 })
            .textAlign(TextAlign.Center)
        }
        .width('100%')
        .height('100%')
        .justifyContent(FlexAlign.Center)
        .padding(32)
      } else if (!this.isInitialized) {
        // 加载中
        Column() {
          LoadingProgress()
            .width(48)
            .height(48)
            .color($r('app.color.primary'))

          Text('正在初始化...')
            .fontSize(14)
            .fontColor($r('app.color.text_secondary'))
            .margin({ top: 16 })
        }
        .width('100%')
        .height('100%')
        .justifyContent(FlexAlign.Center)
      } else {
        // 主内容区
        Column() {
          // 内容区域 - 根据 currentIndex 显示不同页面
          if (this.currentIndex === 0) {
            this.SubscriptionContent()
          } else if (this.currentIndex === 1) {
            this.DiscoverContent()
          } else if (this.currentIndex === 2) {
            this.StarredContent()
          } else {
            this.SettingsContent()
          }
        }
        .layoutWeight(1)

        // 底部标签栏
        TabBar({
          items: this.tabItems,
          currentIndex: $currentIndex
        })
      }
    }
    .width('100%')
    .height('100%')
    .backgroundColor($r('app.color.background'))
  }

  @Builder
  SubscriptionContent() {
    Column() {
      Text('订阅')
        .fontSize(24)
        .fontWeight(FontWeight.Bold)

      Text('订阅功能开发中...')
        .fontSize(14)
        .fontColor($r('app.color.text_secondary'))
        .margin({ top: 16 })
    }
    .width('100%')
    .height('100%')
    .justifyContent(FlexAlign.Center)
  }

  @Builder
  DiscoverContent() {
    Column() {
      Text('发现')
        .fontSize(24)
        .fontWeight(FontWeight.Bold)

      Text('发现功能开发中...')
        .fontSize(14)
        .fontColor($r('app.color.text_secondary'))
        .margin({ top: 16 })
    }
    .width('100%')
    .height('100%')
    .justifyContent(FlexAlign.Center)
  }

  @Builder
  StarredContent() {
    Column() {
      Text('收藏')
        .fontSize(24)
        .fontWeight(FontWeight.Bold)

      Text('收藏功能开发中...')
        .fontSize(14)
        .fontColor($r('app.color.text_secondary'))
        .margin({ top: 16 })
    }
    .width('100%')
    .height('100%')
    .justifyContent(FlexAlign.Center)
  }

  @Builder
  SettingsContent() {
    Column() {
      Text('设置')
        .fontSize(24)
        .fontWeight(FontWeight.Bold)

      Text('设置功能开发中...')
        .fontSize(14)
        .fontColor($r('app.color.text_secondary'))
        .margin({ top: 16 })
    }
    .width('100%')
    .height('100%')
    .justifyContent(FlexAlign.Center)
  }
}
```

**Step 2: 修改 EntryAbility 加载 MainPage**

```typescript
// entry/src/main/ets/entryability/EntryAbility.ets

import { AbilityConstant, ConfigurationConstant, UIAbility, Want } from '@kit.AbilityKit';
import { hilog } from '@kit.PerformanceAnalysisKit';
import { window } from '@kit.ArkUI';

const DOMAIN = 0x0000;
const TAG = 'EntryAbility';

export default class EntryAbility extends UIAbility {
  onCreate(want: Want, launchParam: AbilityConstant.LaunchParam): void {
    try {
      this.context.getApplicationContext().setColorMode(ConfigurationConstant.ColorMode.COLOR_MODE_NOT_SET);
    } catch (err) {
      hilog.error(DOMAIN, TAG, 'Failed to set colorMode. Cause: %{public}s', JSON.stringify(err));
    }
    hilog.info(DOMAIN, TAG, '%{public}s', 'Ability onCreate');
  }

  onDestroy(): void {
    hilog.info(DOMAIN, TAG, '%{public}s', 'Ability onDestroy');
  }

  onWindowStageCreate(windowStage: window.WindowStage): void {
    hilog.info(DOMAIN, TAG, '%{public}s', 'Ability onWindowStageCreate');

    // 加载主页面
    windowStage.loadContent('pages/MainPage', (err) => {
      if (err.code) {
        hilog.error(DOMAIN, TAG, 'Failed to load the content. Cause: %{public}s', JSON.stringify(err));
        return;
      }
      hilog.info(DOMAIN, TAG, 'Succeeded in loading the content.');
    });
  }

  onWindowStageDestroy(): void {
    hilog.info(DOMAIN, TAG, '%{public}s', 'Ability onWindowStageDestroy');
  }

  onForeground(): void {
    hilog.info(DOMAIN, TAG, '%{public}s', 'Ability onForeground');
  }

  onBackground(): void {
    hilog.info(DOMAIN, TAG, '%{public}s', 'Ability onBackground');
  }
}
```

**Step 3: 更新 module.json5 添加页面配置**

首先读取现有配置：
```bash
cat entry/src/main/module.json5
```

然后在 "pages" 数组中添加 MainPage：
```json
{
  "module": {
    "pages": "$profile:main_pages"
  }
}
```

创建或更新 `entry/src/main/resources/base/profile/main_pages.json`：
```json
{
  "src": [
    "pages/Index",
    "pages/MainPage"
  ]
}
```

**Step 4: 创建图标资源**

创建占位图标文件（实际项目中需要替换为真实图标）：
- `entry/src/main/resources/base/media/ic_subscription.png`
- `entry/src/main/resources/base/media/ic_subscription_filled.png`
- `entry/src/main/resources/base/media/ic_discover.png`
- `entry/src/main/resources/base/media/ic_discover_filled.png`
- `entry/src/main/resources/base/media/ic_star.png`
- `entry/src/main/resources/base/media/ic_star_filled.png`
- `entry/src/main/resources/base/media/ic_settings.png`
- `entry/src/main/resources/base/media/ic_settings_filled.png`

**Step 5: 创建颜色资源**

创建 `entry/src/main/resources/base/color.json`：
```json
{
  "color": [
    {
      "name": "primary",
      "value": "#007DFF"
    },
    {
      "name": "background",
      "value": "#FFFFFF"
    },
    {
      "name": "text_primary",
      "value": "#182431"
    },
    {
      "name": "text_secondary",
      "value": "#99182431"
    },
    {
      "name": "text_hint",
      "value": "#66182431"
    },
    {
      "name": "divider",
      "value": "#33182431"
    },
    {
      "name": "error",
      "value": "#E84026"
    }
  ]
}
```

**Step 6: Commit**

```bash
git add entry/src/main/ets/pages/MainPage.ets
git add entry/src/main/ets/entryability/EntryAbility.ets
git add entry/src/main/resources/base/profile/main_pages.json
git add entry/src/main/resources/base/color.json
git commit -m "$(cat <<'EOF'
feat: add MainPage with bottom navigation

- Add MainPage as main container with 4 tabs
- Initialize database and preferences on startup
- Update EntryAbility to load MainPage
- Add color resources for theming

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
EOF
)"
```

---

## Task 8: 构建验证

**Step 1: 运行构建命令**

```bash
hvigorw assembleHap --mode module -p product=default
```

Expected: Build successful with no errors

**Step 2: 运行 lint 检查**

```bash
hvigorw lint
```

Expected: No lint errors

**Step 3: 修复可能的编译错误**

如果出现缺失图标资源的错误，需要创建简单的占位图标。

---

## Summary

Phase 1 完成后，项目将具备：

1. ✅ 完整的目录结构
2. ✅ 数据模型定义 (Feed, Entry, Category, Settings)
3. ✅ 数据库层 (RDB + Repository)
4. ✅ 偏好设置存储 (Preferences)
5. ✅ 基础 UI 组件 (TabBar, EmptyState, LoadingView)
6. ✅ 主页面框架和导航

**下一步 (Phase 2):**
- 实现 RSS/Atom 解析器
- 完成订阅管理功能
- 实现分类管理
- OPML 导入导出