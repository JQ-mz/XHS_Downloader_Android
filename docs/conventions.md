# 代码约定 - 小红书下载器 Android

> **两条铁律**:①每节 `待答:` 列的是待决问题,不是规则——`TODO 待团队补` 填实后才约束代码,仍是 `TODO` 的节评审不得据引导文字判 blocker / cite 违规;②填实后删除该节 `待答:` 引导行;整节删除时引导文同步删除、节号保持连续。
> 与 `INDEX.md` 分工:本文件记规则本身,`INDEX.md` 记命令 / 工具 / 怎么强制执行(lint/build/CI),不在此重复。

## 一、代码风格

- Kotlin 优先;标识符、代码注释、日志、commit message 一律英文
- 面向用户的文案禁止硬编码,走字符串资源(见 §八)
- 遵循仓库现有 Kotlin / Compose / miuix 组织方式,优先复用已有组件,不超前抽象

## 二、命名

| 维度 | 规则 | 正例 |
|---|---|---|
| 包 / 模块 | 全小写,按分层与业务域命名 | `data.xhs`、`domain.download` |
| 类 / 接口 | UpperCamelCase,职责后缀(Repository / ViewModel / Coordinator / Parser) | `XhsDownloadCoordinator` |
| 方法 / 函数 | lowerCamelCase 动词开头 | `startDownload`、`enqueue` |
| 常量 / 配置项 | UPPER_SNAKE_CASE,置于 companion object 或文件级 private | `MAX_ATTEMPTS`、`TAG` |
| 资源(id / dimen / color / string) | string key 用 `{页面}_{描述}`,通用按钮用 `common_` 前缀 | `settings_media_format`、`common_confirm` |
| 测试类 / 方法 | 类名 `<被测类>Test`;方法名描述行为 | `XhsUrlParserTest` |

## 三、分层与依赖方向

- 依赖方向:`feature/` / `viewmodels/` → `domain/download` → `data/*` → `core/model`,由 `app/AppContainer` 组装
- 禁止 `data/` 反向依赖 UI 层;UI 不绕过 ViewModel / domain 直接访问 `data/` 仓库
- 详见 [architecture.md](architecture.md) 第四节

## 四、错误处理(含 null,统一收口本节)

- `CancellationException` 必须重抛,不得吞掉;其它异常捕获后走统一错误出口,失败不得误报成功
- 下载域失败用 `DownloadFailure` 密封类型表达(InvalidInput / Cancelled / Exhausted / RequiresWebView 等),由队列层映射为用户文案
- 对用户的失败提示走字符串资源的轻量反馈(Toast / 状态文案),不崩溃、不静默
- 可空返回仅在确有意义处使用;跨层边界解析失败显式建模,不靠 null 透传多层

## 五、日志(含脱敏,统一收口本节)

- 使用 `android.util.Log`;TAG 取类名或组件名常量(`private const val TAG`),日志正文一律英文
- 禁止明文打印 Cookie、凭据、完整用户隐私内容;手动 Cookie 仅本机加密保存
- 全局调试 TAG 为 `XHS_Debug`;组件级 TAG 清单见 [observability.md](observability.md)

## 六、并发与线程

- 全链路协程 + Flow;主线程禁止 DB / 网络 / 文件 I/O
- `TaskManager` 状态持有用 `SupervisorJob + Dispatchers.IO`,持久化写限定 `limitedParallelism(1)` 串行执行,避免乱序落盘
- Compose 层收集 Flow 一律 `collectAsStateWithLifecycle()`,不用 `collectAsState`
- 下载执行在前台服务 `DownloadService` 中;取消沿协程结构化并发传播,文件 / 网络资源必须释放

## 七、测试

- 可单测的纯逻辑(URL / 笔记解析、命名模板、任务映射、队列与选择逻辑、格式转换)必须配 JUnit 单测,落 `app/src/test`
- 依赖 Android 运行时的(MediaStore 落盘、实况合成、分享 Intent、通知、WebView 流程)走仪器化测试,落 `app/src/androidTest`
- 厂商相册对实况照片的识别以真机验证为准,验证边界记录于 [LIVE_PHOTO_COMPATIBILITY.md](LIVE_PHOTO_COMPATIBILITY.md)

## 八、其它(I18N / 文案)

- 所有面向用户字符串走 `stringResource(R.string.xxx)`(Composable)或 `appContext.getString(R.string.xxx)`(非 Composable),带动态内容用格式参数,不做字符串拼接
- 新增 / 修改字符串必须同步维护 `values`、`values-zh`、`values-zh-rTW` 三份 strings.xml,key 集合、占位符数量与类型保持一致

---

**最后更新**:2026-10-22
