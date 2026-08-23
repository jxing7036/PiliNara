# PiliPlus Z/X/C 倍速快捷键补丁包

给 PiliNara（PiliPlus）添加 **Windows 端 Z/X/C 快捷键快速调节播放倍速** 的独立补丁。

> 背景：上游作者尚未接受相关 PR，本补丁包让官方每次更新后无需手动改代码，直接一键构建。

## 功能

| 按键 | 行为 |
|------|------|
| **Z** | 恢复 1.0x 倍速 |
| **X** | 倍速 -0.1x（长按持续减速） |
| **C** | 倍速 +0.1x（长按持续加速，300ms 防误触后每 100ms 步进） |

- 倍速范围 0.1x ~ 6.0x，整数运算避免浮点精度累积误差
- 直播不生效；播放器内显示当前倍速 toast
- 仅桌面端（Windows）快捷键生效

## 一键构建（推荐）

### GitHub Actions（无需本地环境）

把本仓库推到 GitHub 后，内置的 `.github/workflows/build.yml` 会自动完成全部流程：

手动触发：**Actions** 页 → **Build PiliNara (Z/X/C hotkey)** → **Run workflow**（每次跑都会拉取官方最新源码再构建）

产物在运行页的 **Artifacts**（`pilinara_zxc_windows_x64.zip`，便携版，解压即用）。
勾选 `create_release` 后还会发布到 GitHub Release 的 `nightly` 预发布。


## 涉及文件（3 个）

```
lib/pages/video/widgets/player_focus.dart   # 按键分发 + 步进逻辑
lib/plugin/pl_player/controller.dart        # keyboardSpeedToast 状态 + 定时器
lib/plugin/pl_player/view/view.dart         # 倍速 toast UI
```

