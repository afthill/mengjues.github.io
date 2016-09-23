title: "通向机器学习之路的简单实用指南（一）：背景知识、基础概念和工作流"
date: 2016-09-23 10:09:38
tags:
- Translation
- Recommend
categories:
- 机器学习
---

原文[链接](https://indico.io/blog/simple-practical-path-to-machine-learning-capability-part1/)，作者：Dan Kuster

翻译者：[马峰](http://m.imf.cc/)，本文禁止全文转载

Hello，亲爱的人类！非常欢迎你们来到这一系列关于如何使用机器学习去解决问题的文章。通常我们会以基础的概念为起点，然后在后面会有一些列Python和
TensorFlow的代码来实现。然后我们将进一步展示如何合并利用和拓展这些基本的概念去解决更有趣的问题。

### 一种效率不高的学习方式

现在我们可以很容易在tensorfolow的官方网站或者其他地方找到[教程](https://github.com/nlintz/TensorFlow-Tutorials)，比如（[1](https://www.tensorflow.org/versions/r0.10/tutorials/index.html)、
[2](https://github.com/nlintz/TensorFlow-Tutorials)、[3](https://github.com/aymericdamien/TensorFlow-Examples)、[4](https://github.com/pkmital/tensorflow_tutorials)、[5](https://cs224d.stanford.edu/lectures/CS224d-Lecture7.pdf)）。
但是我们也发现许多很聪明的人，在学习tensorflow的时候，也会在第一次就深深跌入机器学习的代码实现中。这会导致你一个通用的效率不高的模式，你认为你
在学习的时候，停滞在实现的细节里，而实际上你只是停滞在思考的流程里：

- 搜索一些tensorflow的教程，然后放进浏览器里
- 然后开始循着一个简单的教程，很快学完后，觉得好棒，很容易就实现了教程里的功能，而且使用的是DNN（deep neural networks）
- 然后尝试着使用一些非常[复杂](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/python/ops/seq2seq.py#L745)的模型去解决新问题
- 嗯，出现了一些错误导致不能工作，而且看起来这个错误不容易被解决
- 再次去网上搜索寻找答案，也许别人已经解决了我的这类问题

<iframe src="//giphy.com/embed/CXAPW8vCQzYkg?html5=true&hideSocial=true" width="480" height="388" frameborder="0" class="giphy-embed" allowfullscreen=""></iframe>

### 去掉从思考到写代码的过程 

如果这里有像一个专家一样使用机器学习的秘密，我想这个秘密应该是这样：机器学习的核心不是去写代码，而是一种科学思考的流程：首先把你遇到的唯一的问题翻译
成机器学习的任务（Task），这个任务然后通过数据、机器学习的思想、工具（比如tensorflow）、互联网上的信息等被解决。我们现在就开始吧！

### 范围（scope）

这篇文章引入了一些列通用的关于机器学习的基础概念，这些概念可以帮助理解在机器学习中的大部分的解决问题的方案。我们在这里并不会展示大量通用的机器学习的
理论，只是一系列可以帮助你大量提高机器学习能力的范式。这些范式是许多模型的基础，包括从最简单的线性回归到深度神经网络。

(to be continued)