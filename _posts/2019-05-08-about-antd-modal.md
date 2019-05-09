---
layout: post
title: "Ant Design Modal 总结"
description: ""
category: web
tags: [ modal, antd ]
---

当用户需要处理事务，又不希望跳转页面以致打断工作流程时，可以使用模态对话框在当前页面正中打开一个浮层，承载相应的操作 ([Ant Design Modal 组件](https://ant.design/components/modal-cn/))，
例如，表单编辑/提示警告等应用场景，都可以弹出一个对话框来交互，Ant Design 提供了 Modal 组件给开发者使用。

<!-- more -->

## 模态对话框的实现

### 蒙层与窗体

Ant Design 的 [Modal](https://ant.design/components/modal-cn/)  对话框由两部分组成，蒙层部分和窗体部分。蒙层将页面的其他内容遮挡，窗体部分承载交互内容。Modal 的蒙层和窗体是两个同级的元素，这么做的好处是可以根据需要来配置蒙层是否展示，而不影响窗体部分。两者都是采用 `position: fixed` 相对于浏览器窗口进行定位，以铺满整个浏览器窗口。

  ```html
  <div>
    <div class="ant-modal-mask"></div>
    <div class="ant-modal-wrap " role="dialog"></div>
  </div>
  ```

  ```css
  .ant-modal-mask {
    position: fixed;
  }
  .ant-modal-wrap {
    position: fixed;
  }
  ```

### 组件挂载

Modal 组件 的实现依赖于 [rc-dialog](https://www.npmjs.com/package/rc-dialog)，rc-dialog 是一个弹框组件。通常，默认情况下对话框要挂载到 DOM 中的 `body` 元素上，以避免对话框被其他的元素遮挡；当然，也可以自定义对话框要挂载的元素，以满足某些业务场景的需求，可以参考 [rc-dialog 实现](https://github.com/react-component/dialog/blob/f43c96f9883a420c8411b152435871bd56903f45/src/DialogWrap.tsx#L64)。

React 的元素之间多数情况都是以层级结构存在的，数据由父级元素流向子级元素。对话框直接挂载到 `body` 元素上，打破了这种层级结构，使父子组件之间的数据通信等受到到影响。在 React 16 版本之后引入 [Portals](https://reactjs.org/docs/portals.html)，很好的解决了这个问题，可以参考[rc-dialog 实现](https://github.com/react-component/dialog/blob/f43c96f9883a420c8411b152435871bd56903f45/src/DialogWrap.tsx#L79)

### 垂直居中

对话框的位置根据需要进行调整，可以通过样式调整对话框的位置，也可以设置为垂直居中。Modal 的垂直居中采用的是以行内元素设置 `vertical-align: middle` 来实现的：

  ```html
  <div class="ant-modal-wrap ant-modal-centered">
    ::before
    <div role="document" class="ant-modal"></div>
  </div>
  ```

  ```css
  .ant-modal-centered::before {
    display: inline-block;
    width: 0;
    height: 100%;
    vertical-align: middle;
  }
  .ant-modal-centered .ant-modal {
    display: inline-block;
    vertical-align: middle;
  }
  ```

伪元素 `::before` 辅助 `ant-modal` 元素垂直居中，有几点要注意的地方：

- 伪元素 `::before` 高为 `100%`；`::before` 高小于 `100%`， `ant-modal` 就不会垂直居中。
- 伪元素 `::before` 宽为 `0`；但是当 `ant-modal` 的宽度大于 `100%`，`ant-modal`就会折行。

### 隐藏与销毁

对话框的关闭是通过隐藏实现的，将蒙层和窗体设置为 `display: none`：

```html
<div>
  <div class="ant-modal-mask ant-modal-mask-hidden"></div>
  <div class="ant-modal-wrap " role="dialog" style="display: none;"></div>
</div>
```

```css
.ant-modal-mask-hidden {
  display: none;
}
```

而通过 [Modal.method](https://ant.design/components/modal-cn/#Modal.method()) 方法生成的对话框在关闭的时候，是将对话框进行了销毁，从 DOM 树中移除，具体见 [Modal 源码](https://github.com/ant-design/ant-design/blob/77c431b1805282b15bd2735b190018ea31caa178/components/modal/confirm.tsx#L149)

## Modal 对话框的使用

- 对话框要在浏览窗口中完全可见，将对话框的 width 适配浏览窗口。如果对话框的 width 大于浏览窗口的宽度，对话框会被折行导致当前窗口看不见对话框。

  ```jsx
    <Modal width="90vw" />
  ```

- 如果对话框内部内容确实比较长，可以通过对话框内部设置 scroll 来实现。

  ```jsx
    <Modal width="90vw">
      <div style={{width: '100%', overflowX: 'auto'}}>
        <div style={{width: '2000px'}}>
          {/* content */}
        </div>
      </div>
    </Modal>
  ```

- 通过 visible 设置对话框的打开与关闭，在对话框打开与关闭过程中有动画效果。

  ```jsx
    <Modal visible={this.state.visible} /> // 正确打开方式

    this.state.visible && <Modal visible> //错误打开方式
  ```

- 对于`提示`/`警告`/`错误`等提示框 可以考虑使用 [Modal.method()](https://ant.design/components/modal-cn/#Modal.method())
