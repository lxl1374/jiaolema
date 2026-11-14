# 浇了吗（JiaoLeMa）

植物养护记录与浇水提醒应用。按 `docs/PRD.md` 实现，包含两份可交付物：

| 目录 | 是什么 | 怎么用 |
| --- | --- | --- |
| `pwa/` | 网页版 App（可添加到手机桌面） | 手机浏览器打开 `index.html` 即可用，无需构建 |
| `android/` | 原生 Android 工程（Kotlin + Compose + Room + 原生小组件） | Android Studio 打开构建 APK |
| `docs/` | PRD、业务规则、数据库设计、开发计划 | 开发与验收依据 |

> **装到手机上的一步步操作**（不需要懂命令行）：见 [`docs/INSTALL.md`](docs/INSTALL.md)。

## 一、先看能跑的东西（网页版）

`pwa/index.html` 是完整应用，纯前端、无依赖、数据存在浏览器本地。

**手机上最快的用法**

1. 把 `pwa/` 整个目录放到任意 HTTPS 静态托管（GitHub Pages、Vercel、Netlify、自己的服务器）或用手机浏览器直接打开 `index.html`。
2. 用 Chrome 打开，菜单 →「添加到主屏幕」，就是一个全屏图标 App。
3. 数据保存在本机浏览器，不上传、无账号、可离线。

> 用 `file://` 直接双击打开也能用，但部分浏览器会限制 `file://` 下的 localStorage 和 Service Worker，推荐托管或用一个本地静态服务器。

支持：多植物、分类、搜索、浇水记录、施肥记录、计划与暂缓、浇水状态、历史记录、编辑删除、每日提醒（页面存活时）、JSON 导出导入。

## 二、构建原生 APK

**方式 A：Android Studio（推荐）**

1. Android Studio（Hedgehog 以上）→ Open → 选择 `android/` 目录。
2. 首次同步会自动下载 Gradle 与依赖（需联网）。
3. 手机开「开发者选项 → USB 调试」，连上后点 ▶ Run 直接装到手机。
4. 或 Build → Build App Bundle(s)/APK(s) → Build APK(s)，产物在 `android/app/build/outputs/apk/debug/`。

工程里没有提交 `gradle-wrapper.jar`（二进制文件），Android Studio 会自动补齐；如果用命令行，先执行一次 `gradle wrapper`。

**方式 B：GitHub Actions 云端出包（没有本地环境时）**

1. 把整个仓库推到 GitHub。
2. 仓库 Settings → Actions → General，确认 Workflow 权限允许运行。
3. Actions 里手动触发 `build-apk`，完成后在 Artifacts 下载 `jiaolema-debug-apk`。

**环境要求**：JDK 17、Android SDK 34、Gradle 8.7、AGP 8.5.2、Kotlin 1.9.24。`minSdk 26`（Android 8.0）、`targetSdk 34`。

## 三、已实现的功能对照 PRD

- [x] 添加 / 编辑 / 删除植物
- [x] 每株独立浇水周期，可随时修改
- [x] 记录浇水（时间可改、水量、备注）
- [x] 自动计算下次浇水时间
- [x] 查看 / 编辑 / 删除历史浇水记录
- [x] 记录施肥（名称 / 时间 / 类型 / 剂量 / 备注），与浇水完全独立
- [x] 查看历史施肥记录
- [x] Android 系统通知提醒（每日汇总，AlarmManager + 开机恢复 + 降级）
- [x] Android 原生桌面小组件（单株 + 多株列表 + 快捷浇水）
- [x] 本地持久化（Room / localStorage）
- [x] 分类管理、搜索、筛选
- [x] 暂不浇水 / 忽略提醒 的独立语义
- [x] 数据导出 / 导入（网页版已实现；Android 版第一版依赖系统应用备份，JSON 导出/导入列为下一版）

## 四、文档

- `docs/PRD.md` — 产品需求
- `docs/BUSINESS_RULES.md` — 核心业务规则（改代码前必读）
- `docs/DATABASE.md` — 表结构、关系、删除规则、迁移
- `docs/DEVELOPMENT_PLAN.md` — 进度与已知限制

## 五、代码结构（Android）

```
android/app/src/main/java/com/jiaolema/
├── data/          Room 实体、DAO、数据库、Repository
├── domain/        纯函数计算引擎（周期计算 / 状态判定），可单测
├── ui/            Compose 页面、组件、主题、导航
├── notify/        AlarmManager 调度、通知、开机恢复
├── widget/        桌面小组件 + 快捷浇水 + 配置页
└── MainActivity.kt
```
