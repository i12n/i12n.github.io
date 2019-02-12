---
layout: post
title: "TouchEvent"
description: ""
category: web
tags: [touch, event]
---
{% include JB/setup %}

当手指在触摸屏或触摸板上触摸时, 会触发 [TouchEvent](https://developer.mozilla.org/en-US/docs/Web/API/TouchEvent) 事件, 本文对 TouchEvent 做简单的介绍。

<!-- more -->

## TouchEvent 事件类型 ##

TouchEvent 有 4 种类型：
- touchstart
- touchmove
- touchend
- touchcancel

touchmove、touchend 事件中的 target 会与 touchstart 保持一直, 即便是触摸点已经移出 target 目标元素。举个例子, 当在触摸屏滑动某个元素（该元素可以 scroll）的时候, 手指一直按住滑动, 如果滑到该元素区域外面, 该元素依然会发生滚动。

## TouchEvent 默认行为 ##

当元素的 TouchEvent 被触的时候, 元素默认会激活 MouseEvents、click、scroll 等事件 ([具体细节](https://w3c.github.io/touch-events/#mouse-events))。MouseEvents、click 触发顺序如下：

1. `touchstart`
2. Zero or more `touchmove` events, depending on movement of the finger
3. `touchend`
4. `mousemove` (for compatibility with legacy mouse-specific code)
5. `mousedown`
6. `mouseup`
7. `click`

TouchEvent 是 cancelable 的, 可以使用 [preventDefault](https://w3c.github.io/touch-events/#dfn-canceled-event) 取消这种默认行为, 从而不触发其他事件。可以在 touchstart、touchmove、touchend 中的任意一个事件中通过 preventDefault 来取消 MouseEvents、click 被触发, 具体见 [代码示例](https://codepen.io/gewenmao/pen/daeazX)。

为了兼容触屏设备和鼠标设备, 可以做如下兼容处理：

```js
// concurrent "touch AND mouse/keyboard" event binding

// set up event listeners for touch
target.addEventListener('touchend', function(e) {
  // prevent compatibility mouse events and click
  e.preventDefault();
  ...
});
...

// set up event listeners for mouse/keyboard
target.addEventListener('click', ...);
...
```

当 `touchmove` 被触发的时候, 如果元素 scrollable 会触发 `scroll`。在 touchstart 或 touchmove 事件中通过 preventDefault 可以取消 `scroll` 被触发。在 touchend 中调用 preventDefault 可能会出现如下错误：

```js
[Intervention] Ignored attempt to cancel a touchend event with cancelable=false, for example because scrolling is in progress and cannot be interrupted.
```

> touchEvent 结束, scroll 仍然在进行中

除了 `preventDefault`, 阻止默认行为也可以使用 `touch-action` (iOS Safari 不支持):

```css
html {
    touch-action: none;
}
```

## TouchEvent 延迟问题 ##

- **300ms tap 延迟**

    > Mobile browsers applied a 300-350ms delay between touchend and click while they waited to see if this was going to be a double-tap or not, since double-tap was a gesture to zoom into text.  -- *[300ms tap delay, gone away](https://developers.google.com/web/updates/2013/12/300ms-tap-delay-gone-away)*

    300ms 时延解决方法：

    1. 禁止 pinch-zoom (禁止双击缩放)
        - viewport (Chrome/Firefox)

        ```html
        <meta name="viewport" content="width=device-width">
        ```

        - touch-action

        ```css
        html {
            touch-action: manipulation;
        }
        ```

    2. 通过 touchEvent 模拟实现 click

        - FastClick

        - zepto.tap (存在穿透问题)

- **scroll 延迟**

    > Before scrolling, need to wait for the results of any `touchstart` and `touchmove` handlers, which may prevent the scroll entirely by calling preventDefault() on the event. -- *[Passive event listeners](https://github.com/WICG/EventListenerOptions/blob/gh-pages/explainer.md#the-problem)*

    scroll 延迟解决方法, 配置 touch 事件的 `passive` 属性:

    ```js
    elem.addEventListener('touchstart', handler,
    {passive: true});
    ```

    当 `passive: true` 声明 handler 内部不会调用事件的 `preventDefault` 函数, `scroll` 无需延迟等待

## TouchEvent Emulation ##

在一些特殊场景, 浏览器并不支持 `TouchEvent`, 但是需要触发 `TouchEvent` 事件, 例如 ui 测试等情况。这时可以通过代码模拟用户的触摸操作, [参考示例](https://github.com/segmentio/nightmare/issues/1242)。

```js
var target = document.querySelector(selector)
    if (!target) return

    const rect = target.getBoundingClientRect()
    const x = rect.left + rect.width / 2
    const y = rect.top + rect.height / 2

    const touchObj = new Touch({
      identifier: Date.now(),
      target: target,
      clientX: x,
      clientY: y,
      radiusX: 2.5,
      radiusY: 2.5,
      rotationAngle: 10,
      force: 0.5
    })

    const touchEvent = new TouchEvent('touchend', {
      cancelable: true,
      bubbles: true,
      touches: [touchObj],
      targetTouches: [],
      changedTouches: [touchObj],
      shiftKey: false
    })

    target.dispatchEvent(touchEvent)
```

需要注意的是, 对于 zepto.tap 这种事件需要做额外的兼容：

```js
document['ontouchstart'] = null; // 支持 touch 事件 https://github.com/madrobby/zepto/blob/e18bef37a5c14d0fc640c82fa0d113dcd02e40d1/src/touch.js#L69-L78
$.touch.setup(); // 重新初始化
```

并且, 依次模拟 `touchstart` 和 `touchend` 才能触发 `tap`, 具体见代码[模拟 tap 事件](https://codepen.io/gewenmao/pen/oJMewB)

另外一种思路就是, chrome 浏览器 在 开发模式下开启手机模拟器, 对于 Electron 应用, 则可以 使用 [Debugger
](https://electronjs.org/docs/api/debugger#debuggersendcommandmethod-commandparams-callback) 开启 Touch 模拟.

```js
newWindow.webContents.debugger.attach(1.2);
newWindow.webContents.debugger.sendCommand('Emulation.setTouchEmulationEnabled', {
  enabled: true,
  configuration: 'mobile',
 }, () => {
 });
```

上面的代码在 Electron@2.0 版本有效, [3.0 版本失效](https://github.com/electron/electron/issues/15875)。

## 参考资料 ##

- [w3c touch-events](https://w3c.github.io/touch-events)

- [w3 touch-events](https://www.w3.org/TR/touch-events)

- [300ms tap delay, gone away](https://developers.google.com/web/updates/2013/12/300ms-tap-delay-gone-away)

- [chrome-safari-touchmove-preventdefault-treated-as-passive](https://www.zhangxinxu.com/wordpress/2018/07/chrome-safari-touchmove-preventdefault-treated-as-passive/)

- [touch-action](https://developer.mozilla.org/zh-CN/docs/Web/CSS/touch-action)

- [Improving Scroll Performance with Passive Event Listeners](https://developers.google.com/web/updates/2016/06/passive-event-listeners)

- [Passive event listeners](https://github.com/WICG/EventListenerOptions/blob/gh-pages/explainer.md)

- [passive 的事件监听器](http://www.cnblogs.com/ziyunfei/p/5545439.html)