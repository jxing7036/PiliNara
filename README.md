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

### 方式一：GitHub Actions（无需本地环境）

把本仓库推到 GitHub 后，内置的 `.github/workflows/build.yml` 会自动完成全部流程：

手动触发：**Actions** 页 → **Build PiliNara (Z/X/C hotkey)** → **Run workflow**（每次跑都会拉取官方最新源码再构建）

产物在运行页的 **Artifacts**（`pilinara_zxc_windows_x64.zip`，便携版，解压即用）。
勾选 `create_release` 后还会发布到 GitHub Release 的 `nightly` 预发布。

### 方式二：本地脚本

**双击 `run.bat`**，或执行 `.\run.ps1`，自动完成全部流程：

1. 拉取/更新官方最新源码（首次自动克隆到脚本旁的 `PiliNara-source\`）
2. 应用补丁
3. 编译 Windows 版本（有 fvm 用 fvm，否则用系统 flutter）
4. 输出 exe 到 `release\pilinara.exe`

以后官方更新，**重新双击 run.bat 即可拿到带快捷键的最新版**。

编译完成后自动清理中间产物（`build\`），只保留：

- `release\pilinara.exe` —— 最终成品
- `PiliNara-source\` —— 官方源码（保留以便下次免克隆，可手动删除）

可选参数：

```powershell
.\run.ps1 -SourceDir D:\code\PiliPlus   # 用已有源码目录，不重新克隆
.\run.ps1 -NoCompile                    # 只更新源码+打补丁，不编译
```

### 环境要求

- git（https://git-scm.com/）
- 任意版本 Flutter SDK（仅作引导；脚本会自动安装 fvm，并自动下载项目要求的 Flutter 版本）
- Flutter Windows 构建环境（Visual Studio C++ 工具链）
- 首次运行：自动下载 Flutter 3.47.0（约 1-2GB）+ 首次编译，合计可能 15-30 分钟，之后仅需增量更新

## 仅给已有源码打补丁

```powershell
.\apply.ps1 D:\code\PiliPlus          # 优先 git apply，冲突时报错
.\apply.ps1 D:\code\PiliPlus -Force   # git apply 失败时强制覆盖 3 个文件
```

## 涉及文件（3 个）

```
lib/pages/video/widgets/player_focus.dart   # 按键分发 + 步进逻辑
lib/plugin/pl_player/controller.dart        # keyboardSpeedToast 状态 + 定时器
lib/plugin/pl_player/view/view.dart         # 倍速 toast UI
```

## 注意事项

- 官方后续更新若改动这 3 个文件，`git apply` 会失败并回退到覆盖方式
  → 若官方改过这些文件，直接覆盖会**丢失官方的新改动**，此时需手动合并
- 更新补丁包方式：官方版本变化后，在改好代码的仓库里重新生成（见下方）

## 如何重新生成补丁（功能没变就跳过）

在改好代码的仓库里：

```bash
git diff 上一个官方commit 当前HEAD > pilinara_zxc_speed_hotkey.patch
# 同时把 3 个改动文件复制进 files\ 对应路径
```

## 文件清单

```
run.bat                               # 一键构建入口（双击运行）
run.ps1                               # 一键构建脚本（自动更新源码+打补丁+编译+输出 exe）
apply.ps1                             # 仅打补丁脚本（给已有源码用）
pilinara_zxc_speed_hotkey.patch       # git 补丁
files\lib\...\*.dart                  # 修改后的完整文件（覆盖备用）
README.md
```
