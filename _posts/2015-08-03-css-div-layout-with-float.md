---
layout: post
title: "div 中的浮动布局"
description: ""
category: "web"
tags: ["CSS"]
---
{% include JB/setup %}
布局的方式有很多种，例如使用 table 元素布局、使用 columns 属性布局以及使用 div 进行布局。不同的布局方式有不同的特点，下面主要介绍 div 浮动布局。

<!-- more -->

<h2 id="div-layouts-work">
要解决的问题
</h2>
- 多栏布局，两栏、三栏、多栏
- 布局宽度，固定、流动
- 布局高度，等高、不等高

<h2 id="float-layouts">
浮动布局
</h2>
使用div实现页面布局，通常要设置 float 属性，也就是基于浮动进行布局。两栏布局相对比较简单，主要介绍三栏布局。

    <div class="content">
      <div class="left"></div>
      <div class="middle"></div>
      <div class="right"></div>
    </div>

    .content {
      width:400px;
    }
    .left {
      float:left;
      width:100px;
    }
    .middle {
      float:left;
      width:200px;
    }
    .right {
      float:right;
      width:100px;
    }
 
这存在一个问题，浮动元素会脱离文档流，进入了浮动层，导致父元素出现塌陷现象，简单来说就是，子元素撑不起父元素。解决这个问题有以下几种方法：1.设置父元素的 float 属性；2.设置父元素 overflow 属性为 hidden 强制包围子元素 ；3.增加非浮动的元素并设置clear属性。第三种方式的思路是，父元素肯定要包围非浮动元素，因此只要将将这个非浮动元素放在浮动元素的底部，自然会将父元素撑开；而 clear 属性可以将元素放到浮动元素的下面；常常使用的是 :after 伪元素，而不是真正的空白元素。

    .content::after {
      content:'';
      display:block;
      clear:both;
    }

<h2 id="fluid-layouts">
流动布局
</h2>

流式布局，宽度是用百分数设置，好处是页面能够相对浏览器伸缩。当页面伸缩时，内容的排布可能会受到影响，例如文字和图片等，因此要处理这些问题。  
也有一些布局要求某一栏是流动的，而其他部分是固定的，例如三栏布局中，将中间部分设定为流动，而其他两栏设置为固定的。

方法1： 使用 calc 来计算中栏宽度。

    <div class="content">
      <div class="left"></div>    
      <div class="middle"></div>
      <div class="right"></div>
    </div>

    .content {
      width:96%;
    }
    .left {
      float:left;
      width:100px;
    }
    .middle {
      float:left;
      width:calc(100% - 200px);
    }
    .right {
      float:left;
      width:100px;
    }


方法2：设置外边距属性 margin
     
    <div class="content">

        <div class="wrap">
          <div class="left"></div>    
          <div class="middle"></div>
       </div>

       <div class="right"></div>

    </div> 


    .wrap {
      margin-right:-100px;
      float:left; 
      width:100%;
    }
    .left {
     float:left;
     width:100px;
    }
    .middle {
     width:auto;
     margin-left:100px;
     margin-right:100px;
    }
    .right {
       float:left;
       width:100px;
    }

首先，用 wrap 对左栏和中栏进行包装， wrap 实质是一个两栏布局；默认情况下，非浮动的块级元素的宽度自动填满父元素，浮动元素的宽度是由其内部内容宽度等因素决定的，因此中栏并未设置 float 属性；中栏的 margin-left 并不是为了给左栏提供空间，而是防止左栏遮挡中栏。 

其次，除了中栏以外都是浮动的，wrap 如果不浮动，右栏就会挤到 wrap 的下面；但是 wrap 的 width 是 100%，右栏还是被挤到下面去了；为了解决这个问题，wrap 使用了负右外边距，这样就为右栏提供了一个空间，允许右栏遮盖到负右边距的区域；中栏设置 margin-right 防止被右栏遮挡中栏。

> 浮动元素的设置负外边距，并未改变元素宽度，只是允许了其他浮动元素可以遮挡到它上面。

<h2 id="layout-equal-height">
多栏等高
</h2>


    <div class="content">

        <div class="wrap">
          <div class="left eq"></div>    
          <div class="middle eq"></div>
       </div>

       <div class="right eq"></div>

    </div> 


    .content {
      overflow: hidden;
    }
    .eq {
      margin-bottom: -99999px;
      padding-bottom: 99999px;
    }

 这样就可以实现三栏等高

<h2 id="reference">
参考资料
</h2>
[Fluid Width Equal Height Columns](https://css-tricks.com/fluid-width-equal-height-columns) -- CSS-Tricks  
[All About Floats/](https://css-tricks.com/all-about-floats/) -- CSS-Tricks  
[The Definitive Guide to Using Negative Margins](http://www.smashingmagazine.com/2009/07/the-definitive-guide-to-using-negative-margins/)  -- John Imbong  
[CSS float](http://alistapart.com/article/css-floats-101) -- Noah Stokes  
[Equal Height Columns with Cross-Browser CSS](http://matthewjamestaylor.com/blog/equal-height-columns-cross-browser-css-no-hacks) --  MATTHEW JAMES TAYLOR
  
