## 前置学习条件
本篇不介绍一些非常细节的东西，因为这些东西可以直接在搜索引擎中通过关键词查到。  

如果觉得有不理解的地方，请直接使用搜索引擎辅助学习。



## 命令行入门
`win+R`打开程序`运行`，键入`cmd`后回车，即可打开CMD。

在运行中键入任意程序的路径名，即可打开对应程序，但`cmd.exe`却可以直接通过键入`cmd`打开，  
同样的，在cmd中键入`cmd`也可以直接打开cmd，不用想都知道这是为了方便，  
那么是如何做到方便的？其实很简单，系统里存了一个叫`path`的变量，如果没有在当前目录找到对应的程序，会在`path`中存的路径中寻找对应的程序。

`path`中可以存多个值，在设置里有GUI可以调整`path`的值。

在Windows的cmd中，变量是以字符串形式存储的，调用变量是直接将变量的位置替换为字符串，也就是`扩展变量`，  
这些变量通过用`%`括起来扩展，如`%path%`。

Windows中有很多内置命令，如`echo`，通过`/?`选项可以查看这些命令的帮助信息，如`echo /?`,  
可以看到这是个输出消息的命令，`echo %path%`即可看到`path`的值，  
通过`cmd /?`入门cmd的学习。

刚打开cmd时，cmd中的当前目录默认是你所使用的用户的目录，通过`cd`命令更改当前目录。

Windows提供了一种纯文本文件作为cmd运行的脚本，后缀为`bat`，意为批处理文件，  
新建一个txt文件，后缀改为bat，用记事本打开即可编辑，双击运行即可运行其中的命令，  
如果命令中含有非ASCII字符，如中文，需要将文件另存为ANSI编码格式，或者更改cmd的1编码格式。



## FFmpeg入门
如果你看过前面的文章，应该已经下载好了FFmpeg，  
现在将FFmpeg放在一个合适的目录，在`path`中添加ffmpeg.exe所在的那个目录的路径，  
OK，你可以在其他任意目录下运行FFmpeg了。  
键入`ffmpeg -version`查看当前版本，若成功即可正常使用。

键入`ffmpeg -h`查看help，虽然是英文的，但丢给翻译程序还是很容易看懂的(前提是有充足的音视频基础知识)。  
```
Getting help:
    -h      -- print basic options
    -h long -- print more options
    -h full -- print all options (including all format and codec specific options, very long)
    -h type=name -- print all options for the named decoder/encoder/demuxer/muxer/filter/bsf/protocol
    See man ffmpeg for detailed description of the options.
```
可以看到`-h`分多个级别，可供分层学习，与其在网上找教程，不如直接看help和问AI方便，  
用`-h`输出的文字看起来不方便，可以在doc文件夹中找到文档，也可以直接在官网在线查看。

值得注意的是，`-h type=name`这个用法非常好用，  
例如我想找一个边缘检测滤镜，使用`ffmpeg -filters`查看所有滤镜，  
我发现一个名为`edgedetect`可能是我需要的，使用`ffmpeg -h filter=edgedetect`查看详细信息。

`-h`输出的那短短几行字中就已经诠释了基本用法，但可能还是有理解障碍，所以这里写下一些基本的用法以及规律：  
* FFmpeg命令行通过`-i`输入文件，一次可输入多个文件，跟在一次输入后的路径名算作一次输出，  
  如`ffmpeg -i 1.mkv -i 2.mkv out.mkvq，输入了名为1和2的两个mkv文件，输出了一个out.mkv。
* 在输入前可以增加解码选项，输出前可以增加编码选项，
  如`ffmpeg -hwaccel vulkan -i 1.mkv -c:v hevc_qsv -i 2.mkv -map 0:v:0 -map 1:a:0 -c:v libx264 -preset slower -c:a libopus -b:a 128k output.mkv`，
  这行命令首先调用Vulkan API解码1.mkv，然后调用`hevc_qsv`解码器解码2.mkv，  
  使用`-map 0:v:0`将0号输入，即1.mkv中的v类型流，即video(视频)流中的0号流作为输出，  
  同时`-map 1:a:0`将1号输入，即2.mkv中的a类型流，即audio(音频)流中的0号流作为输出，  
  `-c:v libx264 -preset slower`将视频编码器设置为libx264，x264的preset选项设置为slower，  
  `-c:a libopus -b:a 128k`将音频编码器设置为libopus，音频码率设置为128k。



## 简单片源处理

