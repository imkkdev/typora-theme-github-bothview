# typora-theme-github-bothview

GitHub 风格的 Typora 主题，支持 **BothView**：侧边栏图钉固定后，文件树与大纲左右同屏显示，编辑器自动居中。

适配 **Typora 1.14.x / macOS**（WKWebView 版）。

## 特性

- 完整的官方 GitHub 主题观感（标题分割线、引用块、表格、代码高亮）
- BothView 双栏：固定侧边栏后，「文件」和「大纲」两个 tab 下都是双栏布局
  - 文件 tab：左文件树 + 右大纲
  - 大纲 tab：左大纲 + 右文件树
- **左右面板宽度独立**：左侧拖动分界条只改变文件树宽度；右侧面板宽度由 CSS 变量 `--outline-panel-width` 控制（默认 270px，在 CSS 顶部 `:root` 中修改）
- macOS 优化：SF Pro 字体栈、系统强调色光标
- 字体（Open Sans woff2）随主题自带，不依赖官方 github 主题目录
- 与源代码模式兼容（`typora-sourceview-on` 时自动禁用双栏）

## 安装

1. 打开 Typora → 偏好设置 → 外观 → 打开主题文件夹
2. 把 `github-bothview.css` 和 `github-bothview/` 文件夹一起拷进去
3. 重启 Typora → 菜单「主题」→ 选择 **Github Bothview**

## 使用 BothView

1. 显示侧边栏（`Cmd + J`，或 视图 → 侧边栏）
2. 点击侧边栏底部的**图钉**图标把侧边栏固定
3. 完成：文件树和大纲分别显示在窗口两侧，中间为编辑区

## 相对原始 BothView 补丁的修复

| 问题 | 原因 | 修复 |
|:---|:---|:---|
| 文件树面板空白 | `#file-library > div` 的 `margin-left: -90%` 把树推出屏幕外 | 改为 `margin-left: 0` |
| 编辑器盖住两侧面板 | Typora 1.14 的 `content` 默认 `width: inherit`，`left/right` 约束失效 | 补充 `width: auto` 并显式 `bottom: 25px` |
| 大纲模式下右侧文件树被清空 | `#file-library div { display: none }` 把树全部隐藏，与 `display: block !important` 的本意矛盾 | 移除该规则 |
| 普通模式 header 异常 | `header { z-index: -1 }` 全局生效 | 限定为 `.pin-outline header` |
| 左右面板宽度联动 | 双栏共用 `--sidebar-width`，拖左侧分界条右侧大纲跟着变 | 右侧改用独立变量 `--outline-panel-width`（默认 270px） |
| 文件树"楼梯状"漂移出面板 | 文件树缩进依赖 `display:table` 嵌套 + 行内 float 图标；将树容器改为 block 后未清除的浮动逐行级联 | 保持 core 的 table 模式（CSS 内有注释警告，勿改 block） |

## 性能优化（大文件夹 / 卡顿）

| 隐患 | 场景 | 优化 |
|:---|:---|:---|
| `@include-when-export` 远程字体 | 导出 PDF/HTML 时拉取 fonts.loli.net，网络不佳会卡住导出 | 移除远程引用，导出回退系统字体 |
| `page-break-inside: avoid` | 超过一页高的代码块让 WebKit 分页器反复回溯，PDF 导出挂死 | 代码块允许跨页 |
| 双栏面板 `position: fixed` | core 部分状态给侧边栏加 `contain: strict`，fixed 包含块漂移 + 全窗口合成层 | 改为 `absolute`，几何以侧边栏为锚 |
| 文件树 `display: table` | 数千节点的自动表格布局开销极大 | 双栏模式改用 `block` 布局 |
| 悬停切换 `overflow-x` | core 在侧边栏悬停时切换 `auto/hidden`，每次悬停触发整树重排 | 双栏模式固定为 `auto` |
| 全量节点布局绘制 | 视口外的节点白白排版 | `.file-library-node` / `.file-list-item` 加 `content-visibility: auto`（WKWebView/Safari 18+ 生效，旧版本自动忽略） |

以上修复均通过无头 Chrome 加载 Typora 官方 `window.css` + 本主题 CSS，对 normal / pin+files / pin+outline 三种状态做了布局断言（面板 x 坐标、宽度、可见性）验证。

## 致谢

- BothView 补丁原作者：**XiLaiTL**（MIT License）
- 基础主题：Typora 官方 GitHub theme

## License

MIT
