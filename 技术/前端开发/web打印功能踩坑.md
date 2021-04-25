# web 打印功能踩的坑

1. 如何将打印的内容在下一页中打印？

```css
@media print {
  p {
    page-break-after: always;
  }
}
```

每一个 p 标签，都会在一个新页面中进行打印。

2. 当设置了 `page-break-after` 时无效，可以试试设置以下属性：

```css
@media print {
  body {
    height: auto;
  }
}
```

## web 打印相关的博客

- https://www.cnblogs.com/goloving/p/13954873.html
