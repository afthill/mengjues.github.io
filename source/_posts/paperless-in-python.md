title: 使用Python把文档扫描、索引与存档
date: 2016-01-18 12:40:50
tags:
- OCR
categories:
- 程序语言

---

项目地址：<https://github.com/danielquinn/paperless>

所用到的模块：
- ImageMagick converts the images between colour and greyscale.
- Tesseract does the character recognition
- GNU Privacy Guard
- Python 3 is the language of the project
    - Pillow loads the image data as a python object to be used with PyOCR.
    - PyOCR is a slick programmatic wrapper around tesseract
    - Django is the framework this project is written against.
    - Python-GNUPG decrypts the PDFs on-the-fly to allow you to download unencrypted files, leaving the encrypted ones on-disk.

> The keen eye might have noticed that we're converting a PDF to an image to be read by Tesseract, and to do this we're using a chain of: scanned PDF > Imagemagick > Pillow > PyOCR > Tesseract > text. It's not ideal, but apparently, Pillow lacks the ability to read PDFs, and PyOCR requires a Pillow object, so we're sort of stuck.
