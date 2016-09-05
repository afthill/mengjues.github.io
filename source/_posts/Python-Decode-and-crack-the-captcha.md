title: 'Python: Decode and crack the captcha'
date: 2016-09-01 00:32:02
tags:
- captcha
categories:
- 方案
---

对验证码的识别现在基本有3种方案：

### 1）把图片下载后，利用现成的库进行OCR识别

对于OCR engine，现在有一个非常流行的开源库，叫[Tesseract engine](https://github.com/tesseract-ocr)，而对于Python
则有一个叫[Pytesser](https://github.com/RobinDavid/Pytesser)的开源库，是一个python wrapper on tesseract engine。

参考： <https://blog.c22.cc/2010/10/12/python-ocr-or-how-to-break-captchas/>

- 项目1：<https://github.com/RobinDavid/Captacha-basic-recognition>
- 项目2：<https://github.com/Sadhanandh/Captcha-Crack>

### 2）通过图片训练，比较图片的相似度

参考：<http://www.jwandrews.co.uk/2013/01/breaking-the-minteye-image-captcha-in-23-lines-of-python/>

- 项目1：<https://github.com/mekarpeles/captcha-decoder>
- 项目2：<https://github.com/ptigas/simple-captcha-solver>
- 项目3：<https://github.com/cydu/spider/tree/master/captcha>
- 项目4：<https://github.com/ccoley/captcha>
- 项目5：<https://github.com/epsylon/cintruder>
- 项目6：<https://github.com/9-volt/captchonka>
- 项目7：<https://github.com/lan2720/fuck-captcha>
- 项目8：<https://github.com/aflag/captcha-study> 参考：<http://rafael.kontesti.me/blog/posts/Breaking_a_captcha/>

### 3）机器学习或者Deep Learning

- 项目1：<https://github.com/Halfish/Captcha-Cracking>
- 项目2：<https://github.com/shanusalunke/Captcha-Cracker>
- 项目3：<https://github.com/Kagami/chaptcha>
- 项目4：<https://github.com/Yaoshicn/decaptcha>
