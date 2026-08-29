# MisakaMusic

一款极简 Material Design 3 风格的安卓网易云音乐第三方客户端。

App 内部实现网易云 weapi / eapi 加密协议，**无需任何服务器**，直接与网易云音乐 API 通信。

图标为《某科学的超电磁炮》御坂美琴。

## 功能

### 登录
- Cookie 登录（推荐，从浏览器复制 MUSIC_U 即可）

### 歌单与搜索
- 我喜欢的音乐、我的全部歌单
- 搜索歌曲 / 搜索用户
- 收藏歌单、添加歌曲到歌单

### 播放
- 内置 ExoPlayer（Media3）播放，支持后台播放
- 状态栏通知 / 灵动岛 / 锁屏控制 / 蓝牙耳机控制
- 五种音质选择：Hi-Res / 无损 FLAC / 极高 320k / 较高 192k / 标准 128k，自动逐级降级
- 三种播放模式：顺序 / 循环 / 随机（洗牌袋算法，不重复）
- 波形进度条（Namida 风格）
- 歌词显示（原文 + 翻译，支持偏移校准）
- 定时关闭

### 个性化
- 动态取色：封面提取主色，进度条 / 播放键 / 迷你播放条跟随变化
- 自动连播
- 播放记录同步到网易云（最近播放 / 听歌排行）

## 技术栈

| 组件 | 技术 |
|------|------|
| 语言 | Kotlin |
| UI | ViewBinding + Material Components 3 |
| 播放 | Media3 / ExoPlayer |
| 后台 | MediaSessionService + 前台通知 |
| 网络 | OkHttp + 自实现 weapi / eapi 加密 |
| 图片 | Glide + Palette 取色 |
| 异步 | Kotlin Coroutines |

## 系统要求

- Android 6.0（API 23）及以上
- 无需额外权限（除通知权限用于后台播放）

## 构建

### GitHub Actions（推荐，无需电脑）

1. Fork 本仓库
2. 上传 `misakamusic.zip` 到仓库根目录（覆盖已有文件）
3. 自动触发 Actions 构建
4. 在 Actions 页面下载 APK

### 本地构建

```bash
# 需要 JDK 17 + Android SDK
gradle assembleDebug
```

APK 输出：`app/build/outputs/apk/debug/app-debug.apk`

## 项目结构

```
app/src/main/
├── java/com/xbfhm/misakamusic/
│   ├── App.kt                    # Application 初始化
│   ├── crypto/NeteaseCrypto.kt   # weapi / eapi 加密实现
│   ├── model/Models.kt           # 数据模型
│   ├── net/
│   │   ├── Http.kt               # OkHttp 封装
│   │   ├── NeteaseApi.kt         # 网易云 API 客户端
│   │   ├── Session.kt            # 登录态 / Cookie 管理
│   │   └── Device.kt             # 设备信息模拟
│   ├── player/
│   │   ├── PlayerEngine.kt       # 播放引擎（切歌/模式/上报）
│   │   └── PlaybackService.kt    # 后台播放服务
│   ├── ui/                       # Activity / Fragment / Adapter
│   └── util/Logger.kt            # 内存日志（调试用）
└── res/
    ├── layout/                   # 布局文件
    ├── values/                   # 颜色/主题/字符串
    └── drawable/                 # 图标/形状
```

## 致谢

- [NeteaseCloudMusicApi](https://github.com/Binaryify/NeteaseCloudMusicApi) — 网易云音乐接口协议参考
- [MikuBeat](https://github.com/Pafonshaw/MikuBeat) — Material Design 3 界面设计参考
- [Namida](https://github.com/namidaco/namida) — 播放器视觉风格参考
- [yesplaymusic](https://github.com/qier222/YesPlayMusic) — scrobble 上报方案参考

## 免责声明

1. 本软件**仅用于个人学习与技术研究**，严禁用于任何商业用途或牟利行为。

2. 本软件与网易云音乐（杭州网易云音乐科技有限公司）**无任何关联、合作、代理、授权或隶属关系**。项目名称、图标及界面均不构成任何形式的官方暗示或背书。

3. 本软件**不存储、不提供、不传播任何音乐文件**。所有音频均通过网易云音乐公开接口实时获取，歌曲、歌词、封面等相关内容的著作权及其他权利均归网易云音乐及相应权利人所有。

4. 用户通过本软件登录的账号、产生的播放记录及收藏等行为，均作用于用户本人账号。请勿使用本软件从事任何侵害他人权利、规避付费或违反相关服务条款的行为。

5. 网易云音乐接口属其平台资源，若接口发生变动、失效、风控或依据法律要求，本软件可能随时停止维护，开发者不承担由此产生的任何损失。

6. 本软件为开源项目，任何人可查看、修改及分发源码，但须保留本声明。因第三方修改版本所引发的问题，与原作者无关。

## 许可证

本项目基于 [MIT 许可证](LICENSE) 开源。

**注意**：MIT 许可证仅适用于本项目源代码。通过本软件访问的网易云音乐内容（歌曲、歌词、封面等）的版权归网易云音乐及相应权利人所有，不在本许可证覆盖范围内。
