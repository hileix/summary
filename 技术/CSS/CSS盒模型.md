# CSS 盒模型

盒模型由 content(内容) + padding(填充) + border(边框) + margin(边距) 组成。

有两种类型的盒模型：

- W3C(标准)盒模型
- IE 盒模型

这两类盒模型在指定 width 属性时有区别。

## 1. W3C 盒模型

可以通过设置 CSS 的 `box-sizing` 属性值为 `content-box` 指定盒模型为 `W3C 盒模型`。

当指定 `W3C 盒模型` 的 with 时，其指定的是 `content` 的宽度。

## 2. IE 盒模型

可以通过设置 CSS 的 `box-sizing` 属性值为 `border-box` 指定盒模型为 `IE 盒模型`。

当指定 `IE 盒模型` 的 with 时，其指定的是 `content + padding + border` 的宽度。
