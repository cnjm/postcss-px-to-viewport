<!--
 * @Description: 这是**页面（组件）
 * @Date: 2022-04-13 14:58:24
 * @Author: chenjiaming
 * @LastEditors: chenjiaming
 * @LastEditTime: 2022-04-13 15:35:22
-->
# postcss-px-to-viewport
[基于postcss-px-to-viewport插件稍加改造的](https://github.com/evrone/postcss-px-to-viewport)

## 简介（为什么有）

一直希望在vite 中 使用 postcss-px-to-viewport 时可以设置不同的设计尺寸，比如vant是375设计的

而我们可能是750或其他尺寸，一直找了好久都没有合适的解决方案（有个webpack的，不过vite的貌似不行），

干脆就稍加改了一下postcss-px-to-viewport的代码

其实就是在src/index.js中加入了以下代码，把当前文件路径暴露出去，接收一个新的viewportWidth并赋值给opts

具体可以看下源代码
```js
if(opts.customFun){
  opts.viewportWidth = opts.customFun({file:rule.source.input.file});
}
```

