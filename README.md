# 清盘侠 · DiskHero

> 专治 C 盘爆满。双击一下，把那些你根本不知道存在、却在偷偷吃掉几十 G 的垃圾清掉。

[![Windows](https://img.shields.io/badge/Windows-10%20%2F%2011-0078D4?style=flat-square)](#)
[![PowerShell](https://img.shields.io/badge/PowerShell-5.1%2B-5391FE?style=flat-square)](#)
[![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)](LICENSE)
[![Dependencies](https://img.shields.io/badge/依赖-零·免安装-brightgreen?style=flat-square)](#)

---

## 先说清楚：它是给谁做的

**给两类人：**

1. **不懂电脑的普通人。** 看见 C 盘飘红就慌，网上找教程又怕删错东西，最后只好重装系统。
2. **被 AI Agent 吃掉硬盘的人。** 你可能装了 Claude Code、Cursor、WorkBuddy、Codex、Ollama 其中某几个。它们每跑一次任务就往硬盘里写日志、缓存、会话轨迹、模型文件。**这些目录从不在「我的电脑」里显眼地出现，但一个月能长到几十 G。**

我见过最典型的一台机器：C 盘总共 154 G，光一个 `~/.workbuddy` 就占了 10 G，飞书内置浏览器的网页缓存 5 G，Chrome 的 Service Worker 缓存 2.7 G —— 加起来快 18 G，全是**删了会自动长回来**的东西。

这些不是用户的错。是这些软件的默认行为。

---

## ⭐ 只有一个入口

桌面上就一个 `清盘侠.cmd`，双击它就行。

以前那种「一个工具清日志、另一个工具清缓存、还有一个专门清某个软件」的局面已经合并掉了 —— 五个功能全在一个菜单里，不用记哪个管哪件事。

---

## 它长什么样

<p align="center">
  <img src="docs/images/01_主界面.png" width="700" alt="主界面">
</p>

整个软件就一个菜单，五个数字键。没有安装向导，没有广告，没有后台常驻，不联网。

---

## 三分钟用起来

**第 1 步：下载**

点右上角绿色 `Code` → `Download ZIP`，或者：

```bash
git clone https://github.com/<你的用户名>/qingpanxia.git
```

**第 2 步：解压到一个你找得到的地方**（比如 `D:\清盘侠`）

> ⚠️ 不建议放在 C 盘 —— 不然它一边帮你清 C 盘，一边占着 C 盘，有点滑稽。

**第 3 步：双击 `启动.cmd`**

会弹一个窗口问你要管理员权限，点「是」。

> 为什么要管理员？因为要清 `C:\Windows\Temp` 这种系统临时目录，还要在 C 盘建「目录联接」把日志引到别的盘。没有管理员权限，这两件事做不了。

然后照着菜单按数字就行。

---

## 五个功能

### [1] 体检 —— 看看谁在吃你的 C 盘

<p align="center">
  <img src="docs/images/02_体检排行榜.png" width="720" alt="体检">
</p>

**只读，绝对不删任何东西。** 扫一遍，把所有超过 200 MB 的目录排个名，AI 工具用黄色标出来。结果同时存一份 CSV 到 `报告\` 目录。

看完你就知道该清哪儿了。

### [2] 快清 —— 零风险清理（推荐）

<p align="center">
  <img src="docs/images/03_快清清单.png" width="560" alt="快清清单">
</p>

清的全是**能自己长回来的东西**：

| 类别 | 具体内容 |
|---|---|
| **AI 工具** | WorkBuddy 沙箱日志 / 会话轨迹、Claude 终端快照、Codex 缓存、Cursor 缓存、Gemini CLI、Aider、Continue 索引 |
| **开发缓存** | pip、npm、Yarn、pnpm、uv、HuggingFace 下载缓存、Playwright 浏览器包、Whisper、yt-dlp、Gradle、Maven |
| **浏览器** | Chrome / Edge / Firefox / 360 / QQ 浏览器的网页缓存、Service Worker、Code Cache |
| **常用软件** | 飞书内置浏览器缓存、微信旧日志、钉钉日志、豆包缓存、WPS 下载包 |
| **系统临时** | 用户 Temp、Windows Temp、PyInstaller 打包残留 |

**先列清单给你看，你确认了才动手。** 每一项都显示体积和完整路径，你可以自己去资源管理器里核对。

在我这台机器上实测：**18.03 GB / 30 处**。

### [3] 深清 —— 旧版本和残留（会先问你）

<p align="center">
  <img src="docs/images/04_深清清单.png" width="560" alt="深清清单">
</p>

- WPS 插件池（旧版本层层堆积，动辄几个 G）
- 同一个软件装了多个版本时，只留最新的那个
- 各种 `*updater\installer.exe` 更新包残留
- 下载了一半的模型残片 `.corrupt`
- NVIDIA 安装包缓存、金山毒霸组件

删完不影响使用，只是相关软件**第一次用到时会重新下载一次**。

### [4] 搬家 —— 治本的那个

<p align="center">
  <img src="docs/images/05_搬家.png" width="720" alt="搬家">
</p>

清缓存是治标。真正的病根是：**这些软件的日志目录写死在 C 盘，你清完它明天又长出来。**

搬家做的是：把整个目录挪到空间大的盘（D / E / F 自动挑最大的那个），然后在原位置留一个「目录联接」——

```
C:\Users\你\.workbuddy\logs   →   D:\DiskHero\WorkBuddy 日志
        （联接，程序以为文件还在这儿）
```

程序完全感觉不到区别，但文件实际睡在 D 盘。以后每天新产生的日志也全写 D 盘，C 盘再也不会被慢慢吃满。

可搬的目录：WorkBuddy 日志 / 轨迹 / 工作区、Claude 会话、HuggingFace 模型、Ollama 模型、npm 缓存、pip 缓存、Playwright 浏览器。

**已经搬过的会被认出来。** 如果你之前用别的工具搬过其中几个，按 `4` 时它们会标成灰色的「已搬」，只列还没搬的。它还会自动沿用你原来的存放位置，不会把东西摊到两个盘上。

> 搬家前请先关掉相关软件，否则文件被占用搬不动。搬不动时它会**拒绝建联接**（避免半截状态），关掉软件再跑一次就好。

### [5] 还原 —— 后悔药

搬完后悔了？按 5，全部搬回 C 盘，联接自动拆除。有记录，可精确还原。

---

## 安全边界：它绝对不碰这些

这一条比功能更重要。清盘侠的设计原则是「**只删能自己长回来的东西**」：

| 不碰 | 原因 |
|---|---|
| 文档、图片、视频、下载的文件 | 你的东西 |
| 微信 / QQ / 飞书的聊天记录、账号数据 | 删了找不回 |
| 浏览器书签、密码、历史、登录状态 | 只清缓存，不碰 Profile 数据 |
| 任何源码、项目文件、`node_modules` | 清了要重装依赖，很不友好 |
| 正在使用的程序文件 | 只删「旧版本」和「更新包」 |
| 注册表 | 一个字节都不改 |

另外：

- **不联网。** 没有任何网络请求，不上传任何东西。
- **不装东西。** 纯 PowerShell 脚本，Windows 自带，零依赖。
- **不常驻。** 不装服务、不开机自启、不留后台进程。用完就退。
- **源码全公开。** 你可以逐行读，也可以改。`core\QingPanXia.ps1` 就一个文件。

---

## 常见问题

<details>
<summary><b>双击没反应 / 一闪而过？</b></summary>

大概率是被 Windows 的「阻止来自 Internet 的文件」拦了。右键 `启动.cmd` → 属性 → 底部勾选「解除锁定」→ 确定，再双击。
</details>

<details>
<summary><b>被杀毒软件报毒？</b></summary>

脚本类工具被误报很常见，尤其涉及「删除文件」和「申请管理员权限」这两件事。

可以先上传到 [VirusTotal](https://www.virustotal.com/) 看一眼，或者直接读源码 —— 就一个 `core\QingPanXia.ps1`，纯文本，改成 `.txt` 用记事本就能打开。
</details>

<details>
<summary><b>清完发现某个软件要重新登录 / 变慢了？</b></summary>

清缓存后第一次打开确实会慢一点（要重新下载网页资源），这是正常的。如果某个软件行为异常，把「搬家」还原一下即可。
</details>

<details>
<summary><b>提示「还剩 N 项被占用」怎么办？</b></summary>

说明有程序正在用这些文件。把对应软件完全退出（记得托盘图标也要右键退出），或者干脆重启电脑，开机后第一件事就跑一次，那一次能清得最干净。
</details>

<details>
<summary><b>我没有 D 盘，能搬家吗？</b></summary>

不能。搬家本质是把东西挪到另一个物理位置，只有一个盘就没地方挪。这时只能用 [2] 快清和 [3] 深清，定期跑一次。
</details>

<details>
<summary><b>Mac / Linux 能用吗？</b></summary>

不能，这是 Windows 专用（用了目录联接、robocopy 这些 Windows 特性）。
</details>

---

## 实测效果

一台开发机的真实数据：

| 阶段 | C 盘剩余 |
|---|---|
| 开始 | 16.0 GB（飘红） |
| 清旧日志 + 包缓存 | 32.9 GB |
| 清浏览器 / 飞书 / 豆包缓存 | 38.4 GB |
| 把日志目录搬到 D 盘 | 43.5 GB |
| 再清旧版本、安装包残留 | **约 45 GB** |

从「随时可能爆」到「够用很久」，中间没有重装系统，没有删任何个人文件。

---

## 目录结构

```
清盘侠/
├── 启动.cmd                    ← 双击这个
├── core/
│   └── QingPanXia.ps1          ← 全部逻辑，一个文件，可读可改
├── docs/
│   ├── 使用说明.md             ← 详细图文说明
│   ├── 常见问题.md
│   └── images/                 ← 截图
├── 报告/                        ← 运行时自动生成：体检 CSV、清理记录
├── README.md
└── LICENSE
```

---

## 想自己改？

`core\QingPanXia.ps1` 里三份清单就是全部配置，照着加一行就能支持新软件：

```powershell
# 想清某个新软件的缓存，往 Get-SafeTargets 里加一行
@{ Cat = '常用软件'; Name = '某软件缓存'; Pat = "$L\某软件\Cache"; Kind = 'Purge' }
```

`Kind` 四种模式：

| Kind | 含义 |
|---|---|
| `Purge` | 清空目录内容，保留目录本身 |
| `OldItems` | 只删 N 天前的（配 `Days`） |
| `Files` | 删匹配到的文件 |
| `KeepNewest` | 目录下多个版本号文件夹，只留最新的 |

也可以命令行调用（适合放进计划任务）：

```powershell
# 体检
powershell -ExecutionPolicy Bypass -File core\QingPanXia.ps1 -Scan

# 快清（不带 -Real 就是预演）
powershell -ExecutionPolicy Bypass -File core\QingPanXia.ps1 -Clean -Real -Yes

# 搬家
powershell -ExecutionPolicy Bypass -File core\QingPanXia.ps1 -Move -Real -Yes
```

---

## 更新记录

**v1.1**（2026-10-20）
- 合并原先分散的「WorkBuddy 专用清理」「拼多多残留清理」两套工具，统一为一个入口
- 搬家功能会识别系统里**已经是联接**的目录，只列还没搬的，并沿用原有存放位置
- 搬家记录自动补录，即使用旧工具搬过也能被「还原」认出来
- 修掉一个要命的问题：非交互环境（计划任务/管道）下读不到确认输入时，菜单会死循环、确认会被误判成「是」

**v1.0**（2026-10-20）
- 首个版本：体检 / 快清 / 深清 / 搬家 / 还原

---

## 最后

如果它帮到了你，点个 ⭐ 让更多人看到。

如果你发现了新的「吃盘大户」，欢迎提 Issue 告诉我路径，我加进清单里 —— 这类目录实在太多了，一个人挖不完。

**License: MIT** —— 随便用、随便改、随便发，不用问。
