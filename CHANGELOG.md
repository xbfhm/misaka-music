# 更新日志 CHANGELOG

## [v3.1.2] - 2026-09-19（修复启动闪退的**真正根因**）

### 🐛 修复「点击图标直接闪退」

新加入的崩溃详情页给出了确切堆栈：

```
java.lang.NoSuchMethodException:
com.google.android.material.navigation.NavigationBarView.<init>
[class android.content.Context, interface android.util.AttributeSet]
```

- **根因**：`com.google.android.material.navigation.NavigationBarView` 是 Material 中的**抽象基类**（只提供 4 参数构造函数），**不能写在 XML 里 inflate**。AAPT2 不校验这一点，所以构建能通过，但一启动就抛 `InflateException`。
- **修复**：改用具体实现类 **`com.google.android.material.bottomnavigation.BottomNavigationView`** —— 这正是 Material Design 3 规范中「Navigation bar」的官方实现，继承自 `NavigationBarView`，`app:menu` / `itemIconTint` / `itemTextColor` / `labelVisibilityMode` 等属性完全通用，`MainActivity` 无需改动。

### 🔧 本地校验能力增强（防止同类问题再发生）

- 资源校验器新增 **View 类检查**：校验 XML 中的自定义 View 是否为**非抽象类**、且具备 `public (Context, AttributeSet)` 构造函数。
- 这类错误 AAPT2 完全不检查（只会在运行时崩溃），现已能在本地拦截。

---

## [v3.1.1] - 2026-09-19（修复构建失败 + 增强崩溃可诊断性）

### 🐛 修复「云端构建失败」

- `activity_log.xml` 中 `insetTop` / `insetBottom` 曾被误改为 `app:` 命名空间，AAPT2 报 `attribute insetBottom not found`。
- 这两个是 **Android 框架属性**，已改回 `android:` 命名空间。

### 🛡 预防性回退 + 崩溃可诊断性

- 将主题 `windowBackground` / `colorBackground` / `statusBarColor` 及 10 处页面根背景回退为具体颜色（颜色 attr 用在不期望 drawable 的系统属性上存在兼容风险）。
  > 说明：事后证明**闪退并非此项引起**（真凶见 v3.1.2 的 NavigationBarView 抽象类问题），此回退作为兼容性保险保留。
- **新增崩溃详情页**：出错时自动弹出并显示堆栈（系统主题 + 纯代码 UI，不依赖自定义资源），只有手机时可直接截图反馈。
- 崩溃堆栈同时写入 `Android/data/com.xbfhm.misakamusic/files/crash.log`。
- `DynamicColors` 初始化加 `try-catch` 兜底。

---

## [v3.1] - 2026-09-19（第十四轮：改用 Material Design + 启用 Material You）

### 🎨 底部导航栏改用 Material 3 标准组件
- 原手写的 `LinearLayout` + `ImageView` + `TextView` 导航栏，替换为官方 `NavigationBarView` + `@menu/menu_bottom_nav`
- 获得标准的 M3 选中指示器、涟漪反馈与切换动画
- 图标/文字着色改用 `@color/nav_item_tint` selector（选中亮色 / 未选中暗灰），保持原有明暗对比设计

### 🧩 控件 Material 化
- 日志页 2 个原生 `Button` → `MaterialButton`
- 搜索框原生 `EditText` → `TextInputEditText`

### 🌈 启用 Material You 动态取色
- 启动时调用 `DynamicColors.applyToActivitiesIfAvailable()`，Android 12+ 自动跟随系统壁纸取色
- 主题 `android:windowBackground` / `android:colorBackground` / `android:statusBarColor` 改为 `?attr/colorSurface`
- 10 处页面根布局背景由硬编码色改为 `?attr/colorSurface`，背景随主题联动
- Android 12 以下自动回退原有配色，视觉不变

### ✅ 保持不变
- UI 框架与布局结构完全未动
- 封面圆角、卡片、封面动态取色（封面 → 进度条/播放键）等设计保持原样

---

## [v3.0] - 2026-08-29（第十三轮：修复切歌无响应 + 播放引擎重写）

### 🐛 修复「播放歌单点下一首没反应」

- **根因**：播放引擎在顺序模式自然播完会暂停回开头，且切歌依赖 ExoPlayer 内部队列——增量加载未完成时队列里只有一首歌，导致「下一首」失效。
- **修复**：播放引擎改为**基于列表切歌**（不再依赖 ExoPlayer 队列）：
  - 「下一首」按当前播放模式在列表中精确定位切换，点击必响应
  - 顺序 / 循环模式自然播完自动连播（顺序模式播到最后一首才停止）
  - 随机模式改为洗牌袋算法（完全随机、不重复）
  - 「上一首」支持历史回退
  - 保留听歌打卡（scrobble）、收藏、定时关闭

### 🛠 其他

- 编译工具链持久化，避免云端构建重复下载 SDK（加速构建）
- 版本号升级至 v3.0（versionCode 3）

---

## [未发布] - 2026-08-28（第十二轮：UI 继续贴近 namida）

### ✨ 沉浸式 edge-to-edge
- 播放页、歌词页改为 edge-to-edge 沉浸式：封面背景延伸到状态栏后，内容顶栏自适应避开状态栏，全屏质感更强

### 🎨 迷你播放条动态取色
- 主页底部迷你播放条也跟随封面主色取色（进度条 + 播放键），与播放页视觉统一

