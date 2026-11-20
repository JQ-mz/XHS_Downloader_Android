# 项目文档索引 — 小红书下载器 Android

> 本文件是项目长期文档的**唯一入口**;任何外部引用(项目根 `README.md` 等)必须只指向本文件,不允许绕过 INDEX 直链 docs/ 子文档。

---

## 一、项目元信息

| 项 | 值 |
|---|---|
| 项目名 | 小红书下载器 Android(XHS_Downloader_Android) |
| 类型 | Android App |
| 主语言 | Kotlin |
| 构建系统 | Gradle(Groovy DSL),AGP 9.3.0,Gradle Wrapper 9.6.0 |
| 最低运行环境 | minSdk 24(Android 7.0);compileSdk / targetSdk 37;JVM 11 |
| 签名 / 凭据约束 | release 签名从项目根 `keystore.properties` 读取(文件不入库);debug 使用默认签名 |
| 代码风格 / lint | 未接入 lint 工具 |
| 提交规范 | Conventional Commits:`feat / fix / chore / refactor / opt(scope): 英文描述` |
| 其他全局约束 | 面向用户字符串禁止硬编码,values / values-zh / values-zh-rTW 三份 strings.xml 同步维护 |
| 代码索引(可选) | 定位 / 影响面分析:codegraph MCP 可用则 `codegraph_explore` 优先,缺则降级 grep / 读文件 |

---

## 二、研发常用命令

| 模块 / 场景 | 构建命令 | 单元测试命令 | 集成 / UI 测试命令 |
|---|---|---|---|
| **默认**(兜底) | `./gradlew assembleDebug` | `./gradlew test` | `./gradlew connectedAndroidTest` |
| **构建 gate**(必跑) | `./gradlew :app:assembleDebug` | — | — |
| `:app`(唯一模块) | `./gradlew :app:assembleDebug` | `./gradlew :app:testDebugUnitTest` | `./gradlew :app:connectedDebugAndroidTest` |

> `scripts/build.sh debug|release|install` 封装了带本机 JDK / SDK 环境变量的命令行构建。

---

## 三、长期文档导航

| 文档 | 管什么 |
|---|---|
| [`architecture.md`](architecture.md) | 仓库级:模块清单与职责边界 / 依赖方向 / 端到端数据流 / 跨模块运行时约束 |
| [`conventions.md`](conventions.md) | 项目级软性约定:命名 / 分层 / 错误处理 / 日志 / 并发 / 测试 |
| [`glossary.md`](glossary.md) | 跨模块共享术语 + 缩写 |
| [`observability.md`](observability.md) | 日志观测点索引:全局 TAG + 按业务场景归类的关键 TAG |
| [`decisions/`](decisions/) | 架构决策记录(ADR),文件名 `YYYY-MM-DD-<topic>.md`(暂无) |

### 自定义长期文档

| 文档 | 管什么 |
|---|---|
| [`LIVE_PHOTO_COMPATIBILITY.md`](LIVE_PHOTO_COMPATIBILITY.md) | 实况照片各厂商格式差异、开源参考与验证边界 |
| [`UPSTREAM_SYNC.md`](UPSTREAM_SYNC.md) | 与上游 JoeanAmier/XHS-Downloader 的同步策略与记录 |
| [`XHS_APP_MEDIA_RESEARCH.md`](XHS_APP_MEDIA_RESEARCH.md) | 小红书 App 媒体资源调研结论 |
| [`各类媒体资源笔记案例.md`](各类媒体资源笔记案例.md) | 各类型笔记的媒体资源样例 |
| [`20260128-下载失败笔记案例.md`](20260128-下载失败笔记案例.md) | 下载失败排查案例(2026-01-28) |
| [`小红书去水印工具迭代计划.md`](小红书去水印工具迭代计划.md) | 功能迭代计划 |

---

## 四、模块详情索引

> 单模块项目(`:app`),模块文档暂不拆分;内部结构与职责边界见 [`architecture.md`](architecture.md)。

| 模块 | 入口 |
|---|---|
|  |  |

---

## 五、架构决策记录(ADR)

> 只记录需要长期保留"为什么"的重大决策。暂无。

| 日期 | 主题 | 状态 | 文件 |
|---|---|---|---|
|  |  |  |  |

---

**最后更新**:2026-10-22　**维护方式**:随架构 / 约定 / 术语变化增量更新;新增模块构建命令时同步补充本文件。
