title: Webpack Define Plugin
date: 2017-01-05 00:41:16
tags:
- Webpack
categories:
- 笔记
---

```
plugins: [
  //...
  new webpack.DefinePlugin({
      PRODUCTION: JSON.stringify(true),
      VERSION: JSON.stringify("5fa3b9"),
      BROWSER_SUPPORTS_HTML5: true,
      TWO: "1+1",
      "typeof window": JSON.stringify("object"),
      env: {
        DEVELOPMENT: JSON.stringify(false)
      }
  })
  //...
]
```

这个webpack.DefinePlugin类似于C中宏（define）。 

源：http://tomasalabes.me/blog/_site/web-development/2017/01/03/Useful-Webpack-Define-Plugin-Usages.html
