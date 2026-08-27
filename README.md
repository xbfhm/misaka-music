# MisakaMusic 🎵

一款极简 **Material Design 3** 风格的安卓音乐播放器，直接对接**网易云音乐**接口。

App 内部实现了网易云官方加密协议（weapi/eapi），**无需任何服务器**即可登录、获取歌单并播放歌曲（含 VIP 歌曲）。

界面视觉参考：[namida](https://github.com/namidaco/namida)（播放器界面）、[MikuBeat](https://github.com/Pafonshaw/MikuBeat)（MD3 风格）；接口协议参考 [NeteaseCloudMusicApi](https://github.com/Binaryify/NeteaseCloudMusicApi)。

## ✨ 功能

### 🔐 登录
- 扫码登录（网易云音乐 App 扫码，轮询确认）
- 手机号 + 短信验证码登录
- Cookie 登录
- 登录态本地持久化，支持刷新

### 🎶 音乐获取
- 一键获取「我喜欢的音乐」
- 获取我的全部歌单，进入歌单点歌播放
- 搜索歌曲 / 用户

### ▶️ 播放
- 内置 ExoPlayer（Media3）播放，支持 VIP 歌曲（需登录 VIP 账号）
- 自动连播；音质逐级尝试 exhigh(320k) → higher(192k) → standard(128k)
- 后台播放（状态栏 / 灵动岛）

### 🔁 三种播放模式
- 顺序播放：播完最后一首停止
- 循环播放：列表循环
- 随机播放：完全随机且不重复（洗牌袋算法）

### 🎵 歌词
- 实时滚动歌词，点击封面切换歌词页
- 当前歌词高亮

### ❤️ 收藏 / 歌单
- 喜欢 / 取消喜欢（红心反馈）
- 收藏歌曲到指定歌单

### 📊 播放历史 / 时长同步（scrobble）
- 登录后播放会同步到网易云：**最近播放、听歌排行、累计听歌时长**
- 播放中每 30 秒上报一次，暂停 / 切歌 / 停止时补记剩余秒数
- 只统计真实在播时间（暂停不计时）

### ✨ 过渡动画（对标 Google Material Motion）
- 二级页面「右滑进入 / 返回左滑」
- 播放页从底部弹出、返回时滑回底部
- 歌词页淡入淡出
- 底部 Tab 切换淡入淡出

### ⚙️ 设置 / 关于
- 设置页：登录信息（昵称 / 头像 / UID）、VIP 信息（黑胶 VIP / 等级 / 到期时间）、退出登录
- 关于页：软件介绍、致谢、免责声明

## 🎨 视觉风格

- Material Design 3 主题
- 主色：网易云红 `#EC4141`
- 封面 / 卡片圆角化，扁平简洁风

## 🛠 技术栈

- Kotlin + ViewBinding + Material Components 3
- Media3 / ExoPlayer 播放
- OkHttp + 自实现 weapi/eapi 加密
- ZXing 二维码生成
- Glide 图片加载
- Kotlin 协程

## 📦 构建

```bash
# 本地（需 Android SDK）
gradle assembleDebug

# 或使用 GitHub Actions（见 .github/workflows/build.yml）
# 仓库根目录放任意命名的 zip，自动解压构建，产物在 Actions 页面下载
