# B站视频下载与检查指南

> 个人操作记录｜最后核对：2026-11-11｜Windows
>
> 本指南用于保存自己有权观看和保存的视频。充电专属内容需要账号具备该视频对应档位的观看权限；大会员画质和充电档位是不同的条件。工具及平台规则可能改变，使用前请查看上游项目的最新说明。

## 流程图

```mermaid
flowchart LR
    A[确认账号和视频权限] --> B[下载开源工具和 FFmpeg]
    B --> C[扫码登录]
    C --> D[先下载一集样本]
    D --> E{时长和音画完整}
    E -->|是| F[下载全部分集]
    E -->|否| G[检查试看和充电档位]
    F --> H[核对分集数、时长、分辨率]
    H --> I[按日期和内容整理]
```

## 本次使用的工具

| 工具 | 作用 | 官方来源 |
| --- | --- | --- |
| BBDownT | B站视频解析、扫码登录、多P下载 | [项目主页](https://github.com/LOVAHE/BBDownT) · [发布页](https://github.com/LOVAHE/BBDownT/releases) |
| yt-dlp | 替代下载工具，支持视频和分集合并 | [项目主页与安装说明](https://github.com/yt-dlp/yt-dlp) · [发布页](https://github.com/yt-dlp/yt-dlp/releases) |
| FFmpeg | 合并视频和音频、检查解码 | [官方项目](https://ffmpeg.org/) · [下载页面](https://ffmpeg.org/download.html) |

2026-11-11 实际使用 BBDownT 2.2.2、yt-dlp 2026.10.26 和本机已有的 FFmpeg。原 BBDown 仓库当时已归档；BBDownT 是其后续项目。工具的功能及版本会变化。

## 一、安装到 D 盘

1. 在 D 盘新建一个工具文件夹，例如 `D:\BiliTools`。
2. 从 BBDownT 官方发布页下载适合电脑架构的 Windows 程序（一般是 win-x64），放入该文件夹，可重命名为 `BBDownT.exe`。
3. 从 FFmpeg 官方下载页面进入 Windows 构建来源，下载压缩包，解压到 D 盘，例如 `D:\BiliTools\ffmpeg\bin\ffmpeg.exe`。
4. 终端中运行下面的命令，确认文件可用。命令示例适用于 Windows 命令提示符（cmd），路径不同请自行替换。

```bat
D:\BiliTools\BBDownT.exe --version
D:\BiliTools\ffmpeg\bin\ffmpeg.exe -version
```

不要随意从第三方网站下载同名程序。发布页如果提供校验值，可以对照下载文件的 SHA-256。

## 二、登录并确认权限

先在 B 站用相应账号打开目标视频：如果显示「试看中」或要求开通某个充电档位，下载工具通常只能获取试看内容。不要把试看当作完整版。

在命令提示符执行：

```bat
D:
cd /d D:\BiliTools
BBDownT.exe login
```

按提示用 B 站手机 App 扫描二维码并确认登录。登录文件由工具保存在本地；它包含账号会话，**不要上传 GitHub、发送给别人或贴到截图中**。未来登录流程或文件名可能变化，以项目说明为准。

## 三、先下载一个样本

把下面的示例 BV 号和输出目录替换为目标视频。`--ffmpeg-path` 应指向实际的 ffmpeg.exe：

```bat
D:\BiliTools\BBDownT.exe BV1FC4y1m7H8 --work-dir "D:\视频下载\样本" --ffmpeg-path "D:\BiliTools\ffmpeg\bin\ffmpeg.exe" --skip-cover --skip-subtitle -q "1080P 高清" -e "avc,hevc"
```

观察工具输出的「已选择的流」：确认清晰度、视频与音频都存在。播放生成的 MP4，并比较 B 站该集标注时长。个别源视频比例不是 16:9，例如平台的 1080P 档可能是 1920×960；不要只根据高度判断画质。

## 四、下载全部分集

多P视频建议先查看分集，再下载全部分集：

```bat
D:\BiliTools\BBDownT.exe BV1pDHi6hELJ --only-show-info --show-all
D:\BiliTools\BBDownT.exe BV1pDHi6hELJ -p ALL --work-dir "D:\视频下载\2026.10.04（考研英语作文）" --ffmpeg-path "D:\BiliTools\ffmpeg\bin\ffmpeg.exe" --skip-cover --skip-subtitle -q "1080P 高清" -e "avc,hevc"
```

将 BV 号和文件夹替换为自己的目标；`-p 1` 指定第 1 集，`-p 3-5` 选择第 3 至 5 集。分集数量应以当时页面为准。该工具的更新可能改变参数；运行 `BBDownT.exe --help` 查看当前帮助。

## 备用工具：yt-dlp

从 [yt-dlp 官方发布页](https://github.com/yt-dlp/yt-dlp/releases) 下载 Windows 可执行文件到 D 盘，并准备好 FFmpeg。先查看可用格式：

```bat
D:\BiliTools\yt-dlp.exe -F "https://www.bilibili.com/video/BV1FC4y1m7H8"
```

使用已授权账号时，yt-dlp 支持 Cookie 文件或读取浏览器登录状态；步骤见 [官方登录说明](https://github.com/yt-dlp/yt-dlp/wiki/FAQ#how-do-i-pass-cookies-to-yt-dlp)。Cookie 相当于登录凭证，必须保存在本机并妥善保护。浏览器正在运行时读取可能失败。本次通过扫码授权后使用 Cookie 文件成功下载了一个已有权限的托福系列；三个无对应档位的系列只返回试看。

下面的例子假设已安全准备本机 Cookie 文件，注意替换路径；不要把真实 Cookie 放在仓库中：

```bat
D:\BiliTools\yt-dlp.exe --cookies "D:\BiliTools\cookies.txt" --yes-playlist --merge-output-format mp4 --ffmpeg-location "D:\BiliTools\ffmpeg\bin\ffmpeg.exe" -f "bestvideo[height<=1080][ext=mp4]+bestaudio[ext=m4a]/best[height<=1080]" -o "D:\视频下载\%(playlist_index)02d %(title)s [%(id)s].%(ext)s" "https://www.bilibili.com/video/BV155Pez1ErW"
```

上面的命令可直接粘贴到 cmd；如果保存为 .bat 批处理文件，输出模板中的每个 `%` 应写成 `%%`。以当时官方文档及账号权限为准。下载完成后仍须逐集核对时长。

## 五、检查完整版

1. 对照页面，核对每个投稿的分集数和每集标题。
2. 播放开头、中间、结尾，检查声音和画面；比较每集实际时长与页面标注，注意秒数可能有少量取整差异。
3. 用 FFmpeg 完整解码检查；无错误信息且命令退出正常是有用的技术检查，但不能替代人工检查内容。

```bat
D:\BiliTools\ffmpeg\bin\ffmpeg.exe -v error -i "D:\视频下载\样本\视频.mp4" -f null -
```

可用 FFprobe 查看编码、分辨率和时长（如果下载的 FFmpeg 包中包含 ffprobe.exe）：

```bat
D:\BiliTools\ffmpeg\bin\ffprobe.exe -v error -show_entries format=duration:stream=codec_name,codec_type,width,height -of json "D:\视频下载\样本\视频.mp4"
```

## 六、问题排查

- **只有几分钟**：先看页面是否显示试看，确认账号已开通该视频对应充电档位；不能通过大会员代替充电权限。
- **HTTP 412**：平台拒绝了请求，原因可能不同。暂停连续重试，检查浏览器是否能正常播放、登录状态与项目上游 issue，再隔一段时间重试。不要把 412 一概当作没有观看权限。
- **没有声音或无法播放**：确认 FFmpeg 路径正确，文件包含视频和音频流；检查下载是否完成。必要时使用兼容 H.264 的播放器或选择 AVC 流重新下载。
- **系列少了几集**：打开原页面核对当前分P列表、选择范围和每集时长；合集可能后来更新。
- **画质低于预期**：确认工具选中的清晰度及账号可用画质；以平台实际提供的流为准。

## 七、2026-11-11 实际记录

- 按视频发布时间整理为 28 个「日期（主要内容）」文件夹，同一天不同投稿单独命名。
- 完成 80 个 MP4；示例考研作文系列共 11 集，托福写作方法与题库系列共 23 集，大英赛共 2 集。
- 以下三个系列当时页面显示需开通 50 元档包月充电，账号只能试看，因此对应文件夹仍为空：新托福邮件题（2026.05.05）、托福雅思同义替换词（2026.05.12）、托福学术讨论（2026.07.31）。档位和价格可能变化，以页面实时信息为准。
- 下载过程曾遇到 HTTP 412；另外一次得到的试看只有约 4 分钟，而页面完整集约 15 分钟。前者是接口错误，后者是权限问题，两者需要分别判断。

## 隐私与备份

本仓库只保存操作指南和公开项目链接，不保存视频、Cookie、二维码或账号令牌。临时登录文件及回收站中的副本都可能保留登录状态；处理时请留意。建议另外保存一份本指南的离线副本，并定期核对项目是否仍在维护。
