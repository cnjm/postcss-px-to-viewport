# cnjm-postcss-px-to-viewport
[基于postcss-px-to-viewport插件稍加改造的](https://github.com/evrone/postcss-px-to-viewport)
和使用postcss-px-to-viewport 是基本一样的，只是多处理了vant等375尺寸问题，你也可以选择克隆到自己仓库中使用，也可以直接使用笔者改的

顺便把插件升级了postcss到v8+,规避了postcss.plugin was deprecated. Migration guide:  的警告，

```bash
npm i cnjm-postcss-px-to-viewport
锁死v0.0.2,则为不更新postcss版本
```

## 简介（为什么有）

一直希望在**vite** 中 使用 postcss-px-to-viewport 时可以设置不同的设计尺寸，比如**vant**是375设计的

而我们可能是750或其他尺寸，一直找了好久都没有合适的解决方案（有个webpack的，不过vite的貌似不行），如果有知道其他好的解决方法的，麻烦告知一下，感激不尽！

干脆就稍加改了一下postcss-px-to-viewport的代码

其实就是在src/index.js中加入了以下代码，把当前文件路径暴露出去，接收一个新的viewportWidth并赋值给opts

具体可以看下源代码
```js
if(opts.customFun){
  opts.viewportWidth = opts.customFun({file:rule.source.input.file});
}
```

配置时使用cnjm-postcss-px-to-viewport

```js
const path = require("path");
module.exports = () => {
  return {
    plugins: {
      autoprefixer: {
        overrideBrowserslist: ["Android 4.1", "iOS 7.1", "Chrome > 31", "ff > 31", "ie >= 8"],
      },
      // 修改插件名称
      "cnjm-postcss-px-to-viewport": {
        unitToConvert: "px", // 要转化的单位
        viewportWidth: 750, // UI设计稿的宽度
        unitPrecision: 6, // 转换后的精度，即小数点位数
        propList: ["*"], // 指定转换的css属性的单位，*代表全部css属性的单位都进行转换
        viewportUnit: "vw", // 指定需要转换成的视窗单位，默认vw
        fontViewportUnit: "vw", // 指定字体需要转换成的视窗单位，默认vw
        selectorBlackList: ["ignore"], // 指定不转换为视窗单位的类名，
        minPixelValue: 1, // 默认值1，小于或等于1px则不进行转换
        mediaQuery: true, // 是否在媒体查询的css代码中也进行转换，默认false
        replace: true, // 是否转换后直接更换属性值
        exclude: [], // 设置忽略文件，用正则做目录名匹配
        landscape: false, // 是否处理横屏情况
        // 如果没有使用其他的尺寸来设计，下面这个方法可以不需要，比如vant是375的
        customFun: ({ file }) => {
          // 这个自定义的方法是针对处理vant组件下的设计稿为375问题
          const designWidth = path.join(file).includes(path.join("node_modules", "vant")) ? 375 : 750;
          return designWidth;
        },
      },
    },
  };
};

```


https://github.com/vitejs/vite/issues/4653  多个配置,这将导致插件处理两次，**优点缺点都很明显，具体怎么用，见人见智了**

```typescript
const px2viewport = require("postcss-px-to-viewport");
const autoprefixer = require("autoprefixer");
module.exports = () => {
  return {
    plugins: [
      autoprefixer({
        overrideBrowserslist: ["Android 4.1", "iOS 7.1", "Chrome > 31", "ff > 31", "ie >= 8"],
      }),
      px2viewport({
        unitToConvert: "px", // 要转化的单位
        viewportWidth: 375, // UI设计稿的宽度
        unitPrecision: 6, // 转换后的精度，即小数点位数
        propList: ["*"], // 指定转换的css属性的单位，*代表全部css属性的单位都进行转换
        viewportUnit: "vw", // 指定需要转换成的视窗单位，默认vw
        fontViewportUnit: "vw", // 指定字体需要转换成的视窗单位，默认vw
        selectorBlackList: ["ignore"], // 指定不转换为视窗单位的类名，
        minPixelValue: 1, // 默认值1，小于或等于1px则不进行转换
        mediaQuery: true, // 是否在媒体查询的css代码中也进行转换，默认false
        replace: true, // 是否转换后直接更换属性值
        exclude: [/^(?!.*node_modules\/vant)/], // 设置忽略文件，用正则做目录名匹配
        landscape: false, // 是否处理横屏情况
      }),
      px2viewport({
        unitToConvert: "px", // 要转化的单位
        viewportWidth: 750, // UI设计稿的宽度
        unitPrecision: 6, // 转换后的精度，即小数点位数
        propList: ["*"], // 指定转换的css属性的单位，*代表全部css属性的单位都进行转换
        viewportUnit: "vw", // 指定需要转换成的视窗单位，默认vw
        fontViewportUnit: "vw", // 指定字体需要转换成的视窗单位，默认vw
        selectorBlackList: ["ignore"], // 指定不转换为视窗单位的类名，
        minPixelValue: 1, // 默认值1，小于或等于1px则不进行转换
        mediaQuery: true, // 是否在媒体查询的css代码中也进行转换，默认false
        replace: true, // 是否转换后直接更换属性值
        exclude: [/node_modules\/vant/i], // 设置忽略文件，用正则做目录名匹配
        landscape: false, // 是否处理横屏情况
      }),
    ],
  };
};
```
