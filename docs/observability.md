# 可观测性索引 — 小红书下载器 Android

> 项目日志观测点索引:全局 TAG + 按业务场景归类的关键 TAG / 关键词。
> 填充:通用 TAG 清单用探查采样填(标 `(探查所得)`),采不到留空;外部 observer 未探查到则留空。

---

## 一、全局

| 项 | 值 |
|---|---|
| 应用包名 | `com.neoruaa.xhsdn` |
| 通用 TAG 前缀 | 无统一前缀,组件级 TAG 直接取类名;全局调试用 `XHS_Debug` |

### 通用 TAG 清单(探查所得)

| TAG | 归属模块 | 备注 |
|---|---|---|
| `XHS_Debug` | 全局 | 调试日志,出现频率最高 |
| `AppContainer` | `app/` | 依赖容器初始化 |
| `TaskManager` | `data/tasks` | 任务状态观察与持久化失败 |
| `DownloadQueue` | `domain/download` | 队列调度 |
| `XhsDownloadCoordinator` | `domain/download` | 单资源重试与回退 CDN |
| `XHSDownloader` | 根包 | 下载执行 |
| `FileDownloader` | 根包 | 文件下载 |
| `AndroidStorageSink` | `data/storage` | 落盘 / MediaStore |
| `LivePhotoCreator` | 根包 | 实况照片合成 |
| `WebAccount` | `data/account` | 网页会话 |
| `WebViewActivity` | 根包 Activity | 网页提取 |

---

## 二、业务场景

> 本节只保留骨架,由团队后续按业务场景增补;**不要从代码日志串推测预填场景**。

### 场景:<场景名>

- **相关模块**:<模块名>
- **关键 TAG**:<TAG1, TAG2>
- **关键词**:<特征串1, 特征串2>
- **说明**:<一句话描述该场景的典型异常信号>

---

## 三、外部 observer

> 非本项目进程,但日志正文会频繁提到本项目包名。

(未探查到)

---

**最后更新**:2026-10-22
