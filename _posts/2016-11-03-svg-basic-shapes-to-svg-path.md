---
layout: post
title: "SVG 基本形状转换为路径"
description: ""
category: svg
tags: [svg]
---
{% include JB/setup %}
SVG 的提供了rect、circle、ellipse、line、polyline、polygon 六种基本形状，而 path 的功能更为强大，这六种基本形状均可由 path 实现。

<!-- more -->

1. rect to path

    如下图所示，一个 rect 是由 4 个弧和 4 个线段构成；如果 rect 没有设置 rx 和 ry 则 rect 只是由 4 个线段构成。rect 转换为 path 只需要将 A ~ H 之间的弧和线段依次实现即可。

    ![rect2path](/assets/images/svg/rect2path.png)

        function rect2path(x, y, width, height, rx, ry) {
            /*
            * rx 和 ry 的规则是：
            * 1. 如果其中一个设置为 0 则圆角不生效
            * 2. 如果有一个没有设置则取值为另一个
            */
            rx = rx || ry || 0;
            ry = ry || rx || 0;

            // 求相对前一个点的偏移坐标
            var offsets = [
                    [rx, -ry], // B 相对 A 的坐标
                    [width - rx - rx, 0],  // C 相对 B 的坐标
                    [rx, ry],
                    [0, height - ry -ry],
                    [-rx, ry],
                    [rx + rx -width, 0],
                    [-rx, -ry],
                    [0, ry + ry - height] // A 相对 H 的坐标
                ],
                // 移动到初始位置 A 点，如果没有设置圆角，则 A 与 T 相同 
                path = ['M', x, y + ry];

            for (var i = 0; i < 8; i ++ ) {
                 var offset = offsets[i];
                 if (i % 2 === 0) {
                    if (rx && ry) {
                        var arc = ['a', rx, ry, 0, 0, 1, offset[0], offset[1]];
                        path = path.concat(arc);
                     }
                 } else {
                    if (offset[0]) {
                        var line = ['h', offset[0]];
                        path = path.concat(line)

                    } else {
                        var line = ['v', offset[1]];
                         path = path.concat(line)
                     }
                 }
            }

            // 闭合
            path.push('z');

            return path.join(' ');
        }


2. circle/ellipse to path

    圆可视为是一种特殊的椭圆，即 rx 与 ry 相等的椭圆，所以可以放在一起讨论. 椭圆可以用贝赛尔曲线近似模拟，相关算法可以阅读 [Drawing a circle with Bézier Curves](http://www.whizkidtech.redprince.net/bezier/circle/) 和 [How to create circle with Bézier curves](http://stackoverflow.com/questions/1734745/how-to-create-circle-with-b%C3%A9zier-curves). 如下图所示，从 A ~ D 依次做贝塞尔曲线，A、B 之间的贝塞尔曲线由命令`c` 绘制，而之后的曲线则由 `s` 命令绘制。


    ![ellipse2path](/assets/images/svg/ellipse2path.png)


        function ellipse2path(cx, cy, rx, ry) {
            var K = (Math.sqrt(2) - 1) * 4 / 3;
            pOffsets = [
                [rx, -ry],
                [rx, ry],
                [-rx, ry],
                [-rx, -ry],
            ],
            cOffsets = [
                [(1 - K)*rx, -ry],
                [rx, (1 - K)*ry],
                [(K - 1)*rx, ry],
                [-rx, (K - 1)*ry]
            ],
            path = ['M', cx - rx, cy];

            for (var i = 0; i < 4; i++) {
                if (i === 0) {
                    var curv = ['c', 0, -K*ry, cOffsets[i][0], cOffsets[i][1], pOffsets[i][0], pOffsets[i][1]];
                } else {
                    var curv = ['s', cOffsets[i][0], cOffsets[i][1], pOffsets[i][0], pOffsets[i][1]];
                }
                path = path.concat(curv);
            }

            path.push('z');

            return path.join(' ');
        }
4. line to path
    
    相对来说比较简单，可以由 lineto 命令实现.

        function line2path(x1, y1, x2, y2) {
            x1 = x1 || 0;
            y1 = y1 || 0;
            x2 = x2 || 0;
            y2 = y2 || 0;

            var path = ['M', x1, y1, 'l', x2, y2];
            return path.join(' ');
        }
5. polyline to path

    // points = [x1, y1, x2, y2, x3, y3 ...];
    function polyline2path (points) {
        var path = ['M', points.join(' ')];
        return path.join(' ');
    
    }

6. polygon to path

    // points = [x1, y1, x2, y2, x3, y3 ...];
    function polyline2path (points) {
        var path = ['M', points.join(' '), 'z'];
        return path.join(' ');
    }

直线、折线、多边形的转换为path比较简单，而且方法也类似。

可以参考: https://github.com/nfroidure/svgicons2svgfont/blob/master/src/svgshapes2svgpath.js
