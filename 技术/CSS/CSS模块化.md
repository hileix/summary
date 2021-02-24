# CSS 模块化

<img src="../../思维导图/CSS模块化.png">

- [BEM 命名法](./BEM命名法.md)
- CSS Module
- CSS in JS

模块化优点：

- 避免 `全局样式冲突`
- 更高的 `复用性`
- 更高的 `可维护性`

## 1. BEM 命名法

见 [BEM 命名法](./BEM命名法.md)

## 2. CSS Module

http://www.ruanyifeng.com/blog/2016/06/css_modules.html

## 3. CSS in JS

styled-components

https://zhuanlan.zhihu.com/p/103522819

缺点：

## 4. 总结

BEM 相比其他 CSS 模块化方案来说，需要编写更长的类名（结合 scss/less 使用，可以减少很多的字符输入），并且在进行 css 压缩的时候，css 的类名是不会被压缩的。但 css 资源一般都会使用 gzip 压缩，且因为 BEM 中命名相同的地方很多，压缩下来不用担心体积会大很多。

BEM 命名法完美契合组件化开发，可读性高，是我最喜欢的 CSS 模块化方案。

在开发体验上：

BEM：因为只修改了 css，所以 loader 只会转换 css，替换掉页面中的样式，页面不会刷新。
css module 和 styled-components：修改了 css，但因为需要在 dom 上插入生成的唯一 class，所以页面会刷新。

开发体验上来说，我觉得 BEM 更好。
