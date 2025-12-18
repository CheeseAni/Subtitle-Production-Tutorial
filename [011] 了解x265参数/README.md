## 概述
本篇文章将粗略讲解x265和x264这两个编码器的常用参数，对功能的介绍仅基于使用经验，没有详细了解过算法部分，故大家不要完全置信。


## x265参数
[官方文档](https://x265.readthedocs.io)

因为在2024年的今天，HEVC硬件解码已经几乎完全普及(你要是翻出个古董那我也没办法)，所以将视频压为10bit HEVC格式是主流做法，甚至可以完全抛弃AVC，所以我将主要讲解x265编码器的参数。

用法:`ffmpeg -i "" -c:v libx265 -x265-params "这里填x265参数" ""`  
示例:`ffmpeg -i "input.mp4" -c:v libx265 -x265-params "crf=18:deblock=0.5,0.5:me=star:weightb=1" "output.mkv"`

x265有多种码率控制模式，在我们所需要的压制中，使用CRF模式压缩率最高


_取值列中，前面是取值范围，括号内是默认值_

### 质量控制
| 选项 | 取值 | 描述 | 效果 | 补充说明 |
| :--: | :--: | :--: | :--: | :------: |
| [crf](https://x265.readthedocs.io/en/master/cli.html#cmdoption-crf)           | 0..51.0  (28)  | 基于质量的VBR，控制整体质量 | 数值越大，整体质量越低         | crf间接控制P帧的qp |
| [qpmin](https://x265.readthedocs.io/en/master/cli.html#cmdoption-qpmin)       |          (0)   | 限制P帧最低qp              |                               | 可以调高qpmin，降低过低crf下的过高质量帧的质量，以降低整体码率 |
| [qpmax](https://x265.readthedocs.io/en/master/cli.html#cmdoption-qpmax)       |          (69)  | 限制P帧最高qp              |                               | 可以调低qpmax，提升过高crf下的过低质量帧的质量，以防止部分帧质量过低 |
| [aq-mode](https://x265.readthedocs.io/en/master/cli.html#cmdoption-aq-mode)   | 0..4     (2)   | 控制自适应量化的模式        | 有效在保留纹理的同时提高压缩率 | 0关闭；<br>1均匀AQ；<br>2方差AQ，效果比1好但更慢；<br>3在2的基础上质量向暗场偏移；<br>4通过边缘检测调整方差，效果最好，但最慢 |
| [cbqpoffs](https://x265.readthedocs.io/en/master/cli.html#cmdoption-cbqpoffs) | -12..12  (0)   | Chroma Cb QP Offset       | 控制Cb的相对质量                | 可以调低Chroma的QP以弥补YUV420导致的Chroma平面画质的损失，当然，这会增加码率，建议[0,-3] |
| [crqpoffs](https://x265.readthedocs.io/en/master/cli.html#cmdoption-crqpoffs) | -12..12  (0)   | Chroma Cr QP Offset       | 控制Cr的相对质量                | 同上 |
| [ipratio](https://x265.readthedocs.io/en/master/cli.html#cmdoption-ipratio)   | 0..float (1.4) | I帧与P帧的QP差值           | 控制I帧相对P帧的质量            | 默认值挺合适的，没必要调，偶尔需要降低或升高I帧画质的话可以调 |
| [pbratio](https://x265.readthedocs.io/en/master/cli.html#cmdoption-pbratio)   | 0..float (1.3) | P帧与B帧的QP差值           | 控制B帧相对P帧的质量            | 可以适当调低以提升B帧画质，建议[1.3,1.2] |

### 自适应量化
| 选项 | 取值 | 描述 | 效果 | 补充说明 |
| :--: | :--: | :--: | :--: | :------: |
| [aq-strength](https://x265.readthedocs.io/en/master/cli.html#cmdoption-aq-strength) | 0..3.0 (1)     | 控制AQ的强度    | 调高可减少平面和纹理区域的遮挡和模糊   | 建议[0.8,1.2] |
| [qcomp](https://x265.readthedocs.io/en/master/cli.html#cmdoption-qcomp)             | 0.5..1.0 (0.6) | 预测复杂度的权重 | 控制码率向动态画面(复杂残差)的偏移程度 | 调越高，静态画面分配到的码率越低，动态画面分配到的码率越高，所以调很高的话记得调低crf以弥补静态画面的码率损失，建议[0.65,0.75]，一般情况0.65足够<br>(因为是根据残差判断，所以值为1时，同CQP) |

### 运动搜索
| 选项 | 取值 | 描述 | 效果 | 补充说明 |
| :--: | :--: | :--: | :--: | :------: |
| [ref](https://x265.readthedocs.io/en/master/cli.html#cmdoption-ref)         | 1..16    (3)   | 最大参考帧帧数   | 一定程度提升压缩率，少量降低速率 | HEVC规范中最高为8，这个值越高压缩率越高，对编码速率的降低较少，所以个人建议无脑拉最高 |
| [me](https://x265.readthedocs.io/en/master/cli.html#cmdoption-me)           | 0..5     (1)   | 设置运动搜索方法 | 不同方法压缩率与速率不同         | 0:dia 1:hex 2:umh 3:star 4:sea 5:full<br>近期个人测试发现umh完全优于star，特别是高marange时更明显，建议只使用umh |
| [merange](https://x265.readthedocs.io/en/master/cli.html#cmdoption-merange) | 0..32768 (57)  | 设置运动搜索范围 | 提升压缩率，但边际效应明显       | 不求高压缩率时不宜调整，若降低CTU以求速率，应同步降低；<br>1080p时默认的57算是比较合适 |
| [hme](https://x265.readthedocs.io/en/master/cli.html#cmdoption-hme)         | bool     (0)   | 三级运动搜索开关 | 代替默认的运动搜索方式           | 比默认的更好，所以必开，开启后使用 hme-search 和 hme-range 选项配置参数，例如 hex,hex,hex 16,57,184 比 umh 57 快很多的同时压缩率更高 |
| [hme-search](https://x265.readthedocs.io/en/master/cli.html#cmdoption-hme-search) | int&#124;str,int&#124;str,int&#124;str (hex,umh,umh) | 设置每级运动搜索方法 | 同 me | |
| [hme-range](https://x265.readthedocs.io/en/master/cli.html#cmdoption-hme-range)   | int,int,int (16,32,48) | 设置每级运动搜索范围 | 同 merange | |
| [subme](https://x265.readthedocs.io/en/master/cli.html#cmdoption-subme)     | 0..7     (2)   | 子像素运动估计精度 | 提升压缩率，对速率影响较大       | 电脑性能好且时间充裕的话可以无脑拉最高 |
| [rect](https://x265.readthedocs.io/en/master/cli.html#cmdoption-rect)       | bool     (0)   | Enable rectangular motion partitions Nx2N and 2NxN | 略微提升压缩率，降低编码速率 | 也就是启用一种新的预测方法，电脑性能好再考虑开，因为对压缩率的提升真的只是"略微" |
| [amp](https://x265.readthedocs.io/en/master/cli.html#cmdoption-amp)         | bool     (0)   | Enable asymmetric motion partitions                | 略微提升压缩率，降低编码速率 | 同样是启用一种新的预测方法，需要开启rect才能开启，同样是建议电脑性能足够，且时间充裕再开 |

### CTU
| 选项 | 取值 | 描述 | 效果 | 补充说明 |
| :--: | :--: | :--: | :--: | :------: |
| [ctu](https://x265.readthedocs.io/en/master/cli.html#cmdoption-ctu)                       | 64&#124;32&#124;16        (64) | Maximum CU size (WxH)      | | 一般情况下没必要调<br>（这就不得不吐槽一下了，最高只能64，编码高分辨率视频慢得跟吃食一样难受） |
| [min-cu-size](https://x265.readthedocs.io/en/master/cli.html#cmdoption-min-cu-size)       | 64&#124;32&#124;16&#124;8 (8) | Minimum CU size (WxH)      | | 编码高分辨率(指≥1440p)视频时适量调高以提升编码速率，平时编个1080p没必要调 |
| [max-tu-size](https://x265.readthedocs.io/en/master/cli.html#cmdoption-max-tu-size)       | 32&#124;16&#124;8&#124;4  (32) | Maximum TU size (WxH)      | | 同理 |
| [tu-intra-depth](https://x265.readthedocs.io/en/master/cli.html#cmdoption-tu-intra-depth) | 1..4                      (1) | CU内(帧内)的最大TU递归深度  | 提升压缩率，降低速率                | 建议性能够拉高就无脑最高 |
| [tu-inter-depth](https://x265.readthedocs.io/en/master/cli.html#cmdoption-tu-inter-depth) | 1..4                      (1) | CU间(帧间)的最大TU递归深度  | 提升压缩率，降低速率，可能会增高码率 | 建议性能够拉高就无脑最高 |
| [limit-tu](https://x265.readthedocs.io/en/master/cli.html#cmdoption-limit-tu)             | 0..4                      (0) | 最大提前结束帧间TU递归的深度 | 有效提升速率，但略微降低压缩率      | 因为是自动计算递归终止的时机的，所以开启后对压缩率的降低效果甚微，但能大幅度提升高tu-inter-depth编码时的编码速率，建议2 |

### 率失真优化
| 选项 | 取值 | 描述 | 效果 | 补充说明 |
| :--: | :--: | :--: | :--: | :------: |
| [rd](https://x265.readthedocs.io/en/master/cli.html#cmdoption-rd)                 | 1..6    (3) | 控制RD(Rate-Distortion)决策模式      | 显著提升压缩率，但降低速率 | 实测6压缩率比5低，所以只建议开5或3 |
| [psy-rd](https://x265.readthedocs.io/en/master/cli.html#cmdoption-psy-rd)         | 0..5.0  (3) | RD的心理视觉优化强度                 | 提升视觉观感，但降低基于非视觉的压缩率 | rd>=3时生效；简单来说就是一种预测人类视觉观感的算法，优先保证观感而不是图像的相似度，但实际效果不一定很好，调高会导致码率同步升高，可以用来保留纹理，默认2，建议[1.6,2] |
| [rdoq-level](https://x265.readthedocs.io/en/master/cli.html#cmdoption-rdoq-level) | 0..2    (0) | 控制RDOQ(Optimized Quantization)级别 | 显著保留纹理细节，但大幅降低速率 | 开的话建议开2，不建议开1，开启后码率升高 |
| [psy-rdoq](https://x265.readthedocs.io/en/master/cli.html#cmdoption-psy-rdoq)     | 0..50.0 (0) | RDOQ的心理视觉优化强度               | 类似psy-rd | 可用于保留纹理，建议[0,2]，不建议开高，因为开高对画质的优化不如直接调低CRF，调成0也没啥问题 |

### Coding tool
| 选项 | 取值 | 描述 | 效果 | 补充说明 |
| :--: | :--: | :--: | :--: | :------: |
| [weightb](https://x265.readthedocs.io/en/master/cli.html#cmdoption-merange) | bool (0) | 开启B帧加权预测 | 提升压缩率 | 这是在原本的预测参数上加权，所以不会影响编码速率，所以必开 |

### Loop filter
| 选项 | 取值 | 描述 | 效果 | 补充说明 |
| :--: | :--: | :--: | :--: | :------: |
| [deblock](https://x265.readthedocs.io/en/master/cli.html#cmdoption-deblock) | float, float (0, 0) | 去块滤镜 | 两个形参分别是范围和阈值，阈值越高强度越大 | 建议画面清晰的动画-1,-1，画面存在色块的话可以自行调整 |
| [sao](https://x265.readthedocs.io/en/master/cli.html#cmdoption-sao)         | bool         (1)    | 采样自适应偏移 |                                    | 想法很好，但实际测试结果很烂，一般必关 |

### 切片决策
| 选项 | 取值 | 描述 | 效果 | 补充说明 |
| :--: | :--: | :--: | :--: | :------: |
| [open-gop](https://x265.readthedocs.io/en/master/cli.html#cmdoption-open-gop)         | bool      (1)   | 允许I帧作为非IDR帧  | 略微提升压缩率，略微增加解码压力 | 一些HEVC普及早期的古董版本的解码器可能不兼容，现在无所谓了，所以开 |
| [keyint](https://x265.readthedocs.io/en/master/cli.html#cmdoption-keyint)             | -1..int   (250) | 最大GOP帧数 | | 若为-1，无限GOP；过高会导致跳转播放时解码时间爆炸，建议[250,450]，特殊情况下可以通过降低keyint来保证画质(不建议) |
| [min-keyint](https://x265.readthedocs.io/en/master/cli.html#cmdoption-min-keyint)     | 1..keyint (25)  | 最小GOP帧数 | | 建议[1,4] |
| [rc-lookahead](https://x265.readthedocs.io/en/master/cli.html#cmdoption-rc-lookahead) | 1..250    (20)  | 切片类型决策前瞻的帧数 | 提升压缩率，有边际效应，略微降低速率 | 用于判断帧类型和QP等；该值不能低于最大连续B帧数；当值高于最大GOP后，多出的部分没有效果 |
| [bframes](https://x265.readthedocs.io/en/master/cli.html#cmdoption-bframes)           | 0..16     (4)   | 最大连续B帧数         | 大幅提升压缩率，大幅降低速率 | 只要兼容，直接拉满就行；同画质下B帧增多会大幅降低码率，这会增加解码的速率，而不是降低 |