---

## [未发布] - 2026-08-28（第十一轮：修复播放停顿）

### 🐛 修复「播放 10 秒停顿一下」

- **根因**：播放采用「单曲秒开 + 后台解析整队后 `setMediaItems` 一次性替换」的两阶段设计。整队 URL 解析（几十首歌）约 5~10 秒完成，随后 `setMediaItems` 在播放中 reset 播放器，导致正在播的歌卡顿一下。
- **修复**：改为「单曲秒开 + `addMediaItems` 增量追加剩余歌曲」，不再整队替换、不再二次 `prepare/play`，播放全程不打断。
- 顺序模式只追加后续歌曲不绕回；循环/随机模式追加后续 + 绕回补齐，保持原有播放语义。

---

## [未发布] - 2026-08-28（第十轮：同步播放历史/时长 + 修复编译错误）

### 🐛 修复云端打包编译错误
- 上一轮加的动画代码中，RecommendFragment / SearchFragment / PlaylistActivity 缺少 `R` 导入，导致 Kotlin 编译失败，已补齐
- 播放记录上报的定时循环用了 `isActive` 但漏了 `import kotlinx.coroutines.isActive`，导致编译失败，已补齐

### 🎵 新增播放记录/听歌时长同步（scrobble）
- 实现网易云官方「听歌打卡」接口 `/weapi/feedback/weblog`（对齐 NeteaseCloudMusicApi scrobble）
- 登录后播放会同步：**最近播放、听歌排行、累计听歌时长**
- 上报策略：播放中每 30 秒累计上报一次，暂停/切歌/停止时补记剩余时长，时长按「实际播放秒数」精确累计
- 来源 id 自动带上当前歌单（无来源传 0）
- 未登录不上报，网络异常静默失败不影响播放

---

## [未发布] - 2026-08-28（第九轮：改回红色 + 全量过渡动画）

### 🎨 颜色改回网易云红

- 主题主色从紫色调改回**网易云红 #EC4141**（Material You 红色系）
- 导航选中、播放中高亮、歌词高亮、进度条、按钮等全部回归红色

### ✨ 全量过渡动画（对标 Google Material Motion）

- **全局 Activity 过渡**：二级页面一律「右滑进入 / 返回左滑」，带淡入淡出
- **播放页底部弹出**：点击迷你播放条 / 歌单歌曲 → 播放页从底部滑入；返回时滑回底部（网易云/Spotify 式交互）
- **歌词页淡入淡出**：播放页封面 ↔ 歌词页 快速 cross-fade 切换
- **底部 Tab 切换**：Fragment 淡入淡出过渡
- 返回键与返回按钮统一动画（重写 `finish()` 覆盖系统返回键）

---

## [未发布] - 2026-08-28（第八轮：全面对齐 Namida 视觉风格）

### 🎨 主题色全面切换为 Namida 紫

- 全局主题从网易云红 `#EC4141` 切换为 Namida 的 **Material You 紫色调**（primary `#D0BCFF`）
- 导航选中色、播放中歌曲高亮、歌词高亮统一改为紫色
- 保留红色仅用于 VIP 标签、喜欢红心（语义色）

### 🏠 主页改造

- 顶栏品牌名用紫色强调，头像加紫色描边
- **迷你播放条升级为 Namida 底部播放栏**：封面（圆角矩形）+ 歌名 + **上一首 / 播放 / 下一首**三键
- 底部导航栏、迷你播放条卡片统一 `colorSurfaceVariant` 背景 + 圆角

### 📋 列表全面圆角化

- 歌曲列表、歌单列表、我的歌单列表的封面全部改为**圆角矩形**（10dp 圆角）
- 卡片式歌单项统一 16dp 圆角 + 无阴影（Namida 扁平简洁风）

---

## [未发布] - 2026-08-28（第七轮：播放器全面对齐 namida）

### 🎨 播放器彻底重做
- 移除黑胶，改为大圆角方形封面卡片 + 波形进度条（WaveSeekBar）
- 动态取色贯穿（封面主色 → 波形/播放键）

### 🐛 修复 AAPT2 构建失败
- 带点号的样式名 `ShapeAppearanceOverlay.Misaka.Rounded28` 改为 `CoverRounded28` 等
- WaveSeekBar 的 `Random(seed)` 类型错误修复

---

## [未发布] - 2026-08-28（第六轮：歌词页升级 + 修复随机播放封面错乱）

### 🐛 彻底修复随机播放封面/歌手信息错乱
- `PlayerEngine` 通过 `MediaItem.mediaId` 反查真实歌曲，绕过 ExoPlayer 洗牌索引错乱

---

## [未发布] - 2026-08-28（第五轮：关于页正式化）

### 📄 关于页正式化
- "需要感谢的人"改为"致谢"，剔除御坂美琴，免责声明条目式重写

---

## [未发布] - 2026-08-27（第四轮：UI 全面重做）

### 🎨 播放器 UI 重做
- 真实黑胶唱片、毛玻璃背景、动态取色

---

## [未发布] - 2026-08-27（第三轮：根治播放中断 + 日志）

### 🔧 播放中断根因修复 + 运行日志

---

## [未发布] - 2026-08-27（第二轮）
### 🎵 歌词修复 + 防盗链 Referer + 播放引擎重写

---

## [未发布] - 2026-08-27（第一轮）
### ❤️ 收藏反馈、音质标注、页面叠加修复
