# OBS Zoom to Mouse · OBS 32 兼容修复版

一个 OBS Studio 的 Lua 脚本：把「显示器采集」画面局部放大并跟随鼠标移动，适合录屏讲解、演示操作。

- 原脚本：[BlankSourceCode/obs-zoom-to-mouse](https://github.com/BlankSourceCode/obs-zoom-to-mouse)（版权归原作者）
- 本仓库：原版 1.0 + 针对 OBS 32.x 的兼容修复，修好后可直接在 OBS 32.2.2 上运行

## 本版本修了什么

### 1. 报错 `attempt to call field 'obs_sceneitem_get_info' (a nil value)`（原脚本第 556 行）

OBS 32 的 Lua 绑定把变换读写接口改名成了 `obs_sceneitem_get_info2` / `obs_sceneitem_set_info2`，旧名字不再导出。脚本里加了一层兼容：两套名字谁存在就用谁，所以 OBS 30 / 31 / 32 都能跑。

### 2. `obs.OBS_LOG_INFO` 变成 nil

OBS 32 只导出 `obs.LOG_INFO`。现在自动二选一，并加了空值保护，不会再因为一条日志把整个脚本顶掉。

### 3. 「还原变换」其实没还原（上游 bug）

卸载脚本或释放场景项时，原代码注释写着恢复原变换，实际调的却是 `get_info`（又从画面读了一遍），等于什么都没做。现在只在真的发生过 bounding box 转换时才用 `set_info` 还原。

## 安装

1. 下载 `obs-zoom-to-mouse.lua`（或下载 zip 解压）
2. OBS 顶部菜单 →「工具 / Tools」→「脚本 / Scripts」
3. 点「+」，选中 `obs-zoom-to-mouse.lua`
4. 在「缩放源 / Zoom Source」里选你的显示器采集源
5. 到「设置 → 热键」搜索 `Toggle zoom to mouse`，绑定开关键

## 主要参数

| 参数 | 作用 |
| --- | --- |
| Zoom Source | 要缩放的采集源，必须是「显示器采集」 |
| Zoom Value | 放大倍数，2 表示放大到 2 倍 |
| Auto Follow Mouse | 放大后自动跟随鼠标移动 |
| Follow Speed | 跟随速度，越小越平滑 |
| Follow Border / Safe Zone | 鼠标离边缘多近才开始移动画面 |
| Follow Auto Lock | 鼠标停下后画面自动居中锁定 |
| Enable debug logging | 在脚本日志里输出调试信息 |

## 常见问题

- **日志报 `obs_sceneitem_get_info (a nil value)`**：用的是旧脚本，换本仓库这份。
- **画面被裁掉 / 卡在放大状态**：右键采集源 →「滤镜」，删掉残留的 `obs-zoom-to-mouse-crop`。
- **启动日志出现一次 `Error opening file: (null)`**：OBS 启动那一刻读文件失败留下的空错误串，在脚本窗口点「重新加载脚本」即可，不影响功能。

## 已验证环境

- OBS Studio 32.2.2（64-bit / Windows）
- 脚本语法用该版本自带的 `lua51.dll` 编译校验通过
- 脚本内用到的 `obs.*` 接口已逐个对照该版本实际导出的 2067 个 Lua 函数核对

## 版权

脚本版权归原作者 BlankSourceCode 所有，本仓库仅分发兼容性修复版本，未额外声明许可。