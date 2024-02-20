Web Components是一组技术，允许开发者创建封装好的、可重用的自定义元素，这些元素的内部实现细节不会影响到文档的其他部分，反之亦然。Web Components由三个主要的技术组成：

1. Custom Elements: 允许开发者定义新的HTML元素。通过创建一个类并将其与一个新的元素名称关联，开发者可以指定新元素的行为和属性。

代码示例：
```javascript
class MyCustomElement extends HTMLElement {
  constructor() {
    super(); // 总是首先调用super()构造函数
    // 元素的初始化代码
  }
}

// 定义新的元素
customElements.define('my-custom-element', MyCustomElement);
```

2. Shadow DOM: 提供了封装的样式和标记的DOM。Shadow DOM允许开发者将一个隐藏的、独立的DOM附加到一个元素上。

代码示例：

```javascript
class MyShadowDomElement extends HTMLElement {
  constructor() {
    super();
    // 附加一个shadow root
    const shadowRoot = this.attachShadow({ mode: 'open' });
    // 添加一些HTML到shadow root
    shadowRoot.innerHTML = `<p>Shadow DOM content</p>`;
    // 添加一些封装的样式
    const style = document.createElement('style');
    style.textContent = `p { color: red; }`;
    shadowRoot.appendChild(style);
  }
}

customElements.define('my-shadow-dom-element', MyShadowDomElement);
```

3. HTML Templates (<template> 和 <slot>): <template>标签允许声明一段不会立即渲染的HTML，而<slot>元素是一个占位符，用于插入来自外部HTML的内容。

代码示例：

```javascript
<!-- 定义模板 -->
<template id="my-template">
  <div>
    <slot name="my-slot">Default content</slot>
  </div>
</template>

<script>
  class MyTemplateElement extends HTMLElement {
    constructor() {
      super();
      // 获取模板内容
      const template = document.getElementById('my-template').content;
      // 附加一个shadow root
      const shadowRoot = this.attachShadow({ mode: 'open' });
      // 克隆模板内容并添加到shadow root
      shadowRoot.appendChild(template.cloneNode(true));
    }
  }

  customElements.define('my-template-element', MyTemplateElement);
</script>
```

使用时：

```javascript
<my-template-element>
  <span slot="my-slot">Replaced content</span>
</my-template-element>
```

Web Components的好处包括封装性、可重用性、维护性和互操作性。但是，它们在旧版浏览器中可能需要polyfills来提供支持，并且开发者需要熟悉这些API的使用方式。