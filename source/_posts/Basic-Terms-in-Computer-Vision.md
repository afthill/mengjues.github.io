title: Basic Terms in Computer Vision
date: 2016-10-26 15:27:46
tags:
- CV
categories:
- 笔记
---

### Intensity of Image

根据[这里](https://www.quora.com/What-is-the-difference-between-brightness-and-intensity-in-digital-image-processing)，Intensity（光强）是一个特定波长在能量上的度量，正比于该波长在`振幅上的平方`。而brightness（亮度）则与波长与振幅都有关系。因此`bright blue light`与`dim blue light`不同，由于都是`blue light`，所以波长是一样的，而不同的是`amplitude`（振幅）。而如果是`blue light`与`yellow light`对比，则brightness（亮度）是不同，因为波长不同，而且amplitude也是不同的。

上面是通俗的解释，下面进入CV领域：

brightness是一个相对的概念，它依赖于你的视觉认知（visual perception），亮度没有具体的度量标准，往往在说brightness时，只是相对于某一个参照物的对比。

intensity则是对光的强度的一个度量，或者说是某一个像素的数字化的值。比如，在一个（0~255）的灰度图中，intensity被用一个0~255之间的灰度图来表示。

在数字处理的领域上，intensity可以为对一张图片的全局的数字量化（而不是对某一个像素点的数值化），诸如`mean pixel intensity`（MPI）是对图片中所有的像素点的均值化。我们在比较图片的亮度时（how bright），我们一般用MPI（mean pixel intensity）来比较和数值化两张图片的亮度变化。


