title: Compare Image Pixel by Pixel by ImageMagick
date: 2016-07-09 12:33:04
tags:
- ImageMagick
categories:
- 计算机视觉
---

第一个方案： 把要去掉的部分，写进去一个矩阵里，然后再逐像素比较时，跳过原本要去掉的部分。

<http://stackoverflow.com/questions/28881471/comparing-images-in-which-some-parts-are-variable-but-should-not-matter>


第二个方案：把要填充的部分，使用单一的颜色借助于ImageMagick的convert功能，生成一个新的
图片，这个图片保证在需要去除的部分，使用实色填充。
