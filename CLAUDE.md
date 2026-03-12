# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

HarmonyOS 6.0.0 application using the Stage model architecture. Target SDK: 6.0.0(20).

- Bundle ID: `com.example.flow`
- Build system: Hvigor
- UI framework: ArkTS (ArkUI)

**Product Requirements Document**: [PRD.md](./PRD.md) - Flow 是一款 AI 驱动的信息聚合应用，类似 Folo，支持 RSS 订阅管理、智能摘要、翻译等功能。

## Build Commands

```bash
# Build the project
hvigorw assembleHap

# Build for debug
hvigorw assembleHap --mode module -p product=default

# Build for release
hvigorw assembleHap --mode module -p product=default -p buildMode=release

# Clean build
hvigorw clean
```

## Linting

```bash
# Run code linter (uses TypeScript ESLint + performance rules)
hvigorw lint
```

The linter enforces security rules for cryptographic operations (AES, RSA, DSA, ECDSA, DH, hash functions).

## Testing

Uses Hypium test framework. Tests are located in:
- `entry/src/ohosTest/ets/test/` - Instrumented tests (run on device)
- `entry/src/test/` - Local unit tests

```bash
# Run instrumented tests
hvigorw test --mode module -p product=default -p target=ohosTest
```

## Project Structure

```
├── AppScope/           # App-level configuration and resources
│   ├── app.json5       # App bundle name, version, icon
│   └── resources/      # App-wide resources (icons, strings)
├── entry/              # Main entry module (HAP)
│   ├── src/main/
│   │   ├── ets/
│   │   │   ├── entryability/    # Entry ability (app lifecycle)
│   │   │   ├── entrybackupability/  # Backup extension
│   │   │   └── pages/           # UI pages (Index.ets)
│   │   ├── module.json5         # Module config (abilities, pages)
│   │   └── resources/           # Module resources
│   └── src/ohosTest/            # Instrumented tests
├── build-profile.json5  # Build configuration
├── hvigorfile.ts        # Hvigor app-level tasks
└── oh-package.json5     # Dependencies (Hypium, Hamock)
```

## Architecture

- **EntryAbility**: Main entry point extending `UIAbility`. Handles app lifecycle (onCreate, onDestroy, window stage creation). Loads `pages/Index` as the main page.
- **Pages**: UI components decorated with `@Entry` and `@Component`. Use `RelativeContainer` for layout.
- **Resources**: Access via `$r('app.resource_name')` for strings, floats, colors, media.

## Key APIs

- `@kit.AbilityKit` - Ability lifecycle, Want
- `@kit.ArkUI` - UI components, window management
- `@kit.PerformanceAnalysisKit` - hilog for logging