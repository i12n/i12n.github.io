---
layout: post
title: "大屏 Dashboard 的实现"
description: ""
category: web
tags: [ dashboard ]
---
{% include JB/setup %}

大盘数据图表（dashboard）在一些应用场景中需要投放在大屏设备上展现，例如比较常见的 [TV Dashboard](https://www.klipfolio.com/resources/articles/what-is-a-tv-dashboard) 是在电视屏幕上展示的。由于大屏 dashboard 的使用场景不同于常规的 dashboard，因此在设计实现上也存在一些的差异。

<!-- more -->

## 大屏 Dashboard 的特点

1. **实时性高**

    在大屏 Dashboard 的业务场景中，主要是将些业务的关键指标以图表的方式实时展现出来，使用户更加直观、快捷的获得这些数据信息。

2. **全屏适配**

    为了让用户拥有更好的体验，大屏 Dashboard 是以全屏的方式铺满整个屏幕，不同尺寸的屏幕需要适配。

3. **交互单一**

    在这种大屏数据的展示过程，用户不能做任何交互操作（例如鼠标点击等），用户只能“看”。

4. **视觉动效**

    对动效的要求较高，通过动效让用户感知到数据的变化。通过页面内容的自动切换，弥补交互单一带来的缺陷。

    > [实时销售的案例](https://datav.aliyun.com/share/83b1633bfced939b45d9456b19edb12f)

## 实时数据

- **定时请求数据**

    为了满足数据的实时性，需要 Dashboard 不停的发送请求，并将请求到的数据渲染到页面中。这个过程可以通过定时器实现：

    ```js
    fetchData() {
        request
            .get('url')
            .then(data => { renderData() });

        setTimeout(() => {
            fetchData()
        }, 1000);
    }
    ```

- **数据异常策略**

    当数据请求发生异常，要避免将异常展示在页面上，同时也要避免某个接口异常使整个 Dashboard 不可用。对于频繁请求的接口，偶然发生的一次异常，不应该影响用户的体验；为了降低风险，对低频的接口适当增加请求频次。

## 全屏适配

- **布局**

    rem 自适应布局，字体和高度用 通过 rem 来处理；
    水平方向用宽度的 百分比来处理。

    ![dashboard-layout](/assets/images/web/dashboard/layout.png)

- **适配**

    通过高度来调整 html 的 font-size:

    ```html
    <html style="font-size: 100px;">
        <script>
        window.addEventListener('load', function() {
            var height = document.body.clientHeight;
            var ratio = height / 1080;
            document.documentElement.style.fontSize = ratio * 100 + 'px';
        })
        </script>
    </html>
    ```

    存在的弊端：由于 chrome 的限制，字体的 font-size 小于 12px不生效，当屏幕比较小时，并不能等比缩放。另外，当页面高/宽比值较大的时候，也会导致宽度不能较好的适配。

- **全屏**

    [requestFullScreen](https://developer.mozilla.org/en-US/docs/Web/API/Element/requestFullScreen) 实现页面全屏，浏览器禁止自动触发全屏，只能通过用户的交互操作触发全屏。

    ```js
    function requestFullScreen(element) {
        var requestMethod = element.requestFullScreen || element.webkitRequestFullScreen || element.mozRequestFullScreen || element.msRequestFullScreen;

        if (requestMethod) {
            requestMethod.call(element);
        } else if (typeof window.ActiveXObject !== "undefined") {
            var wscript = new ActiveXObject("WScript.Shell");
            if (wscript !== null) {
                wscript.SendKeys("{F11}");
            }
        }
    }
    window.addEventListener('click', function() {
        requestFullScreen(document.body);
    });
    ```

## 动画效果

- **播放的时序性**

    动画依据先后次序进行播放，例如下图所示，整个页面的各个模块之间，按着 1 - 6 的次序触发动画效果，使整个页面更有秩序。

    ![animation-sequence.png](/assets/images/web/dashboard/sequence.png)

    而模块的内部同样存在这种次序性，如下图的翻页效果，从第一列到最后一列逐一翻页。

    ![animation show](/assets/images/web/dashboard/animation-show.gif)

    > [代码示例](https://codepen.io/gewenmao/full/ZVoGQa)

- **内部的时序性**

    单个动画效果的内部是由一组动作组成，下图所示的是翻页切换动画的实现步骤（由 UED 同学提供），它可以指导前端 RD 按部就班的实现动画。

    ![animation detail](/assets/images/web/dashboard/animation-detail.png)

## 参考资料

[数据可视化，大屏展示，哪家公司做的不错](https://www.zhihu.com/question/28509135)

[what-is-data-dashboard](https://www.klipfolio.com/resources/articles/what-is-data-dashboard)

[https://smashing.github.io/](http://dashingdemo.herokuapp.com/sample)