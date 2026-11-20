# 架构 - 小红书下载器 Android

> 顶层架构总览。**关注"长什么样"和"为什么这样切"**,具体接口签名留给 modules/ 与 iterations/。

## 一、技术栈

> 主语言 / 构建系统 / 构建命令 / 签名约束记 [`INDEX.md`](INDEX.md),不在此重复。

| 维度 | 选型 | 备注 |
|---|---|---|
| 框架 | Android,minSdk 24 / targetSdk 37 | 单模块 `:app` |
| UI 层 | Jetpack Compose + miuix 0.9.4 + Navigation3 | miuix 提供组件 / 模糊 / 图标;squircle 圆角 |
| 数据层 | Room3(任务与记录)+ DataStore Preferences(设置)+ OkHttp 5(网络)+ kotlinx-serialization(JSON) | Room schema 落 `app/schemas/` |
| 关键三方库 | kotlinx-coroutines 1.11、miuix 系列、capsule(模糊渲染) | 不无故新增依赖,新增需验证 minSdk 24 兼容 |

## 二、顶层目录结构

```
XHS_Downloader_Android/
├── app/              # 唯一应用模块:源码、资源、Room schema、测试
├── docs/             # 长期文档(本目录);iterations/ 为迭代临时产物,不入库
├── gradle/           # Gradle Wrapper 配置
└── scripts/          # build.sh 等本机辅助脚本
```

`app/src/main/java/com/neoruaa/xhsdn/` 内部分层:

```
com.neoruaa.xhsdn/
├── app/              # AppContainer:应用级依赖容器与初始化
├── core/model/       # 跨层共享模型(DownloadTask、NoteType 等)
├── data/             # 仓库层:xhs(解析)、network、storage、tasks、settings、account、media、update
├── domain/download/  # 下载域:DownloadQueue 调度、DownloadService 前台服务、XhsDownloadCoordinator
├── feature/          # 页面级 UI:detail / history / settings / update
├── viewmodels/       # MainViewModel 等 ViewModel
├── ui/               # 可复用 UI 组件与 navigation
└── utils/            # 工具类
```

## 三、模块清单

| 模块 | 职责 | 不负责 | 主要入口(文件/类) | 详情 |
|---|---|---|---|---|
| `:app` | 全部应用功能:解析、下载、存储、任务、设置、UI | —(单模块) | `XHSApplication`、`app/AppContainer`、`MainActivity` | 见第二节分层 |

## 四、模块依赖关系

单模块项目,约束体现在包级分层方向:

- UI 侧(`feature/`、`viewmodels/`、Activity)→ `domain/download` → `data/*` → `core/model`
- `app/AppContainer` 负责组装各层实现并注入
- 禁止反向:`data/` 不依赖 `feature/` / `viewmodels/`;UI 不绕过 ViewModel / domain 直接调 `data/` 仓库

## 五、关键运行时约束

- **线程 / 并发模型**:全链路协程 + Flow;主线程禁止 DB / 网络 / 文件 I/O;`TaskManager` 持久化写操作在 `Dispatchers.IO.limitedParallelism(1)` 上串行执行,保证顺序落盘;Compose 层一律 `collectAsStateWithLifecycle()`
- **进程 / IPC**:单进程;后台下载由 `DownloadService`(foregroundServiceType=`dataSync`)承载,无跨进程接口
- **关键生命周期 / 启动序**:`XHSApplication` 创建 `AppContainer` 并启动异步初始化;`TaskManager.init` 从容器接管 `TaskRepository`;进程被杀后未完成任务保留为暂停态,需手动继续
- **数据流主干**:链接输入(剪贴板 / 分享 / 手动)→ `data/xhs` 解析笔记 → 任务入队(`TaskManager` → Room)→ `DownloadQueue` 调度 → `XhsDownloadCoordinator` 做多候选 CDN 重试(每候选 3 次,退避 350ms)→ `FileDownloader` 下载 → `data/storage` 落盘 / MediaStore → 状态经 Flow 回到 UI

## 六、外部依赖与接口

| 对端 | 通信方式 | 用途 | 失败兜底 |
|---|---|---|---|
| 小红书 / RedNote 站点与 CDN(xiaohongshu.com、rednote.com、xhslink 短链、xhscdn.com) | HTTPS / OkHttp,可配 HTTP / SOCKS5 代理 | 笔记解析、媒体下载 | okhttp 不支持时走内置 WebView 网页提取;下载多候选 URL + 重试 |
| GitHub Releases | HTTPS | 启动时检查新版本 | 检查失败不打扰,可设置页手动查看 |
| Android MediaStore / SAF | 系统 API | 媒体与笔记信息落盘 | 自定义目录不可用时回退默认目录(Pictures/Movies/Download 下 `xhsdn`) |
| 系统分享 / 剪贴板 | Intent / ClipboardManager | 接收笔记链接 | 手动输入链接兜底 |

## 七、部署与产物

- **打包产物**:`app-debug.apk` / `app-release.apk`(release 用 `keystore.properties` 签名,未混淆)
- **CI / 流水线**:无,本机 `./gradlew` 或 `scripts/build.sh` 构建

## 八、已知约束与红线

> 这一节是"长期生效的硬约束",任何迭代都不许违反。

- 兼容 minSdk 24;新增依赖必须验证其兼容性并说明原因
- 面向用户的字符串一律走字符串资源,三语言(values / values-zh / values-zh-rTW)同步,禁止硬编码
- 协程 / 下载 / 文件 / MediaStore 操作必须正确处理取消、异常与资源释放;`CancellationException` 必须重抛,失败不得误报成功
- Cookie 等登录凭据仅本机加密保存,不参与记录导出,禁止明文打日志
- 不顺手重构、不超前抽象;修改范围以当前请求为准

---

**最后更新**:2026-10-22
