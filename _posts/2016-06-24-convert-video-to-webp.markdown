---
layout: post
title: "视频转WebP"
author: Leon
categories: [community]
---

因为产品的需求，在用户的Moment里面显示动态的图片，类似于music.ly个人主页的效果，所以在技术上面我们需要把mp4格式的视频转换成动态WebP格式的图片。为什么不用gif而用WebP格式，这里有答案：[https://developers.google.com/speed/webp/](https://developers.google.com/speed/webp/)

清楚了需求，接下来我们就开始动手，既然是处理视频，那么我们必然选用ffmpeg工具。按照需求我们只需要把mp4格式的视频转换成WebP格式，那ffmpeg能否直接满足我们的需求呢，答案是否定的，目前ffmpeg还不支持把视频文件转换成WebP格式的图片。那么我们就需要先把视频文件转换成gif格式的图片，然后再把gif格式的图片转换成WebP格式的图片。

视频转换成gif比较简单，一行命令就可以解决：
{% highlight bash %}
ffmpeg -t 1 -ss 1 -i a.mp4 a.gif
 -t 表示截取视频的时长
 -ss 表示从视频的第几秒开始截取
 -i 表示视频源文件
 a.mp4是视频源文件
 a.gif是输出的gif格式图片
 {% endhighlight %}

这样视频文件就转换成了gif格式的图片，接下来我们只需要把gif格式的图片转换成动态WebP格式即可，因为Google官方提供了工具可以直接把gif图片转换成WebP格式的文件，所以也是一行命令就可以解决：
{% highlight bash %}
gif2webp -lossy a.fig -o a.webp
-lossy 表示有损压缩
-o 表示输出的webp格式文件
{% endhighlight %}

这样我就得到了我们想要的动态WebP格式的图片，没错，就是这么简单。
当然涉及到图片的尺寸、视频转gif后图片的色彩质量、最终的文件大小大家可以继续深入的研究，目前我们完整的命令是这样：
{% highlight bash %}
ffmpeg -y -ss 1 -t 1 -i a.mp4 -vf fps=10,scale=-1:300:flags=lanczos,\
palettegen palette.png
ffmpeg -ss 1 -t 1 -i a.mp4 -i palette.png -filter_complex \
"fps=12,scale=-1:300:flags=lanczos[x];[x][1:v]paletteuse" a.gif
gif2webp -lossy a.gif -o a.webp
{% endhighlight %}

相关参考资料：<br />
[http://blog.pkh.me/p/21-high-quality-gif-with-ffmpeg.html](http://blog.pkh.me/p/21-high-quality-gif-with-ffmpeg.html)
[https://developers.google.com/speed/webp/docs/gif2webp](https://developers.google.com/speed/webp/docs/gif2webp)
