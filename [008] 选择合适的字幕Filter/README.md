## 什么是字幕Filter

字幕Filter，这里的 `Filter` 可以翻译成滤镜、解码器、渲染器，它的作用是解析字幕文件，输出为画面。  
字幕文件是纯文本文件，Filter 读取文件，解析这个文件里需要渲染哪些画面，将画面输出给下游(下一位)渲染器，如 madVR，渲染器再将画面渲染到屏幕上。

所以不同的 Filter，可能会导致渲染出来的画面不同，以至于必须统一 Filter，也就是说必须统一整个制作流程的所有 Filter，例如用于字幕制作的 Aegisub、用于检查校对的播放器、用于内嵌的滤镜，这些都得统一。

## 简介各Filters

现在常见的 ASS Filter 分为 `VSFilter` 和 `libass` 两系后端。

### VSFilter

`VSFilter` 是 `DirectVobSub` 的旧称，原身是 `Windows` 的 `DirectShow`。

`VSFilter` 的应用非常广泛，但因为该项目历史久远，且在十几年前主分支就停止开发，历史包袱沉重，性能差。

### VSFM

`VSFM` 是 `VSFilterMod`，是在 `VSFilter` 的基础上扩展了功能，扩展了很多有用的ASS标签，详见 [VSFilterMod - Wiki - New Tags](https://github.com/computerfan/VSFilterMod/wiki/New-Tags)

但是这些标签扩展并没有被其他Filters 的规范接受，导致使用这些标签的ASS只能使用 `VSFM` 渲染，再加上因为基于 `VSFilter`，所以性能同样差，以至于 `VSFM` 依旧冷门

### XySubFilter

前身是 `xy-VSFilter`，在 `VSFilter` 基础上改进，且兼容 `madVR` `MPC` 等

### [libass](https://github.com/libass/libass)

与 `VSFilter` 不同的一个Filter，本身的规范是向 `VSFilter` 兼容的，但还是存在很多与 `VSFilter` 渲染效果不同的地方

优势是 `libass` 的性能非常好，渲染一帧百行的特效字幕游刃有余，不像 VSF 系的 Filters 会卡顿，且项目一直在更新维护

### [XySubFilter with libass](https://github.com/Masaiki/xy-VSFilter)

`XySubFilter` 的分支，后端换成了 `libass`

## 如何选择

从长远的角度看，选择 `libass` 是最好的方案，但 `VSFM` 又有很多 `libass` 没有的好用功能，  
虽然有些功能可以通过自动化脚本解决，但有非原生带来的各种问题，例如大幅增加字幕行的占用和性能开销。

即使 `VSFM` 功能丰富，但主流是使用 `libass`，且 `libass` 项目仍在持续更新，所以建议所有项目全部统一使用 `libass`。
