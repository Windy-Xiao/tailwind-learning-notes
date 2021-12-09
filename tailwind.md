## Tailwind CSS

tailwind通过功能类构建自定义设计，无需写css。

### 基础样式 - Style - class name

- 通过给元素添加相应**功能类名**来添加样式

  ```html
  <p class="font-medium">
     tailwind css
  </p>
  // font-medium => font-weight:500
  ```



### 响应式设计 - Responsive

- 通过给功能类名添加**前缀**（断点前缀）

  ```html
  // 默认样式是hidden;在>=768px的屏幕下是block
  <div class="hidden md:block">
    tailwind css
  </div>
  ```



#### 默认断点

| 断点前缀 | 最小宽度 | CSS                             |
| :------- | :------: | ------------------------------- |
| `sm`     |  640px   | `@media(min-width:640px){...}`  |
| `md`     |  768px   | `@media(min-width:768px){...}`  |
| `lg`     |  1024px  | `@media(min-width:1024px){...}` |
| `xl`     |  1280px  | `@media(min-width:1280px){...}` |
| `2xl`    |  1536px  | `@media(min-width:1536px){...}` |

`tailwind`断点只包含`min-width`，没有`max-width`

##### 移动优先

先为移动设备设计布局，接着在 `sm` 屏幕上进行更改，然后是 `md` 屏幕...即从小屏幕到大屏幕的顺序

```html
// 屏幕小于640px，文本大小是sm，640-768px之间的大小是base，大于等于768px的大小是lg
<div class="text-sm sm:text-base md:text-lg">
  tailwind css
</div>
```



#### 自定义断点配置`theme-screens`

可以根据项目需求在tailwind配置文件自定义断点的位置和断点名。

```jsx
// tailwind.config.js
module.exports = {
  theme: {
    screens: {
      //@media (min-width: 640px) { ... }
      "tablet":"640px", 
      //@media (min-width: 1024px) { ... }
      "laptop":"1024px",
    }
  }
}

// xxx.html
<div class="text-sm tablet:text-base laptop:text-lg">
  tailwind css
</div>
```



### 状态变体 - Hover、Focus and Others

- 与响应式设计类似，通过为功能类添加适当的状态变体前缀

  [默认变体参考表](https://www.tailwindcss.cn/docs/hover-focus-and-other-states#-4)

- 每个变体有默认启用的这个变体的核心插件，也可通过配置控制是否启用

    - 如：默认启用`hover`变体的有 - `backgroundColor/backgroundOpacity...`

  ```js
  // tailwind.config.js
  module.exports = {
    // ...
    variants: {
      extend: {
        // padding插件启用hover变体
        padding: ['hover'],
      }
    },
  }
  ```

  ```html
  // 启用之后即可使用
  // hover上去样式从bg-red-500变成bg-red-700
  <button class="bg-red-500 hover:bg-red-700 ...">
    Hover me
  </button>
  ```

- 与响应式前缀结合，响应式前缀在前，状态前缀在后`sm:hover:bg-blue-500`

#### 自定义功能类生成变体

- 通过使用 `@variants` 指令包裹住**自定义功能类**来为他们生成状态变体

  ```css
  @variants group-hover, hover, focus {
    .banana {
      color: yellow;
    }
  }
  ```

### 提取组件样式 - components

- 使用`@apply`抽取组件类

  ```css
  .btn {
      @apply py-2 px-4 font-semibold rounded-lg shadow-md;
  }
  
  // 通过@layer把提取的组件样式放在components这层
  @layer components {
    .btn-green {
      @apply bg-green-500 py-2 px-4 font-semibold rounded-lg shadow-md;
  	}
  }
  ```

  ```html
  <button class="btn">
    Click me
  </button>
  ```

- 建议用`@layer components {...}`指令包装自定义组件样式

### 基础样式 - base

- 通过`@layer`指令，将样式移到`@layer base`的同一位置

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

// 通过@layer把样式放在base这层
@layer base {
  h1 {
    @apply text-2xl;
  }
  h2 {
    @apply text-xl;
  }
}
```

### 添加新功能类

- 最简单的功能类生成是样式放入`@layer utilities`中

- 需要生成响应式变体，则再加一层`@variants responsive`

  ```css
  @tailwind base;
  @tailwind components;
  @tailwind utilities;
  
  // 把新添加的功能类放在utilities这层
  @layer utilities {
  	// 生成响应式变体
    @variants responsive {
      .scroll-snap-none {
        scroll-snap-type: none;
      }
      .scroll-snap-x {
        scroll-snap-type: x;
      }
      .scroll-snap-y {
        scroll-snap-type: y;
      }
    }
  }
  ```

  ```html
  <div class="scroll-snap-none md:scroll-snap-x"></div>
  ```

- 生成深色模式，加一层`@variants dark`，并在`tailwind.config.js`中设置`darkMode:media|class`开启深色模式

- 生成状态变体，加一层`@variants 状态`，状态优先级取决于顺序，后者优先

  ```css
  @tailwind base;
  @tailwind components;
  @tailwind utilities;
  
  @layer utilities {
    @variants hover, focus {
      .filter-none {
        filter: none;
      }
      .filter-grayscale {
        filter: grayscale(100%);
      }
    }
  }
  ```



### 动画- Animations

#### 内置动画

添加功能类名即可使用。

`animate-spin`  `animate-ping`  `animate-pulse`  `animate-bounce`

#### 自定义动画

1. 添加新动画`keyframes`

```js
// tailwind.config.js
module.exports = {
  theme:{
    extend:{
      keyframes:{
        grow:{
          '0':{'font-size':'12px'},
          '100%':{'font-size':'50px'}
        }
      }
    }
  }
}
```

2. 将动画注入`animation`

```js
// tailwind.config.js
module.exports = {
  theme:{
    extend:{
      animation: {
        grow:'grow 1s linear 1 forwards',
        'spin-slow': 'spin 3s linear infinite',
      },
      keyframes:{
        grow:{
          '0':{'font-size':'12px'},
          '100%':{'font-size':'50px'}
        }
      }
    }
  }
}
```

3. 使用

注意，使用的时候加上`animate-`

```html
// xxx.html 使用
<div className='animate-grow'>grow and stay</div>
```



### 其他

#### 前缀的顺序

上述讲到了三种前缀，在使用时需要注意顺序。

`响应式变体` : `dark变体` : `状态变体` : `功能类`

```html
<div class="hidden dark:block sm:dark:hover:bg-blue-500">dark model</div>
```

#### @layer - 自定义样式层级

tailwind可用的层有三个。

自定义样式应该通过`@layer base|components|utilities`指定对应的层，tailwind会将CSS移至与相应 `@tailwind` 规则相同的位置，以避免发生特定性问题。

```css
@tailwind base;  // base层，一些基础|全局样式的设置
@tailwind components;
@tailwind utilities;

// base styles
@layer base {
  *{
    @apply m-0 p-0
  }
  li{
    list-style:none;
  }
}

// 自定义组件
@layer components {
  .btn-primary{
    @apply bg-green-400 text-white rounded-md pr-2 pl-2 hover:bg-green-500
  }
}

// 自定义功能类
@layer utilities {
  @variants hover, focus{
    .filter-none{
      filter:none;
    }
  }
}
```

