# BEM 命名法

BEM 的意思就是块（block）、元素（element）、修饰符（modifier）,是由 Yandex 团队提出的一种前端命名方法论。

catui 使用 BEM 命名法。

## 1 命名约定模式

```css
.block {
}
.block__element {
}
.block--modifier {
}
```

- .block 表示更高级别的抽象或组件
- .block\_\_element 表示 `.block` 的后代，`.block` 就是由这些 `后代` 组成的
- .block--modifier 表示 `.block` 的不同状态

`__`（双下划线）用于连接 `element`，`--`（双中划线）用于连接 `modifier`，`-` （单中划线）用于连接单词

```css
.site-header {
}
.site-header__logo {
}
.site-header__logo--hover {
}

.site-header__menus {
}
.site-header__menus--active {
}
```

## 2 React 组件 + SCSS + BEM 实际使用

比如我们要开发一个 MainHeader 组件，作用网站的 header

### 2.1 结构

```tsx
import React, { useState } from 'react';
import classNames from 'classnames';

const MainHeader = () => {
  const [isActive, setIsActive] = useState(false);

  return (
    <header className="main-header">
      {/* logo */}
      <img src="xxx" className="main-header__logo">
      {/* search */}
      <input
        className={classNames('main-header__search', {
        'main-header__search--active': isActive
        })}
        type="text"
      />
      {/* user avatar */}
      <img src="yyy" className="main-header__user-avatar" />
    </header>
  )
};

export default MainHeader;
```

- `main-header` 由 `logo`, `search` 和 `user avatar` element 组成
- `mian-header__search--active` 带有 `active` 修饰，表示 `search` element 的 `active` 状态

### 2.2 SCSS 样式

```scss
.main-header {
  &__logo {
  }
  &__search {
    &--active {
    }
  }
  &__user-avatar {
  }
}
```

- 利用 SCSS 嵌套的功能，可以快速写出 `element` 的类

### 2.3 React 组件 + SCSS + BEM 总结

#### 1. `block` 的值为 `组件名`

#### 2. 组件内部所有的后代都为 `element`，这些 `element` 组成了整个组件
