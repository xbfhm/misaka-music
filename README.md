# MikuMusic 🎵

一款极简 **Material Design 3** 风格的安卓音乐播放器，直接对接**网易云音乐**接口（App 内部实现 weapi/eapi 加密协议，**无需任何服务器**）。

灵感参考：[MikuBeat](https://github.com/Pafonshaw/MikuBeat)（术力口播放器，MD3 风格）。

## 功能

- 🔐 **登录**
  - 扫码登录（网易云音乐 App 扫码，轮询确认）
  - 手机号 + 短信验证码登录
  - 登录态本地持久化，支持刷新
- 🎶 **歌单**
  - 一键获取「我喜欢的音乐」
  - 获取我的全部歌单，进入歌单点歌播放
- ▶️ **播放**
  - 内置 ExoPlayer（Media3）播放，支持 VIP 歌曲（需登录 VIP 账号）
  - 自动连播；音质逐级尝试 exhigh(320k) → higher(192k) → standard(128k)
  - 封面/歌词信息展示，进度条拖动
- 🔁 **三种播放模式**
  - 顺序播放：播完最后一首停止
  - 循环播放：列表循环
  - 随机播放：**完全随机且不重复**（洗牌袋算法，当前歌曲播完后随机选下一首并自动过滤已播放；全部播完重新洗牌）

## 技术栈

- Kotlin + ViewBinding + Material Components 3（MD3 主题）
- Media3/ExoPlayer 播放
- OkHttp 网络 + 自实现 weapi/eapi 加密（对齐 NeteaseCloudMusicApi 4.32.0）
- ZXing 二维码生成（扫码登录）
- Glide 图片加载
- 协程

## 构建

```bash
# 本地（需 Android SDK）
gradle assembleDebug

# 或用 GitHub Actions（见 .github/workflows/build.yml）
# 仓库根目录放 mikumusic.zip，自动解压构建，产物在 Actions 页面下载
```

APK 输出：`app/build/outputs/apk/debug/app-debug.apk`

## 说明

- 本项目仅用于学习交流，请勿商用；音乐版权归网易云音乐及权利人所有。
- VIP 歌曲需要登录一个**在有效期内的 VIP 账号**；网易云会不定期调整接口，如遇失效请等待修复或重新登录。
