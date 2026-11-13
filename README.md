# typora-theme-github-bothview

GitHub 风格的 Typora 主题，支持 **BothView**：侧边栏图钉固定后，文件树与大纲左右同屏显示，编辑器自动居中。

**BothView 的现代化延续版**：在 XiLaiTL 原版 BothView 补丁（原仓库已下线）与早期文件树修复的基础上，适配新版 Typora 1.14.x / macOS（WKWebView 内核），并修复原版遗留的大纲 tab 显示 bug、左右面板宽度联动等问题，持续维护。

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

方式一：一键命令（克隆后自动拷入主题目录）

```bash
# macOS
git clone https://github.com/imkkdev/typora-theme-github-bothview.git /tmp/ttgb \
  && cp -R /tmp/ttgb/github-bothview.css /tmp/ttgb/github-bothview \
  ~/Library/Application\ Support/abnerworks.Typora/themes/ && rm -rf /tmp/ttgb
```

```powershell
# Windows (PowerShell)
git clone https://github.com/imkkdev/typora-theme-github-bothview.git $env:TEMP\ttgb
Copy-Item $env:TEMP\ttgb\github-bothview.css,$env:TEMP\ttgb\github-bothview "$env:APPDATA\Typora\themes" -Recurse -Force
Remove-Item $env:TEMP\ttgb -Recurse -Force
```

> 本主题在 macOS（WKWebView 内核）上实测；Windows 版 Typora 为 Chromium 内核，双栏布局未实测，欢迎反馈问题。

方式二：手动安装

1. 下载本仓库（Code → Download ZIP），或到 [Releases](https://github.com/imkkdev/typora-theme-github-bothview/releases) 下载主题包
2. 打开 Typora → 偏好设置 → 外观 → 打开主题文件夹
3. 把 `github-bothview.css` 和 `github-bothview/` 文件夹一起拷进去
4. 重启 Typora → 菜单「主题」→ 选择 **Github Bothview**

## 使用 BothView

1. 显示侧边栏（`Cmd + J`，或 视图 → 侧边栏）
2. 点击侧边栏底部的**图钉**图标把侧边栏固定
3. 完成：文件树和大纲分别显示在窗口两侧，中间为编辑区

## 相对原始 BothView 补丁的改动

BothView 双栏段基于长期使用验证过的版本（XiLaiTL 原版补丁 + 早期文件树定位修复），做了以下改动：

| 改动                                | 说明                                                                                                                                                                                         |
| :---------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 左右面板宽度独立                    | 双栏原本共用 `--sidebar-width`，拖左侧分界条右侧大纲会跟着变；现右侧改用独立变量 `--outline-panel-width`（默认 270px，在 CSS 顶部 `:root` 中修改），左侧面板宽度仍跟随分界条                 |
| 大纲 tab 大纲面板不可见（原版遗留） | 原版大纲 tab 用 `position: relative; left: 0`，但面板静态位在 x=侧栏宽处，整块垫在不透明编辑器下方；改为 `position: fixed; left: 0` 显式落位左栏，宽度跟随左栏分界条，文件搜索打开时自动让位 |
| 大纲 tab 右侧背景块整窗宽           | core 的 `.sidebar-content-content { min-width: 100% }` 把固定宽度的背景块撑到整窗宽并溢出窗口；补 `min-width: 0`                                                                             |

### 不要动这些（会复发文件树"楼梯状"漂移）

- 文件树缩进依赖 core 的 `display:table` 匿名表格嵌套 + 行内 float 图标，把树容器改成 `block` 后未清除的浮动会逐行级联，整棵树呈楼梯状漂移出面板
- 不要给 `.file-library-node` 等树节点加 `content-visibility` / `containment` —— WebKit 上会脱离 table 修复导致同样的漂移（Chrome 测不出，只有真实 Typora 能复现）

## 性能优化（卡顿）

| 隐患                            | 场景                                                     | 优化                           |
| :------------------------------ | :------------------------------------------------------- | :----------------------------- |
| `@include-when-export` 远程字体 | 导出 PDF/HTML 时拉取 fonts.loli.net，网络不佳会卡住导出  | 移除远程引用，导出回退系统字体 |
| `page-break-inside: avoid`      | 超过一页高的代码块让 WebKit 分页器反复回溯，PDF 导出挂死 | 代码块允许跨页                 |

## 致谢

- BothView 补丁原作者：**XiLaiTL**（MIT License）
- 基础主题：Typora 官方 GitHub theme

## License

MIT
