---
layout: post
---

# 搭建一个 CSS 框架
## 目标效果:


![I and My friends]({{site.baseurl}}/assets/img/15079851283084-1.jpg)

## 实现要求:
**使用百分比和 display:flex 属性**
**调整 margin 使其页面居中**

**HTML 实现**

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>Framework Test Page</title>
  <link rel="stylesheet" href="css/main.css">
</head>
<body >
  <div class="grid">
    <div class="row">
      <div class="col-2">2 Columns</div>
      <div class="col-10">10 Columns</div>
    </div>
    <div class="row">
      <div class="col-3">3 Columns</div>
      <div class="col-9">9 Columns</div>
    </div>
    <div class="row">
      <div class="col-4">4 Columns</div>
      <div class="col-4">4 Columns</div>
      <div class="col-4">4 Columns</div>
    </div>
    <div class="row">
      <div class="col-3">3 Columns</div>
      <div class="col-6">6 Columns</div>
      <div class="col-3">3 Columns</div>
    </div>
  </div>
</body>
</html>
```

**CSS 实现**

```css
/*main.css*/

* {
    border: 1px solid red !important;
}

/*!importmant 表示在 css 的选择上有最高的优先级*/

* {
    -webkit-box-sizing: border-box;
    -moz-box-sizing: border-box;
    -ms-box-sizing: border-box;
    box-sizing: border-box;
}
/*box-sizing: border-box; 表示 width 的宽度包含 padding 和 border*/

.grid {
    margin: 0 auto;
    max-width: 1200px;
    width: 100%;
}
/*margin: 0 auto; 表示限制用户在超大屏幕上查看页面时的最大页面宽度并将内容居中*/

.row {
    width: 100%;
    margin-bottom: 20px;
    display: flex;
}

.col-1 {
    width: 8.33%;
}

.col-2 {
    width: 16.66%;
}

.col-3 {
    width: 25%;
}

.col-4 {
    width: 33.33%;
}

.col-5 {
    width: 41.66%;
}

.col-6 {
    width: 50%;
}

.col-7 {
    width: 58.33%;
}

.col-8 {
    width: 66.66%;
}

.col-9 {
    width: 75%;
}

.col-10 {
    width: 83.33%;
}

.col-11 {
    width: 91.66%;
}

.col-12 {
    width: 100%;
}

/*only 表示兼容更老的浏览器
screen 表示在任何屏幕并且小于 500 px适用*/
@media only screen and (max-width: 500px) {
    p {
        display: none;
    }
}
```


