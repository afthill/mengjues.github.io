title: 绘制基于ascii的复杂表格的算法
date: 2016-01-08 10:31:22
tags:
- Render Table
categories:
- 算法

---

算法：<https://snai.pe/python/algorithm/rendering-tables/>

ascii原图：
```
+-----+-----+-----+-----+
|  A  |  B  |  C  |  D  |
+-----+-----+-----+-----+
|  E  |        F        |
+-----+-----+-----------+
|  G  |     |           |
+-----+  I  |     J     |
|  H  |     |           |
+=====+=====+===========+
```
算法解析：

![](/images/table1.PNG)

![](/images/table2.PNG)

![](/images/table3.PNG)

数据结构：tree

遍历算法：Vistor

--END--
