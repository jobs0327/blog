---
title: css清除浮动常用方法
date: 2018-12-12 21:06:41
tags: CSS
categories: CSS
copyright: 乾學長
top:
---

# css清除浮动常用方法总结

## 浮动对布局带来的影响

- css块级元素在页面中默认是独占一行且自上而下排列的，也就是所谓的文档标准流，但当我们遇到将块级元素水平排列的情况时，标准流已经不能满足我们的需求，使用浮动布局可能会是最简单快捷的方式
- 浮动会将行内元素转化成为块级元素，当然这也是脱离文档流的表现
- 如果要实现左右浮动之后,父容器没有设置高度，子容器全部浮动，下面还有内容跟在后面，这样就会出现后面的内容跑到浮动元素的下面去，显然这不是我们想要的结果，这时我们就需要考虑怎么清除浮动对后面元素产生的影响

## 如何清除浮动



- 使用clear属性清理浮动

  代码示例：

   `clear: both | left | right`

- 给父元素设置 `overflow: hidden`

- **推荐**： 使用伪元素清除浮动

  <!-- more -->

  代码示例

  ```css
  .clearfix:after {
  	content: "";
  	display: block;
  	height: 0;
  	line-height: 0;
  	clear: both;
  	visibility: hidden;//隐藏	
  }
  
  .clearfix {
  	zoom:1;//兼容IE
  }
  ```

  使用时将 `clerafix` 这个类作用在浮动元素的父元素上即可

- 如果使用`less`或者`sass`，可以使用混入的方式，这样`css`样式就会和`html`代码完全分开，不需要再在`html`中加上额外的类名，当然实现代码还是上面的效果

  - `less`实现混入

    ```
    .a {
      color: red;
    }
    .mixin-class {
      .a();
    }
    
    result=>
    .a {
        color: red;
    }
    .mixin-class {
        color: red;
    }
    ```

    **具体参见：** [less混入](http://lesscss.cn/features/#mixins-feature)

  - `sass`实现混入

    ```
    定义：（@mixin）
    @mixin large-text {
      font: {
        family: Arial;
        size: 20px;
        weight: bold;
      }
      color: #ff0000;
    }
    引用：(@include)
    .page-title {
      @include large-text;
      padding: 4px;
      margin-top: 10px;
    }
    ```

    **具体参见：** [sass混入](https://www.sass.hk/docs/)