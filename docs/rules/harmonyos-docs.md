---
name: harmonyos-docs
description: Reference HarmonyOS development documentation via NotebookLM. Use when developing HarmonyOS features, looking up ArkUI components, understanding APIs, or needing platform-specific guidance.
---

# HarmonyOS 开发文档参考

## ⚠️ 重要注意事项

**不要使用 `source_get_content` 读取文档内容，文档非常大（数十MB），会撑爆上下文！**

**正确用法：使用 `notebook_query` 提问方式获取信息**

```
mcp__notebooklm-mcp__notebook_query(
  notebook_id: "5c999611-ade3-4964-a026-9f579bb669af",
  query: "具体问题，如：如何在 HarmonyOS 中实现列表组件？"
)
```

## NotebookLM 资源

在 NotebookLM 中有完整的鸿蒙开发指南文档，位于 `Harmony_DEV` notebook。

**Notebook ID**: `5c999611-ade3-4964-a026-9f579bb669af`
**Notebook URL**: https://notebooklm.google.com/notebook/5c999611-ade3-4964-a026-9f579bb669af

## 文档列表 (21个)

| 文档名称 | 内容概述 |
|----------|----------|
| AI.md | AI 开发相关 |
| NDK开发.md | NDK 开发指南 |
| 一次开发，多端部署.md | 多端部署 |
| 优化应用性能.md | 性能优化 |
| 使用AI智能辅助编程.md | AI 辅助编程 |
| 发布应用.md | 应用发布 |
| 命令行工具.md | CLI 工具 |
| 图形.md | 图形开发 |
| 基础入门.md | 基础入门（39篇文档） |
| 媒体.md | 媒体开发 |
| 应用体验建议.md | 体验优化 |
| 应用开发准备.md | 开发准备 |
| 应用服务.md | 应用服务 |
| 应用框架-上.md | 应用框架上篇（760篇文档） |
| 应用框架-下.md | 应用框架下篇 |
| 应用测试.md | 测试指南 |
| 开发环境搭建.md | 环境搭建 |
| 构建应用.md | 构建应用（47篇文档） |
| 系统.md | 系统服务 |
| 编写与调试应用.md | 编写调试 |
| 自由流转.md | 自由流转 |

## 核心文档说明

### 基础入门.md
包含 39 篇文档，涵盖：
- 快速入门
- 开发基础知识
- 应用程序包基础知识（结构、开发使用、安装卸载更新）
- 应用配置文件（Stage模型、FA模型）
- 典型场景开发指导
- ArkTS 语言学习

### 应用框架-上.md
包含 760 篇文档，涵盖：
- Ability Kit（程序框架服务）
- Stage 模型开发指导
- UIAbility 组件
- ExtensionAbility 组件
- 应用组件生命周期
- 应用上下文
- UIAbility 组件间交互

### 应用框架-下.md
包含 ArkUI（方舟UI框架）相关内容：
- UI 开发（ArkTS 声明式开发范式）
- 文本输入组件（TextInput/TextArea/Search）
- 列表组件
- 网格组件
- 滚动组件
- 导航组件
- 自定义组件

### 构建应用.md
包含 47 篇文档，涵盖：
- 构建概述
- 配置文件
- 配置构建流程
- 多目标产物配置
- 定制构建选项
- 提升构建效率
- 扩展构建能力
- 构建报错排查

## 使用方式

使用 `notebook_query` 提问获取具体开发信息：

```
mcp__notebooklm-mcp__notebook_query(
  notebook_id: "5c999611-ade3-4964-a026-9f579bb669af",
  query: "如何在 HarmonyOS 中使用 List 组件？"
)
```

## 开发优先参考

开发 Flow 应用时，优先参考以下文档：

1. **基础入门** - 了解 HarmonyOS 应用开发基础
2. **应用框架** - UIAbility、ArkUI 组件使用
3. **构建应用** - Hvigor 构建配置
4. **应用测试** - Hypium 测试框架
5. **系统** - 网络请求、数据存储等系统服务