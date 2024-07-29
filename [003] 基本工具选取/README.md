## 下载链接

* 基本工具
  * [Aegisub 分支下载](https://github.com/arch1t3cht/Aegisub/releases)
  * [FFmpeg 官网下载](https://ffmpeg.org/download.html)
  * [MediaInfo 官网下载](https://mediaarea.net/en/MediaInfo/Download)
  * [MKVToolNix 官网下载](https://mkvtoolnix.download/downloads.html)
  * [GPAC 官网下载](https://gpac.io/downloads/gpac-nightly-builds/)

* 预处理工具
  * [Python 官网下载](https://www.python.org/downloads/)
  * [VapourSynth 官网下载](https://github.com/vapoursynth/vapoursynth/releases)
  * [Vapour Synth Editor 官网下载](https://github.com/YomikoR/VapourSynth-Editor/releases)



## 工具介绍
大致介绍各工具的用处，供大家参考以按需下载

### Aegisub
编辑字幕的软件  
从效率的角度来看，各职位都应该掌握Aegisub的使用

### FFmpeg
处理媒体文件的必需品  
需要处理媒体文件的职位必须使用的  
其他职位没必要使用，但如果能掌握基本用法，可以提高效率

### MediaInfo
查看媒体文件元数据的必需品  
前提是你能看懂元数据  
建议所有职位安装

### MKVToolNix
封装mkv的必需品  
需要封装成品的职位必须使用的

### GPAC
内有工具MP4Box，其为封装mp4文件的必需品  
需要封装成品的职位必须使用的

### 预处理工具
预处理使用  
如果你不知道预处理是什么，不要安装  
这三个软件需要按顺序安装，并手动配置



## 工具使用
大致介绍如何使用各工具，让大家心里有个框架  
部分工具会在后面的教程中详细讲解

### Aegisub
主要用来编辑ass文件，这是一种字幕格式  
可以将ass文件的默认打开应用设置为Aegisub  
用Aegisub打开ass文件后，出现GUI界面，拖入视频、音频、关键帧文件即可载入  
载入相关文件后，出现视频预览窗口和音频可视化窗口  
在音频可视化窗口中操作鼠标，可以更改所选中的字幕行的时间轴到点击位置  
可以对各字幕行进行增删改查操作

### FFmpeg
FFmpeg为纯命令行工具，需要使用命令行调用  
`win+R`打开`运行`，键入`cmd`并回车，打开cmd  
`ffmpeg -h`即可查看所有操作，跟随各操作解释即可学会基本使用  
不了解命令行的新手建议先了解命令行的基础知识

### MediaInfo
安装后第一次打开时，出现设置界面，将语言设置为中文，视图(输出格式)设置为网页  
资源管理器中右击媒体文件，点击MediaInfo选项，即可查看元数据

### MKVToolNix
完全GUI界面，拖一拖文件就行

### GPAC - MP4Box
MP4Box需要使用命令行调用，所以同样需要先了解命令行基础知识  
先使用MKVToolNix封装，再使用MP4Box，即可在不了解MP4Box的相关选项的情况下完成封装  
`mp4box -add 输入视频.mkv -new 输出视频.mp4`即可封装
