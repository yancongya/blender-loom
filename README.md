# Loom

Loom 是一个 Blender 插件，旨在简化渲染*图像序列*和*特定帧*的流程。

**原作者：** [p2or](https://github.com/p2or/blender-loom)  
**汉化作者：** [yancongya](https://github.com/yancongya)

---

### 目录

- [渲染图像序列对话框](#渲染图像序列-dialog-ctrlshiftf12)
- [批量渲染对话框](#批量渲染-dialog-ctrlshiftaltf12)
- [编码图像序列对话框](#编码图像序列-dialog-ctrlshiftf9)
- [实用工具](#实用工具)
- [注意事项与限制](#注意事项与限制)
- [插件偏好设置](#插件偏好设置)
- [安装](#安装)

## 渲染图像序列 Dialog <kbd>Ctrl</kbd><kbd>Shift</kbd><kbd>F12</kbd>

使用此对话框可以渲染相当复杂的**帧范围**、**单帧**、**子帧**以及**排除**某些帧和范围，而无需在后台（可选）操作*时间轴*。

<!-- ![Render Image Sequence Dialog](https://i.stack.imgur.com/ppHBr.jpg) -->
<img width="550" alt="渲染图像序列对话框" src="https://user-images.githubusercontent.com/512368/141286862-8e094f3f-4713-4089-a05c-80a7f87ad2a0.png">


例如，您可以输入 `1, 2, 3, 5-10` 来仅渲染这些帧。若要排除例如 `1-10` 范围中的第 `7` 帧，只需添加*插入符号*或*感叹号*后跟数字，如 `^7` 来渲染帧 `1-6, 8-10`（类似于在命令行中指定多个范围）。

### 示例

| 输入（范围）           | 输出（帧）                                              |
|:-----------------------|:-------------------------------------------------------|
| 1, 2, 3, 5-10          | 1, 2, 3, 5, 6, 7, 8, 9, 10                             |
| 1-5, 10-15             | 1, 2, 3, 5, 10, 11, 12, 13, 14, 15                     |
| 1-10 ^7                | 1, 2, 3, 4, 6, 8, 9, 10                                |
| 1-10 ^3,4              | 1, 2, 5, 6, 8, 9, 10                                   |
| 1-10 ^3-5              | 1, 2, 6, 8, 9, 10                                      |
| 1-10 ^3-5, 9           | 1, 2, 6, 8, 10                                         |
| 1-10 23-29 ^3-5, 7-9   | 1, 2, 5, 6, 10, 23, 24, 25, 26, 27, 28, 29             |
| 1-10x2                 | 1, 3, 5, 7, 9                                          |
| 1-10x2 10              | 1, 3, 5, 7, 9, 10                                      |
| 1-10x2 10 ^5           | 1, 3, 7, 9, 10                                         |
| 1-2x0.1                | 1.0, 1.1, 1.2, 1.3, 1.4, 1.5, 1.6, 1.7, 1.8, 1.9, 2.0  |
| 1-2x0.1 ^1.4, 1.5      | 1.0, 1.1, 1.2, 1.3, 1.6, 1.7, 1.8, 1.9, 2.0            |

您还可以通过在范围后添加 `x` 后跟任意数字来渲染场景的**每第 n 帧**，这可以表示*增量步长*。例如，`1-10x2` 渲染给定范围 `1, 3, 5, 7, 9` 的每隔一帧。通过这种方式，您可以通过指示浮点值（如 `x0.1` 甚至 `x0.01`）来渲染**子帧**以实现慢动作。

<!--
![Loom Slow Motion](https://i.stack.imgur.com/wOCMZ.gif)
`1-17x0.25` @25fps *(无运动模糊)*
-->


<img width="700" alt="Loom 慢动作" src="https://i.sstatic.net/wOCMZ.gif"><br>
`1-17x0.25` @25fps *(无运动模糊)*



### 选项



 - 您可以通过点击*验证输出*操作符 ![I1](https://i.sstatic.net/JovW0.jpg) 在渲染动画之前**验证输出**，该操作符报告所有将要渲染的帧（在*信息区域*以及终端中）。如果您在点击按钮时按住 <kbd>Ctrl</kbd> 或 <kbd>Cmd</kbd>，所有图像将单独输出到*信息区域*和终端 `1,2,3,4,5,7 ...`。
 - 当您点击秒表 ![stopwatch_small](https://user-images.githubusercontent.com/512368/182232254-882c38c5-6b77-46dc-8485-79697cabc02f.jpg) 时，Loom 会尝试基于常规*输出路径*找出**哪些帧缺失**并自动将它们添加到列表中。如果您在点击按钮时按住 <kbd>Ctrl</kbd> 或 <kbd>Cmd</kbd>，则强制使用时间轴的常规帧范围。
 - 您可以启用*过滤* ![I1](https://i.sstatic.net/lKqJq.jpg) 来单独处理插入符号 (`^`) 后的每个数字，这允许在**任何排除的帧之后添加单帧**或帧范围，例如 `1-10 23 ^3-7 4 6` 渲染 `1, 2, 4, 6, 8, 9, 10, 23` 而不是在该属性禁用时的 `1, 2, 8, 9, 10, 23`。

## 批量渲染 Dialog <kbd>Ctrl</kbd><kbd>Shift</kbd><kbd>Alt</kbd><kbd>F12</kbd>

批量对话框允许*渲染多个 .blend 文件*并*编码它们的输出序列*，通过命令行一次性完成。您可以扫描任何目录或手动添加 .blend 文件，重新排序它们并为每个文件指定渲染范围或单帧，类似于*渲染图像序列对话框*。

<!-- ![Loom Batch Dialog](https://i.stack.imgur.com/OSbdI.jpg) -->
<img width="700" alt="Loom 批量对话框" src="https://user-images.githubusercontent.com/512368/180617979-fad236e2-0a0f-4892-acad-3d0524b20284.png">


### 注意事项

 - 编码 <img width="22" alt="encoding-flag-disabled" src="https://github.com/p2or/blender-loom/assets/512368/645d1594-f20b-4f82-b1ee-c13761d812b7"> 是可选的，在**渲染**（路径追踪/光栅化）**完成后**进行
 - 执行此操作符会在 Blender 的脚本目录中生成一个**批处理文件**（`.sh` 或 `.bat`，取决于操作系统）并通过命令行逐个运行所有命令 - 您可以使生成的批处理文件成为您自己的并修改它们（为了便于访问，点击*插件偏好设置*中*删除临时批处理文件*操作符旁边的*磁盘图标*，打开系统上的脚本目录）
  - 如果您需要在对话框本身内有**更多空间**，或者例如您想查看**每个文件的路径**，您可以通过点击右上角的箭头来更改对话框中元素的外观以更改显示设置

    <!-- ![Loom Batch Display Menu](https://i.stack.imgur.com/MgHPk.jpg) -->


## 编码图像序列 Dialog <kbd>Ctrl</kbd><kbd>Shift</kbd><kbd>F9</kbd>

如果您的机器上安装了 [FFmpeg](https://www.ffmpeg.org/) 并在*插件偏好设置*中正确设置，此对话框允许将图像序列编码为 [ProRes](https://en.wikipedia.org/wiki/Apple_ProRes) 或 [DNxHD](https://en.wikipedia.org/wiki/Avid_DNxHD)/[DNxHR](https://en.wikipedia.org/wiki/DNxHR_codec) 以用于预览或布局目的。您可以选择任何图像序列，设置输出色彩空间（用于编码线性 exr 序列很有用）、帧率和所需的编解码器。

<!-- ![Encode Image Sequence Dialog](https://i.stack.imgur.com/ILENa.jpg) -->

<img width="700" alt="编码图像序列" src="https://user-images.githubusercontent.com/512368/180618261-979aac90-a906-448c-9e9c-09028db4b50f.png">

### 选项
 - 选择 `ProRes 4444 XQ` 以获得**Alpha 通道**
 - 您可以在编码之前通过点击*验证图像序列*操作符 ![I1](https://i.sstatic.net/JovW0.jpg) **验证磁盘上的图像序列**，该操作符还会检测序列中缺失的所有帧
 - 如果**缺失帧**，插件提供一个实用功能来用前一帧（最近）的副本来填充图像序列的间隙以获得完整长度的动画，另一个操作符来渲染所有缺失的帧。如果您只想填充给定序列的间隙，启用*忽略场景范围*属性 <img width="18" alt="Screenshot 2022-12-15 at 08 32 33" src="https://user-images.githubusercontent.com/512368/207799961-29fb9796-941b-48dd-8ea0-e5c22a94e1e9.png">

   <!-- ![Encode Image Sequence Dialog](https://i.stack.imgur.com/ul9ld.jpg) -->


## 实用工具

Loom 还包括一些在生产中很有用的实用工具，例如显示最终*输出路径*、常规*输出面板*中所有*文件输出*节点的列表、项目文件夹的创建等。


### 渲染版本

一旦您在*输出路径*中添加 `v` 和一些任意数字，*输出区域*中就会出现一个新的"滑块"，允许即时更改实际版本字符串。

<!--
![Render Version](https://user-images.githubusercontent.com/512368/180618398-cb566e95-53cd-4f37-ad1d-6aeed1b184d6.gif)
-->

<img width="700" alt="渲染版本" src="https://user-images.githubusercontent.com/512368/180618398-cb566e95-53cd-4f37-ad1d-6aeed1b184d6.gif">

如果您想移除*文件输出*节点中的所有版本字符串，按 <kbd>F3</kbd> 并输入 `Remove version str...`

### 渲染特定关键帧

您可以通过在*时间轴*、*摄影表*或*曲线编辑器*中选择关键帧来**渲染特定关键帧**，并使用每个区域标题中的弹出窗口调用*渲染所选关键帧*操作符，该操作符会自动将帧添加到渲染列表中。

<!--![Render Selected Keyframes](https://i.stack.imgur.com/PUs71.jpg) -->
<!-- <img width="608" alt="popover" src="https://user-images.githubusercontent.com/512368/141284257-1f380f00-feb4-40eb-9f64-64df67903edb.png"> -->
<img width="700" alt="Loom 时间轴弹出窗口" src="https://user-images.githubusercontent.com/512368/141284381-80695c90-a6cb-4dcd-9f12-d068ec16b8f0.png">

#### 选项

 - 如果_限制到对象选择_切换 <img width="16" alt="limit_by_object-in-selection" src="https://user-images.githubusercontent.com/512368/204095616-19d16715-91be-4210-81b1-d30eb263d3bb.png">
启用，则只有场景中所选对象的关键帧会添加到列表中。
 - 在点击"渲染所选关键帧"按钮时按住 <kbd>Ctrl</kbd> 会将所有关键帧添加到列表中，无论在*时间轴、曲线编辑器或摄影表*中选择了什么。
 - 在点击"渲染所选关键帧"操作符时按住 <kbd>Alt</kbd> 或 <kbd>Option</kbd> 会将关键帧的选择限制在场景的当前_帧范围_内。
 - 如果在点击"渲染所选关键帧"操作符时按住 <kbd>Ctrl</kbd> 和 <kbd>Alt</kbd> 或 <kbd>Option</kbd> 且未选择任何对象，则_帧范围_内的_所有关键帧_都会添加到列表中。

### 文件路径变量

Loom 允许通过*自定义 Python 表达式*替换*插件偏好设置*中定义的任何"全局变量"的所有出现，用于常规*输出路径*以及所有*文件输出*节点。

<!--
![globals](https://user-images.githubusercontent.com/512368/180618964-b4d6ef0c-c542-4925-be40-3e6b65cb9172.gif)
-->

<img width="700" alt="全局变量" src="https://user-images.githubusercontent.com/512368/180618964-b4d6ef0c-c542-4925-be40-3e6b65cb9172.gif">

#### 注意事项

- 仅当 Loom 用于渲染时（例如使用 <kbd>Ctrl</kbd><kbd>Shift</kbd><kbd>F12</kbd>），替换才有效。
- 用于演示目的的默认值包括 `$BLEND`、`$F4`、`$SCENE`、`$CAMERA`、`$LENS`、`$MARKER`、`$COLL` 等。
- 要自定义提供的用于每日样片的时间变量，请参阅 [`strftime()`](https://docs.python.org/3/library/datetime.html#strftime-and-strptime-format-codes) 的官方文档以获取所有选项。

<!-- <img width="736" alt="Globals" src="https://user-images.githubusercontent.com/512368/180612932-93a1882c-06f3-4016-a2f7-13fa61c78dce.png"> -->

### 渲染翻书动画 <kbd>Ctrl</kbd><kbd>Shift</kbd><kbd>F10</kbd>

使用此对话框可以基于给定的帧、帧范围或子帧来 [渲染视口](https://docs.blender.org/manual/en/latest/editors/3dview/viewport_render.html#viewport-render)，而无需操作*时间轴*，类似于 _视图 > 渲染视口动画_。由于渲染视口非常快，这对于布局目的、预览子帧以及利用 [实时合成器](https://docs.blender.org/manual/en/latest/compositing/realtime_compositor.html) 非常有用。

<img width="700" alt="渲染翻书动画对话框" src="https://github.com/p2or/blender-loom/assets/512368/5c02a537-343a-4240-b5bf-6732fc9b9b86">


### 渲染预设

Loom 允许为所有必要的渲染属性存储自定义预设。您可以设置引擎、采样、输出路径、图像属性等，并将所有设置保存为*属性区域*的*标题*中的新自定义"预设"以供重用。

<!--<img width="700" alt="Screenshot 2022-12-11 at 18 36 42" src="https://user-images.githubusercontent.com/512368/206920653-6de7400f-f9b0-4382-bce5-085415534c2d.png">-->

<img width="700" alt="Screenshot 2023-01-14 at 14 04 22" src="https://user-images.githubusercontent.com/512368/212472944-9fcdb914-2d21-480d-bc98-9d3100cb11f0.png">


如果存在预设，当启用"使用命令行渲染"时，"图像序列对话框"中以及当启用"覆盖渲染设置"时"批量对话框"中会出现一个新的下拉菜单，这允许您使用自定义预设渲染当前场景或文件，而不管实际的渲染设置如何：

<!-- <img width="521" alt="Screenshot 2022-12-11 at 18 38 19" src="https://user-images.githubusercontent.com/512368/206921276-28bb5bc0-64ba-4bcd-8e65-4b9963e52bf2.png"> -->

<img width="700" alt="渲染预设下拉菜单" src="https://user-images.githubusercontent.com/512368/212472717-63c19b49-4e6d-4898-ba16-33777a397105.png">

### 标记

Loom 在*时间轴的标记菜单（时间轴 > 标记）*中添加了三个新的操作符。您可以基于视口中的所选相机**生成标记**、**从相机解绑标记**或使用自定义名称或*插件偏好设置*中定义的*全局变量*来**批量重命名**它们。

<img width="700" alt="批量重命名标记" src="https://user-images.githubusercontent.com/512368/180614748-98ba2f5f-cb67-4c88-9714-5b63f1d09684.png">

### 元数据变量

*插件偏好设置*中的所有预定义变量（例如 `$CAMERA`、`$F4` 或 `$LENS`）可用于元数据面板的_注释_字段以进行预览。为了格式化文本并使其更易于阅读，支持换行符 (`\n`) 和空格。

<img width="700" alt="元数据变量" src="https://github.com/user-attachments/assets/8c231274-552b-4f9e-91ba-caa116507c9c" />

### 重命名文件序列 <kbd>Ctrl</kbd><kbd>Shift</kbd><kbd>F2</kbd>

使用此对话框可以重命名磁盘上的任何任意图像或文件序列。

<img width="700" alt="重命名文件序列" src="https://user-images.githubusercontent.com/512368/180613687-fc71ca7b-a415-45b1-bc27-db4332d76603.png">

### 项目设置 <kbd>Ctrl</kbd><kbd>Shift</kbd><kbd>F1</kbd>

使用此对话框可以为当前项目创建所有相关的主文件夹，并自动将*输出路径*设置到渲染文件夹。

<img width="700" alt="项目设置" src="https://user-images.githubusercontent.com/512368/180613021-0046c4b8-4ef6-4a10-bcef-f4a87660421e.png">


<!--
### 镜头范围

使用此对话框可以快速设置任何帧范围，并且设置可以重用于多个项目。

<img width="350" alt="Project Setup" src="https://user-images.githubusercontent.com/512368/182233951-b196ce0c-2d03-4ca6-92fa-707993aaee89.jpg">
-->

## 插件偏好设置

可用的设置因操作系统而异。但是，您可以设置*每个对话框的大小*，设置*ffmpeg 二进制文件的路径*，访问以及移除*批处理文件*和*编辑快捷键*在所有操作系统上。


| 属性                                | 描述                                            |
|:----------------------------------|:-------------------------------------------------------|
| *时间轴扩展*             | 允许在*时间轴*中打开或关闭"Loom 弹出窗口"和"镜头范围"对话框的显示 |
| *输出面板扩展*         | 允许在*所有文件输出节点*和最终*输出路径*的显示（*属性 > 输出属性 > 输出*）  |
| *播放预览（实验性）*        | 允许使用 <kbd>Shift</kbd><kbd>Ctrl</kbd><kbd>F11</kbd> 快捷键播放最新渲染的图像序列（需要在保存*用户偏好设置*后重启 Blender） |
| *默认动画播放器*        | 强制为*播放预览*操作符使用默认*动画播放器（用户偏好设置 > 文件 > 动画播放器）* |
| *FFmpeg 二进制文件路径*           | 仅当尚未包含在您的 Linux 发行版中或未添加到环境变量时才需要。点击「自动检测」按钮可自动查找插件目录或脚本目录中的 FFmpeg，或点击「下载 FFmpeg」前往官方下载页面 |
| *强制生成 `.sh` 或 `.bat` 文件* | 为所有命令行操作生成批处理文件，即使是单个操作 |
| *删除临时 `.sh` 或 `.bat` 文件* | 移除在*Blender 脚本目录*中找到的所有生成的批处理文件 |
| *Xterm（终端回退）*       | 如果不支持系统终端，则作为所有命令行操作的回退，[Xterm](https://en.wikipedia.org/wiki/Xterm) 可用于大多数*Linux 发行版*和*旧版本的 MacOS* |
| *重置偏好设置*       | 将所有属性重置为默认值（ffmpeg 的二进制路径除外） |


## 注意事项与限制

 - Loom 不支持直接编码，确保*文件格式*设置为*图像*。
 - 激活*播放预览*快捷键需要重启 Blender。
 - 如果由于某种原因编码失败，请确保 ffmpeg 二进制文件的路径是 [绝对路径](https://en.wikipedia.org/wiki/Path_(computing))。
 - 热键*一旦完全移除*后更新，需要重置*插件偏好设置*并重启 Blender。
 - 在某些情况下，从*Xterm*切换回默认系统终端需要重置*插件偏好设置*。


## 安装

 1. 下载 [最新版本](https://github.com/p2or/blender-loom/releases/)
 1. 在 Blender 中打开*用户偏好设置 > 插件*
 1. 点击*从文件安装*，选择 `loom.py` 并激活插件
 1. 保存*偏好设置*并重启 Blender

----

欢迎对 *Loom* 做出贡献。已在 *Arch Linux 2017+*、*Ubuntu 16.04+*、*MacOSX 10.6.8+*、*Windows 7+* 上成功测试。
