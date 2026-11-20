# 术语表 - 小红书下载器 Android

> 本项目特有的业务概念、缩写、外部系统名称。新增需求、接口或模块时先对齐这里的术语。

| 术语 | 全称(中/英) | 释义 | 适用场景 | 关联模块 |
|---|---|---|---|---|
| 笔记 | Note | 小红书内容单元,含图片 / 视频 / 实况照片与文案 | 解析、下载、归档全链路 | `data/xhs`、`domain/download` |
| 实况照片 | Live Photo / Motion Photo | 静态图 + 短视频合成的动态照片,各厂商相册格式不一 | 下载、合成、相册识别 | `LivePhotoCreator`、`MotionPhotoContainer` |
| 选择下载 | Selective Download | 预览笔记媒体后勾选部分内容再提交下载 | 下载流程 | `viewmodels/MainViewModel`、选择面板 |
| 智能跳过 | Smart Skip | 资源记录匹配且文件仍可访问时跳过重复下载,默认关闭 | 下载去重 | `data/tasks`、`domain/download` |
| 网页提取 | Web Crawl / 从网页爬取 | okhttp 方式不支持的笔记,改用内置 WebView 加载并提取内容 | 解析兜底 | `WebViewActivity`、`data/network` |
| 网页会话 | Web Session / Cookie | 内置浏览器的小红书登录态,本机加密保存,可被网页提取与新任务复用 | 登录、网页提取 | `data/account` |
| 归档 | Archive | 按作者 / 笔记分目录与命名模板保存文件,可写入发布时间 | 存储落盘 | `data/storage` |
| 记录 | History / Records | 历史任务的搜索、筛选、删除与 JSON / CSV 导出 | 任务管理 | `data/tasks`、`feature/history` |
| 上游 | Upstream | JoeanAmier/XHS-Downloader,本项目解析与归档设计的参照来源 | 同步维护 | [`UPSTREAM_SYNC.md`](UPSTREAM_SYNC.md) |

## 别名 / 易混淆

> 同一概念在不同上下文有不同叫法,在这里对齐。

| 主名 | 别名 | 说明 |
|---|---|---|
| 小红书 | XHS / RedNote / xhs | 同一平台;域名有 xiaohongshu.com 与 rednote.com 两套 |
| 实况照片 | Live Photo / Motion Photo / 动态照片 | 苹果与安卓厂商命名差异,代码内多用 LivePhoto / MotionPhoto |
| 短链接 | xhslink | xhslink.com / xhslink.cn 分享短链,解析时先跳转还原 |
| 评论配图 | 评论图片 / Comment Images | 笔记数据中已加载的评论与回复图片,需在设置开启后保存 |

---

**最后更新**:2026-10-22
