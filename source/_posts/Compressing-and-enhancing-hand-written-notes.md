title: Compressing and enhancing hand-written notes
date: 2016-09-24 21:53:26
tags:
- Translation
- Recommend
- Image Processing
categories:
- Python
---

原文[链接](https://mzucker.github.io/2016/09/20/noteshrink.html)，作者：Matt Zucker

--------------------------------

翻译者：[马峰](http://m.imf.cc/)，禁止全文转载

我写了一个程序去清洁我扫描的手写的笔记，同时也是为了减少扫描文档的大小。

示例：原始效果和处理后的效果

![](https://mzucker.github.io/images/noteshrink/notesA1_comparison.png)

*左边是扫描的文档@300DPI， 7.2MB PNG， 790KB；右边是处理后的文档，300DPI，PNG，121KB*

声明：这里所描述的处理流程或多或少[Office Lens](https://blogs.office.com/2015/04/02/office-lens-comes-to-iphone-and-android/)这个App也能够做到，也可能已经有别的工具也已经能够做到类似的效果。我并没有在这里声明这是一个新发明，而只是我自己实现的一个有用的工具。

如果你很想知道最后的处理的效果，你可以直接查看这个[仓库](https://github.com/mzucker/noteshrink)里的代码，或者直接跳转到结果部分，那里可以直接看到可交互式的3D示意图。

### 缘由

我所教授的某些课并没有指定的教科书，对于这些课程，我喜欢每周都指定一些学生分享他所记录的课堂笔记给他的同学们，以便于通过这些学生手写的资料可以再次检查他们对于课程理解的程度。这些课程笔记全部以PDF格式被放在一个教学的网站上。

学校里有一个复印机可以方便的扫描笔记到PDF文档，但是文档的质量却并不好，比如下面的这类：

![](https://mzucker.github.io/images/noteshrink/copier_bad.png)

看起来复印机会随机选择是否[二值化](http://www.leptonica.com/binarization.html)每一个数学符号，比如上图中的`x`，或者把他们转化为JPG，比如上图中的根号。不用说，我们可以做的更好。

### 预备

我们从一个学生的可爱的笔记开始，比如下面这个：

![](https://mzucker.github.io/images/noteshrink/notesA1.jpg)

原初的300DPI扫描出来的PNG是7.2MB，如果我们吧这个PNG转化为JPG（85%质量），将会是790KB。因为扫描出来的PDF基本是一个JPG或者PNG的容器，所以我们并不期待，转为PDF后，文件大小会大大减少。但是800KB每页对加载时间来说实在是太大了，我觉得最好是100KB每页比较好。

虽然上图中的学生的笔记写的非常的整齐，但是上图中扫描出来的笔记还是把背面的文字透进来了，这会让阅读者失去兴趣，同时也对文件大小的压缩很不利。

这是通过我们的程序处理之后的样子：

![](https://mzucker.github.io/images/noteshrink/notesA1_output.png)

这是一个非常小的PNG文件，大概121KB。这就是想要的部分，文件很小，而且看起来也更清爽。

### 处理和渲染图像的基础

这是产生一个压缩的、更清晰的图片的基本步骤：
1. 确定原来的扫描的图片的背景的颜色
2. 通过设定阈值于前景色与背景色的差值去分离前景色
3. 通过从前景色中选择一部分“代表色”，将图片中转化为index色的PNG

在我们深入这些步骤之前，我们应该首先去理解color image是怎么被数字化存储的。因为人类有3个不同种类的对颜色敏感的细胞，我们可以通过联合各种各样的红、绿、蓝三色的密度去形成不同的颜色。最终的颜色等同于在[RGB颜色空间](https://en.wikipedia.org/wiki/RGB_color_space)里的3个不同的点。

![](https://mzucker.github.io/images/noteshrink/RGB_color_cube.svg)

虽然一个真正的矢量色彩空间将会允许连续的无限的像素密度，但是我们在数字化它们的时候，一般会离散化，通常我们会把红、绿、蓝三色分别用8个bit来存储。尽快这样，当我们把一个图片放在一个连续的色彩空间里分析仍旧是一个强大的工具。

### 找到背景颜色

因为我们扫描的笔记大部分是手写的文字和线段，我们可以期待纸的颜色应该是比较单一的，如果扫描仪每次都把没有写上文字的背景白纸当作一种固定的颜色话，我们可以很容易把这个背景色找出来。但是，现实不是这样。纸的颜色是随机变化的，由于扫描仪上镜头的灰尘，或者纸张本身的色差等。因此，在现实里，纸张本身的颜色的颜色可能在RGB色彩空间里千变万化的。

![](https://mzucker.github.io/images/noteshrink/notesA1_samples_raw.png)

尽管实际的每张纸的相似度很低，但是每张纸的颜色的分布却是非常的类似。两个都是大部分是灰白色，同时可能零散夹杂一些红、绿、蓝色等。这里有一个比较同样的10000个像素，通过亮度（就是红绿蓝三色的总和）对他们进行排序：

![](https://mzucker.github.io/images/noteshrink/notesA1_samples_sorted.png)

我们可以看到底部的大概80%到90%的颜色是同样的，但是，进一步观察时又不一样。实际上，大部分上图中的颜色是在RGB（240，240， 242），但是我们实际观察到原图时，只有10000个像素点中226个符合，大概占3%不到。

因为这种模式所占有的像素点是如此之低，我们应该有一个疑问，如何才能可依赖的描述一个图像的颜色分布。我们实际上是有一个很好的方案去确定上面的图片中的色彩分布，如果我们在寻找模式之前降低图片的像素的位数的话。这里是我们如果把8bit的像素将为4bit的像素后的效果图：

![](https://mzucker.github.io/images/noteshrink/notesA1_samples_sorted_4bit.png)

现在我们可以看到RGB（224， 224， 224）大概可以占据36%的比例。通过减少像素的位数，我们可以把临近相似的像素点合并为更大的点，这样我们就容易放在数据的波峰。

这里有一个精度和可依赖的权衡：更小的点可以让颜色更好的区分，但是更大的点可以让得出的结论更可依赖。最后我们在每个channel里使用6bit去寻找背景颜色，这个看起来是一个很好的平衡点。

### 分离前景色

一旦我们分离了背景色，我们可以用同样类似的方法去分离前景色。一个很自然的计算两个像素的相似度的方案就是去计算它们在RGB色彩空间的[Euclidean Distance](https://en.wikipedia.org/wiki/Euclidean_distance)，但是这种简单的方法并不能分离下面的颜色：

![](https://mzucker.github.io/images/noteshrink/colors.svg)

这里是一个表关于前景色与背景色之间的Euclidean Distance：

Color|Where|found|R|G|B|Dist. from BG
--------------------------------------
white|background|238|238|242|—
gray|bleed-through from back|160|168|166|129.4
black|ink on front of page|71|73|71|290.4
red|ink on front of page|219|83|86|220.7
pink|vertical line at left margin|243|179|182|84.3

你可以看到，我们这里的选择的背景色跟真正的白色之间差距比粉色还远。任何基于Euclidean Distance的threshold在将粉色作为前景色时，必须要考虑到背景色。

我们可以避免这个问题，如果我们从RGB的色彩空间移动到HSV色彩空间，它用一个圆柱体来表达：

![](https://mzucker.github.io/images/noteshrink/hsv.png)

HSV圆柱体把七彩的颜色分布圆周分布在它的外顶部，hue表达的是圆周的角度，圆柱体的底部是黑色，顶部是白色，中间是灰色的阴影。中间的saturation是0，外部是1.0。最后value表示的是亮度，从黑到白。

因此，如果我们使用HSV，会得到：


Color|Value|Saturation|Value diff. from BG|Sat. diff from BG
-----------------------------------------------------------------
white|0.949|0.017|—|—
gray|0.659|0.048|0.290|0.031
black|0.286|0.027|0.663|0.011
red|0.859|0.621|0.090|0.604
pink|0.953|0.263|0.004|0.247


我们可以看到黑、白、灰在数值上差异很大，但是saturation都很低，远低于红、粉。通过HSV的信息，我们可以成功地标记一个像素是否属于前景色：

- value的差值大于0.3或者
- saturation大于0.2

第一个主要是分离黑色墨水的颜色，而第二个主要是分离红色墨水盒粉色线段。两个都可以把灰色成功地分离出去，不同的图片可能需要不同的saturation和value threshold。

### 选择一系列代表颜色

一旦我们分离了前景色，我们剩下的工作就是要把纸上的相关的笔迹分离出来。我们可以把这些集合可视化出来：但是这次，我们不把颜色作为一团像素，我们将把他们当作RGB色彩空间中3D的点来处理。最终的离散图看起来是成块的。

（等待更新）

