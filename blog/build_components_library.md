---
slug: react_library
title: 基于React打造组件库
authors:
  name: 马兴原
  title: 前端团队
  url: https://github.com/mxydl2009
  image_url: "https://tvax1.sinaimg.cn/crop.0.0.812.812.1024/967d3c97ly8g4lsqc2llcj20mk0mkdi4.jpg?KID=imgbed,tva&Expires=1660833238&ssig=5cj5Vx5jK5"
tags: [前端, react, 组件库]
---

# react-component-library

## 介绍

基于React和storybook打造自己的组件库，使用TypeScript编写

### 软件架构

#### 完成一个组件库需要考虑的问题

##### 规划代码结构（项目目录结构）

与一般的web项目不同，不需要页面，只需要有JavaScript代码和样式相关的代码

##### 样式解决方案

良好的样式解决方案，如何组织合理的CSS代码架构形态

##### 组件需求分析与编码

组件需求 -> 具体编码

需求从简单到复杂 -> 编码从简单到复杂

##### 组件测试用例分析与编码

组件是独立的逻辑单元，适合单元测试

##### 代码打包输出和发布

模块打包输出需要满足的各种规范，如ESM、CommonJS等等

##### 持续集成与部署

CI/CD、文档生成等等

#### 创建项目

\- `npx create-react-app mxyrc --template typescript`, 项目名字为mxyrc(mxy react components), `--template typescript`提供对typescript的支持，根据官方文档，需要将项目中的.js文件重命名为.ts(x)文件。

##### 代码结构安排

组织代码的方式并没有统一的模板规范，有一些经验性的结论可以作为参考，实际上，没必要在开始时深谋远虑去考虑项目结构，因为在项目逐渐庞大的过程中，会逐渐改变原来的项目结构。

- 按照路由来组织代码

- 按照功能来组织代码

有一个原则：**避免创建太深嵌套的项目结构**

刚开始的项目结构如下图：

<!-- ![截屏2021-06-21 下午8.39.20](/Users/maxingyuan/learn/笔记/react/打造自己的组件库-图片资源/原始的项目结构示意图.png) -->

描述如下

- components中组织具体的组件，每个组件使用Pascal命名
  - 每个组件结构中包含了.tsx组件逻辑代码、style.scss为组件样式代码和.test.tsx组件单元测试代码
- styles目录下组织全局样式，包含了全局变量、全局的mixin等
- index.ts是组件库的入口文件

##### 代码规范

 tsc只是一个编译器，无法捕捉到代码规范层面的东西，如使用相等还是严格相等这样的符号，代码规范是人为指定的应该使用哪种方式，编译器只能辅助得到代码是什么样的，无法帮助判定，因此需要人为指定一个确定的规范。

cra生成的项目已经指定了ESLint的代码规范，但只是一个针对常见错误的最小规则集，如果需要扩展，则要手动添加扩展的配置文件。

VScode集成的ESLint插件会在编辑器里提示错误信息，但无法影响终端和浏览器端的错误信息，而cra指定的规范会影响终端和浏览器端的错误信息。

具体方式可以参考[官方文档](https://create-react-app.dev/docs/setting-up-your-editor)

ESlint插件目前的版本已经可以支持TypeScript了，所以在vscode中不需要配置也可以正常在编辑器里提示错误信息。如果没有正常提示，则重启编辑器试试。如果还是没有，则可以在项目根目录下创建`./vscode/settings.json`配置文件，输入以下内容表示要在编辑器里使用ESLint验证JS和TS文件

```js
{
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    "typescript",
    "typescriptreact"
  ]
}
```

##### tsconfig.json的配置问题

由于react升级到了17版本，会导致tsconfig.json中的jsx配置项为"react-jsx"，但TypeScript的低版本（4.1以下）会报错，因此需要做如下的改变（[参考官方文档](https://code.visualstudio.com/docs/typescript/typescript-compiling#_using-newer-typescript-versions)）：

- 选择项目中使用的TypeScript版本为高版本（高于4.1）：同时按下shift+Command+P，填写select typescript版本，选择工作区版本（即项目的node_modules中下载的TypeScript包）。如果没有出现工作区版本的选项，重启编辑器即可。

- 通过上述操作，会在根目录下创建`./vscode/settings.json配置文件`，包含了如下配置信息：

  ```js
  {
    "typescript.tsdk": "node_modules/typescript/lib"
  }
  ```

##### 样式解决方案分析

###### Inline CSS

利用的是style属性，官方建议CSS class比内联样式性能更好

###### CSS in JS

主要是利用JS的可编程性来增加CSS的可编程性，如styled-component解决方案

###### Sass/Less

使用编程语言来对Sass/Less进行编译为CSS，在编写Sass/Less时可以具有可编程性，我们这里采用该方案

##### 样式文件结构

<!-- <img src="/Users/maxingyuan/learn/笔记/react/打造自己的组件库-图片资源/样式文件结构示意图.png" alt="样式文件结构示意图" style="zoom:67%;" /> -->

##### 样式文件组织

- styles文件夹下是全局要使用的变量、mixins、函数以及其他

- components 中每个组件定义自己的样式文件

- Styles/index.scss负责引用全局文件以及组件的样式文件

##### 样式变量分类

为了让样式达到灵活的可配置的模式，对样式变量要进行如下的分类

###### 基础色彩系统

###### 字体系统

###### 表单系统

###### 按钮

###### 边框和阴影

###### 可配置开关

对某些功能进行打开和取消

##### 添加色彩体系

###### 系统色板

基础色板 + 中性色板（黑、白、灰）

###### 产品色板

品牌色板（突出品牌的主色primary色和辅助色secondary色） + 功能色板（在强调某些的功能）

###### 定义全局变量用于色彩体系

在`styles/_variables.scss`中定义用于色彩体系的Sass变量

```scss
// 定义项目中的全局CSS变量

// 中性色板, 使用!default的方式变量赋值时，如果当前变量已经被赋值，则该次赋值无效，否则会被赋值成功；
$white: #fff !default;
$grey-100: #f8f8fa !default;
$grey-200: #e9ecef !default;
$grey-300: #dee2e6 !default;
$grey-400: #ced4da !default;
$grey-500: #adb5bd !default;
$grey-600: #6c757d !default;
$grey-700: #495057 !default;
$grey-800: #343a40 !default;
$grey-900: #212529 !default;
$black: #000 !default;
```

##### 添加字体体系

###### 字体家族font family

###### 字体大小

使用rem作为基本大小来定义所有的字体相关的大小，这样便于做响应式排版

###### 字重

###### 行高

###### 标题大小

###### 链接样式（颜色、悬浮时）

##### 添加浏览器统一样式

添加[normalize.css](https://necolas.github.io/normalize.css/)，为不同浏览器提供相同的表现形式。`.styles/_reboot.scss`文件中将normalize.css中的属性值替换为我们之前定义的全局变量。

##### 样式文件统一导出

`.styles/index.scss`中统一导入变量定义文件和布局样式文件，注意这里的两个样式文件在Sass中都是partial文件，使用如下方式导入

```scss
// 导入变量相关的partial
@import 'variables.scss';

// 导入layout相关
@import 'reboot.scss';
```

#### Button组件

##### 需求分析

组件应该支持哪些属性，或者说哪些props和state来控制组件的表现和行为。

按钮的外观表现主要由type、size决定，并且按钮组件最终渲染结果会是button元素或者a元素，由type的值来决定。

按钮的行为包含了点击、hover、focus，点击通过事件处理函数解决，hover和focus的表现通过相关样式解决。

###### 不同的Button类型type（使用class来添加样式）

- primary类型

- default类型

- danger类型

- link类型（链接型）

###### 不同的Button大小size（使用class来添加样式）

- normal
- small
- large

###### 不同的Button状态（使用disable属性）

- disabled
- link-disabled（**a元素没有disabled属性，需要区别对待**）

###### 额外注意

button本身的HTML属性，是否需要支持，如autofocus，href等等

##### 编码实现

###### 定义props的形状

```js
// 创建Button组件props的形状
interface BaseButtonProps {
  className?: string;
  disabled?: boolean;
  size?: ButtonSize;
  btnType?: ButtonType;
  href?: string;
  children: React.ReactNode;
}
```

Button类型type和size都是几种常量值，使用枚举类型来定义这两个属性，枚举类型的成员可以作为值使用

```javascript
// 使用枚举类型来定义Button的size和type有哪些属性值，因为这些属性值都是一些固定的常量
export enum ButtonSize {
  Large = 'lg',
  Small = 'sm'
}

export enum ButtonType {
  Primary = 'primary',
  Default = 'default',
  Danger = 'danger',
  Link = 'link'
}
```

###### 定义组件

```jsx
const Button: React.FC<BaseButtonProps> = (props) => {
  const { btnType, disabled, size, href, children } = props;
  // 使用classNames来添加className
  // 当前元素的className除了包含btn外，还会通过type来指定类型，如btn-primary, 以及btn-size，如果是link类型，需要添加
  // disabled className
  const classes = classNames('btn', {
    // 如果btnType存在，则添加了btn-${btnType}
    [`btn-${btnType}`]: btnType,
    [`btn-${size}`]: size,
    disabled: btnType === ButtonType.Link ? true: false
  });
  if (btnType === ButtonType.Link && href) {
    // 如果是link类型且传入了href属性，则返回a元素
    return (
      <a
        className={classes}
        href={href}
      >
        {children}
      </a>
    )
  } else {
    return (
      <button
        className={classes}
        disabled={disabled}
      >
        {children}
      </button>
    )
  }
}

Button.defaultProps = {
  disabled: false,
  btnType: ButtonType.Default
}

export default Button
```

###### 定义样式

添加Button样式相关的变量到`_variables.scss`中

```scss
// 按钮
// 按钮基本属性
$btn-font-weight:             400;
$btn-padding-y:               .375rem !default;
$btn-padding-x:               .75rem !default;
$btn-font-family:             $font-family-base !default;
$btn-font-size:               $font-size-base !default;
$btn-line-height:             $line-height-base !default;

//不同大小按钮的 padding 和 font size
$btn-padding-y-sm:            .25rem !default;
$btn-padding-x-sm:            .5rem !default;
$btn-font-size-sm:            $font-size-sm !default;

$btn-padding-y-lg:            .5rem !default;
$btn-padding-x-lg:            1rem !default;
$btn-font-size-lg:            $font-size-lg !default;

// 按钮边框
$btn-border-width:            $border-width !default;

// 按钮其他
$btn-box-shadow:              inset 0 1px 0 rgba($white, .15), 0 1px 1px rgba($black, .075) !default;
$btn-disabled-opacity:        .65 !default;

// 链接按钮
$btn-link-color:              $link-color !default;
$btn-link-hover-color:        $link-hover-color !default;
$btn-link-disabled-color:     $gray-600 !default;


// 按钮 radius
$btn-border-radius:           $border-radius !default;
$btn-border-radius-lg:        $border-radius-lg !default;
$btn-border-radius-sm:        $border-radius-sm !default;

$btn-transition:              color .15s ease-in-out, background-color .15s ease-in-out, border-color .15s ease-in-out, box-shadow .15s ease-in-out !default;
```

定义button组件的样式

```scss
.btn {
  position: relative;
  display: inline-block;
  font-weight: $btn-font-weight;
  line-height: $btn-line-height;
  color: $body-color;
  white-space: nowrap;
  text-align: center;
  vertical-align: middle;
  background-image: none;
  border: $btn-border-width solid transparent;
  @include button-size($btn-padding-y, $btn-padding-x, $btn-font-size, $btn-border-radius);
  box-shadow: $btn-box-shadow;
  cursor: pointer;
  transition: $btn-transition;
  &.disabled, &[disabled] {
    cursor: not-allowed;
    opacity: $btn-disabled-opacity;
    box-shadow: none;
    > * {
      // 让所有子元素不接收鼠标事件
      pointer-events: none;
    }
  }
}

.btn-lg {
  @include button-size($btn-padding-y-lg, $btn-padding-x-lg, $btn-font-size-lg, $btn-border-radius-lg);
}

.btn-sm {
  @include button-size($btn-padding-y-sm, $btn-padding-x-sm, $btn-font-size-sm, $btn-border-radius-sm);
}

.btn-primary {
  @include button-style($primary, $primary, $white)
}

.btn-danger {
  @include button-style($danger, $danger, $white)
}

.btn-default {
  @include button-style($white, $gray-400, $body-color, $white, $primary, $primary)
}

.btn-link {
  font-weight: $font-weight-normal;
  color: $btn-link-color;
  text-decoration: $link-decoration;
  box-shadow: none;
  &:hover {
    color: $btn-link-hover-color;
    text-decoration: $link-hover-decoration;
  }
  &:focus, &.focus {
    text-decoration: $link-hover-decoration;
    box-shadow: none;
  }
  &[disabled], &.disabled {
    color: $btn-link-disabled-color;
    pointer-events: none;
  }
}
```

相关的mixin定义如下

```scss
@mixin button-size($padding-y, $padding-x, $font-size, $border-radius) {
  padding: $padding-y $padding-x;
  font-size: $font-size;
  border-radius: $border-radius;
}

@mixin button-style(
  $background,
  $border,
  $color,
  $hover-background: lighten($background, 7.5%),
  $hover-border: lighten($border, 10%),
  $hover-color: $color
) {
  color: $color;
  background: $background;
  border-color: $border;
  &:hover {
    color: $hover-color;
    background: $hover-background;
    border-color: $hover-border;
  }
  &:focus, &.focus {
    color: $hover-color;
    background: $hover-background;
    border-color: $hover-border;
  }
  &.disabled, &[disabled] {
    color: $color;
    background: $background;
    border-color: $border;
  }
}
```

###### 更新Props的形状

目前定义的props形状显然无法添加更多的button元素原生的属性，而我们希望可以将HTML中的button元素的原生属性也同样直接添加到Button组件上。

比如我们想将autoFocus属性添加到Button组件上并出现相应的行为，但我们的Props形状压根儿没有定义autoFocus，这种情况下我们不可能将所有的原生属性都定义在Props。如果用JavaScript来写，当然不必定义props的类型，但typescript需要将props的类型定义好，因此面对这种情况有如下解决方案：

```jsx
<Button autoFocus>aotoFocus</Button>
```

**扩展props来解决定义原生属性**

- `React.ButtonHTMLAttributes<HTMLElement>`和`React.AnchorHTMLAttributes<HTMLElement>`已经定义好了原生属性的类型，将我们之前自定义的BaseButtonProps与上述类型做一个交叉类型即可`ButtonProps = NativeButtonProps & AnchorButtonProps`。
- 由于button元素上的属性和a元素上的属性有出入，button元素上的必须属性可能在a元素上并没有，所以这种情况下，我们就必须将所有的属性都转变为可选的，使用typescript提供的Utility Types来完成`ButtonProps = <NativeButtonProps & AnchorButtonProps>`。
- Utility types更多像是类型转换的工具函数一样，将类型转换为其他类型。

```jsx
// 定义NativeButtonProps 类型别名，将React本身提供的ButtonHTMLAttribute类型与BaseButtonProps合并为交叉类型
type NativeButtonProps = BaseButtonProps & React.ButtonHTMLAttributes<HTMLElement>;

type AnchorButtonProps = BaseButtonProps & React.AnchorHTMLAttributes<HTMLElement>;

// Utility Types: Partial<T>将T上所有的属性变为可选的，因为NativeButtonProps的必须属性不属于AnchorButtonProps的属性，所以直接将所有属性变为可选的；
export type ButtonProps = Partial<NativeButtonProps & AnchorButtonProps>;
```

- 将多余传入的属性绑定到组件内部元素上

  ```jsx
  // 使用restProps来收集多余传入的属性
  const { btnType, className, disabled, size, href, children, ...restProps } = props;
  ......
  return (
    <a
      className={classes}
      href={href}
      // 解构restProps
      {...restProps}
      >
      {children}
    </a>
  )
  // 或者
  return (
    <button
      className={classes}
      disabled={disabled}
      {...restProps}
      >
      {children}
    </button>
  )
  ```

#### 测试

自动化测试的必要性

- 高质量代码
- 更早发现bug
- 重构和升级更可靠
- 开发流程更敏捷

测试金字塔

Unit（底部） -> Service（中部） -> UI（顶部）

React组件适合单元测试

- component化
- function组件，纯函数容易测试
- 单项数据流

##### 通用测试框架Jest

Create-react-app内置了jest框架。Jest框架用于管理测试用例，运行测试用例，提供一系列断言语法等功能。

使用jest提供的断言和**mock函数**

##### React组件测试工具

[参考文档](https://www.html.cn/create-react-app/docs/running-tests/)

**React Testing Library**是一个用于类似于用户使用组件的方式来测试React组件的测试库，适用于React组件混合应用程序的单元、集成和端到端的测试。

Enzyme的替代品，如下特点

- 与DOM节点一起工作，因此建议使用jest-dom来改进断言。jest-dom额外添加了很多针对DOM节点的断言。
- 基于DOM Testing Library构建，通过添加更多的API来测试React组件
- 轻量级测试解决方案，提供基于react-dom和react-dom/test-utils的工具函数来保证更好的测试体验
  - 不再处理react组件实例，而是处理真实DOM节点。库本身提供的工具通过模拟用户使用的方式来管理查询DOM，如通过元素的label text（标签文本）来寻找元素，通过文本来寻找链接与button，并且还暴露了一个data-testid（作为hatch的方式）的方式来寻找元素（如果文本内容和标签不管用的情况下）
- create-react-app 3.3版本以上已经安装了`@testing-library/react`，并且随之还有`@testing-library/jest-dom`和`@testing-library/user-event`

###### @tesing-library/react的重要API

[参考文档](https://testing-library.com/docs/react-testing-library/api#render)

- render：

  - 使用方式

    ```jsx
    import { render } from '@testing-library/react'
    
    render(<div />)
    ```

  - 作用：将组件渲染到页面中，默认使用react-dom的渲染方式，即将组件挂载到一个div下（该div元素已经append到body中了）

  - 函数类型定义：

    ```tsx
    function render(
      ui: React.ReactElement<any>,
      options?: {
        /* You won't often use this, expand below for docs on options */
      }
    ): RenderResult
    ```

- RenderResult

  render函数的返回值，包含如下的属性

  - ...queries系列属性
    - queries系列方法用来进行DOM查询，[参考](https://testing-library.com/docs/queries/about)

- fireEvent

  - 作用：用于模拟用户操作触发事件

  - 使用：

        ```javascript
        fireEvent(node: HTMLElement, event: Event)
        // <button>Submit</button>
        fireEvent(
          getByText(container, 'Submit'),
          new MouseEvent('click', {
            bubbles: true,
            cancelable: true,
          })
        )
        // 更方便的写法
        fireEvent[eventName](node: HTMLElement, eventProperties: Object)
        ```

- cleanup

  - 作用：将render函数调用产生的挂载到DOM上的React树卸载，这样保证了每次的测试用例都是新的React树。（测试框架如果支持afterEach全局钩子函数，则cleanup在每次测试用例结束后是自动调用的`afterEach(cleanup)`，如jest，Mocha等）

##### 测试用例代码

```typescript
import React from 'react'
import { render, fireEvent } from '@testing-library/react'
import Button, { ButtonProps, ButtonSize, ButtonType } from './Button'

const defaultProps = {
  // 添加事件处理函数的mock函数，测试时只需要判断该函数是否被调用，使用jest.fn()创建一个mock函数
  onClick: jest.fn()
}

const testProps: ButtonProps = {
  btnType: ButtonType.Primary,
  size: ButtonSize.Large,
  className: 'klass'
}

const disabledProps: ButtonProps = {
  disabled: true,
  onClick: jest.fn()
}

describe('test button component', () => {
  it('should rendered the correct default button', () => {
    const wrapper = render(<Button {...defaultProps}>Nice</Button>)
    const element = wrapper.getByText('Nice')
    expect(element).toBeInTheDocument()
    expect(element.tagName).toEqual('BUTTON')
    expect(element).toHaveClass('btn btn-default')
    fireEvent(element, new MouseEvent('click', { bubbles: true, cancelable: true}))
    // fireEvent.click(element)
    expect(defaultProps.onClick).toHaveBeenCalled()
  })

  it('should rendered the correct component based on different props', () => {
    const wrapper = render(<Button {...testProps}>Nice</Button>)
    const element = wrapper.getByText('Nice')
    expect(element).toBeInTheDocument()
    expect(element.tagName).toEqual('BUTTON')
    expect(element).toHaveClass('btn btn-primary btn-lg klass')
  })

  it('should render a link when btnType equals link and href is provided', () => {
    const wrapper = render(<Button btnType={ButtonType.Link} href={`https://baidu.com`}>a link</Button>)
    const element = wrapper.getByText('a link')
    expect(element).toBeInTheDocument()
    expect(element.tagName).toEqual('A')
    expect(element).toHaveClass('btn btn-link')
  })

  it('should render disabled button when disabled set to true', () => {
    const wrapper = render(<Button {...disabledProps}>Nice</Button>)
    const element = wrapper.getByText('Nice') as HTMLButtonElement
    expect(element).toBeInTheDocument()
    expect(element.tagName).toEqual('BUTTON')
    expect(element.disabled).toBeTruthy()
    expect(element).toHaveClass('btn btn-default')
    fireEvent(element, new MouseEvent('click', { bubbles: true, cancelable: true}))
    expect(defaultProps.onClick).not.toHaveBeenCalled()
  })
})
```

#### Menu组件

##### 需求分析

- 横线或者纵向排版：使用mode属性来选择
- 当前被激活的菜单子项：使用activeIndex来指示
- 默认被激活的菜单子项：使用defaultIndex来指示
- 被disabled的菜单子项：菜单子项的disabled属性来指示
- 选中子菜单时的回调函数：使用`onSelect: (selectedIndex: number) => void`

###### 方案选取

- 只有一个`<Menu>`组件，将需要渲染的菜单子项通过items属性传递给`<Menu items={items} /> // items是一个包含子项的数组`组件

- Menu组件分为父组件`<Menu>`和子组件`<MenuItem>`，与HTML标记ul和li是相对应，子组件作为父组件的children

  ```tsx
  <Menu mode={'horizontal'} activeIndex={2} defaultIndex={0} onSelect={() => {}}>
      <Menu.Item disabled index={0}>子菜单1</Menu.Item>
    <Menu.Item index={1}>子菜单2</Menu.Item>
    <Menu.Item index={2} >子菜单3</Menu.Item>
  </Menu>
  ```

按照标记语言的行为习惯，显然第二种方案更加符合HTML规范。

##### 编码实现

###### Menu

```tsx
import React from 'react'
import classNames from 'classnames'

// 字符串字面量联合类型，等同于使用枚举
type MenuMode = 'horizontal' | 'vertical'
type SelectCallback = (selectedIndex: number) => void

export interface MenuProps {
  defaultIndex?: number;
  className?: string;
  mode?: MenuMode;
  style?: React.CSSProperties;
  onSelect?: SelectCallback;
}

export const MenuContext = createContext<IMenuContext>({ index: 0 })

const Menu: React.FC<MenuProps> = (props) => {
  const { className, mode, style, defaultIndex, children } = props;
  const classes = classNames('menu', className, {
    'menu-vertical': mode === 'vertical'
  })
  return (
    <ul
      className={classes}
      style={style}
    >
      {children}
    </ul>
  )
}

Menu.defaultProps = {
  defaultIndex: 0,
  mode: 'horizontal'
}

export default Menu
```

###### MenuItem

```tsx
import React from 'react'
import classNames from 'classnames'

export interface MenuItemProps {
  index?: number;
  disabled?: boolean;
  className?: string;
  style?: React.CSSProperties;
}

const MenuItem: React.FC<MenuItemProps> = (props) => {
  const { index, disabled, className, style, children } = props;
  const classes = classNames('menu-item', className, {
    'is-disabled': disabled
  })
  return (
    <li
     className={classes}
     style={style}
    >
      { children }
    </li>
  )
}

export default MenuItem
```

###### 组件通讯

父组件需要给子组件传递当前激活的索引，子组件在点击事件后需要将索引传递给父组件，由父组件更新当前激活的索引

由于父组件与子组件是相互隔离的（父组件并不知道子组件是什么，子组件也不知道父组件是什么），因此父子组件的通讯无法使用直接传递的方式，因此我们选择使用Context的解决方案来做通讯。

```tsx
// Menu组件
import React, { createContext, useState } from 'react'
import classNames from 'classnames'

// 字符串字面量联合类型，等同于使用枚举
type MenuMode = 'horizontal' | 'vertical'
type SelectedCallback = (selectedIndex: number) => void

// 定义父组件向子组件传递的数据接口
interface IMenuContext {
  activeIndex: number;
  // 用于在选取菜单子项后用户自定义的逻辑
  onSeleted?: SelectedCallback;
}

export interface MenuProps {
  defaultIndex?: number;
  className?: string;
  mode?: MenuMode;
  style?: React.CSSProperties;
  onSelected?: SelectedCallback;
}

export const MenuContext = createContext<IMenuContext>({ activeIndex: 0 })

const Menu: React.FC<MenuProps> = (props) => {
  const { className, mode, style, defaultIndex, children, onSelected } = props;
  const [ currentActive, setActive ] = useState(defaultIndex)
  const classes = classNames('menu', className, {
    'menu-vertical': mode === 'vertical'
  })
  const handleClick = (selectedIndex: number) => {
    setActive(selectedIndex)
    onSelected && onSelected(selectedIndex)
  }
  const passedContext: IMenuContext = {
    activeIndex: currentActive? currentActive: 0,
    onSeleted: handleClick
  }
  return (
    <ul
      className={classes}
      style={style}
    >
      <MenuContext.Provider value={passedContext}>
        { children }
      </MenuContext.Provider>
    </ul>
  )
}

Menu.defaultProps = {
  defaultIndex: 0,
  mode: 'horizontal'
}

export default Menu

// MenuItem组件
import React, { useContext } from 'react'
import classNames from 'classnames'
import { MenuContext } from './Menu'

export interface MenuItemProps {
  index: number;
  disabled?: boolean;
  className?: string;
  style?: React.CSSProperties;
}

const MenuItem: React.FC<MenuItemProps> = (props) => {
  const { index, disabled, className, style, children } = props;
  const context = useContext(MenuContext);
  const classes = classNames('menu-item', className, {
    'is-disabled': disabled,
    'is-active': index === context.activeIndex
  })
  const handleClick = () => {
    if (context.onSeleted && !disabled) {
      context.onSeleted(index);
    }
  }
  return (
    <li
     className={classes}
     style={style}
     onClick={handleClick} 
    >
      { children }
    </li>
  )
}

// 为下文的验证children是否为MenuItem添加的display属性
MenuItem.displayName = 'MenuItem'

export default MenuItem
```

##### 样式添加

```scss
.menu {
  display: flex;
  flex-wrap: wrap;
  padding-left: 0;
  // margin-bottom: 30px;
  list-style: none;
  // border-bottom: $menu-border-width solid $menu-border-color;
  // box-shadow: $menu-box-shadow;
  .menu-item {
    padding: $menu-item-padding-y $menu-item-padding-x;
    cursor: pointer;
    transition: $menu-transition;
    &:hover, &:focus {
      text-decoration: none;
    }
    &.is-disabled {
      color: $menu-item-disabled-color;
      pointer-events: none;
      cursor: default;
    }
    &.is-active, &:hover {
      color: $menu-item-active-color;
      border-bottom: $menu-item-active-border-width solid $menu-item-active-color;
    }
  }
}

.menu-vertical {
  flex-direction: column;
  border-bottom: 0px;
  border-right: $menu-border-width solid $menu-border-color;
  .menu-item {
    border-left: $menu-item-active-border-width solid transparent;
    &.is-active, &:hover {
      border-bottom: 0px;
      border-left: $menu-item-active-border-width solid $menu-item-active-color;
    }
  }
}
```

##### 测试用例驱动

```tsx
import React from 'react'
import { fireEvent, render, RenderResult, cleanup } from '@testing-library/react'
import Menu, { MenuProps } from './Menu'
import MenuItem, { MenuItemProps } from './MenuItem'

const testProps: MenuProps = {
  defaultIndex: 0,
  onSelected: jest.fn(),
  className: 'test'
}

const testVerticalProps: MenuProps = {
  defaultIndex: 0,
  mode: 'vertical'
}

// 生成测试组件的工厂函数
const generateMenu = (props: MenuProps) => {
  return (
    <Menu {...props}>
      <MenuItem index={0}>active</MenuItem>
      <MenuItem disabled index={1}>disabled</MenuItem>
      <MenuItem index={2}>item3</MenuItem>
    </Menu>
  )
}

let wrapper: RenderResult, menuElement: HTMLElement, activeElement: HTMLElement, disabledElement: HTMLElement

describe('test Menu and MenuItem components', () => {

  // beforeEach全局钩子函数，在每个测试用例之前运行传入的回调函数
  beforeEach(() => {
    wrapper = render(generateMenu(testProps))
    menuElement = wrapper.getByTestId('test-menu')
    activeElement = wrapper.getByText('active')
    disabledElement = wrapper.getByText('disabled')
  })

  it('should render correct Menu and MenuItem based on default props', () => {
    expect(menuElement).toBeInTheDocument()
    expect(menuElement).toHaveClass('menu test')
    expect(menuElement.getElementsByTagName('li').length).toEqual(3)
    expect(activeElement).toHaveClass('menu-item is-active')
    expect(disabledElement).toHaveClass('menu-item is-disabled')
  })

  it('click item should change active and call the right callback', () => {
    const thirdItem = wrapper.getByText('item3')
    fireEvent.click(thirdItem)
    expect(testProps.onSelected).toHaveBeenCalledWith(2)
    expect(thirdItem).toHaveClass('is-active')
    expect(activeElement).not.toHaveClass('is-active')
    fireEvent.click(disabledElement)
    expect(disabledElement).not.toHaveClass('is-active')
    expect(testProps.onSelected).not.toHaveBeenCalledWith(1)
  })

  it('should render vertical mode when mode is set to vertical', () => {
    cleanup()
    const wrapper = render(generateMenu(testVerticalProps))
    const menuElement = wrapper.getByTestId('test-menu')
    expect(menuElement).toHaveClass('menu-vertical')
  })
})
```

##### 验证children并减少样板代码

在每次给Menu组件传入子组件MenuItem时，都需要指定index属性，这是很麻烦的事情。我们希望index属性通过编程的方式指定，并且Menu组件的子节点只接受MenuItem组件，如果传入其他组件则会忽略并提示警告信息。

###### 提供一个验证函数

由于children是一个不透明的数据结构（无法确定children到底是什么，即可能是字符串，数组或者其他），所以不能对children直接使用数组的方法，这里需要用React.Children API来解决这个问题。

- React.Children.map：`React.Children.map(children, function[(thisArg)])`

  - 在 `children` 里的每个直接子节点上调用一个函数，并将 `this` 设置为 `thisArg`。如果 `children` 是一个数组，它将被遍历并为数组中的每个子节点调用该函数。如果子节点为 `null` 或是 `undefined`，则此方法将返回 `null` 或是 `undefined`，而不会返回数组

- React.cloneElement

  ```tsx
  React.cloneElement(
    element,
    [props],
    [...children]
  )
  ```

  - 以 `element` 元素为样板克隆并返回新的 React 元素。返回元素的 props 是将新的 props 与原始元素的 props 浅层合并后的结果。新的子元素将取代现有的子元素，而来自原始元素的 `key` 和 `ref` 将被保留。

```tsx
const renderChildren = () => {
  return React.Children.map(children, (child, index) => {
    // 由于child的类型是各种类型的联合类型，所以这里使用了断言来确定类型，这里使用类型断言主要是为了不出现类型错误，实际上，任何的React Element类型都由一个type属性，所以可以从childElement.type中获取displayName，除了组件（被定义了displayName）外，其他React Element的type属性的displayName属性为undefined.
    //   这里使用React.FunctionComponentElement类型的原因：
    //   interface FunctionComponentElement<P> extends ReactElement<P, FunctionComponent<P>>
    //   interface ReactElement<P = any, T extends 很长> {
    //     type: T;
    //     props: P;
    //     key: Key | null;
    //   }
    //   显然，FunctionComponentElement是函数组件调用返回的React Element类型，其中的type属性值就是该FunctionComponent
    const childElement = child as React.FunctionComponentElement<MenuItemProps>
    const { displayName } = childElement.type
    if (displayName === 'MenuItem') {
      return React.cloneElement(childElement, { index })
    } else {
      console.error('Warning: Menu has a child which is not a MenuItem Component')
    }
  })
}

return (
  <ul
    className={classes}
    style={style}
    data-testid={'test-menu'}
    >
    <MenuContext.Provider value={passedContext}>
      // 取代之前的{ children }
      { renderChildren() }
    </MenuContext.Provider>
  </ul>
)
```

#### Icon组件

使用fortawesome/react-fortawesome作为基础方案，进行二次封装

##### 需求分析

将主题颜色封装到Icon组件上

##### 编码

###### 组件逻辑

```tsx
import React from 'react'
import classNames from 'classnames'
import { FontAwesomeIcon, FontAwesomeIconProps } from '@fortawesome/react-fontawesome'

export type themeProps = 'primary' | 'secondary' | 'success' | 'info' | 'warning' | 'danger' | 'light' | 'dark'

export interface IconProps extends FontAwesomeIconProps {
  theme?: themeProps
}

const Icon: React.FC<IconProps> = (props) => {
  const { className, theme, ...restProps } = props
  const classes = classNames('icon', className, {
    [`icon-${theme}`]: theme
  })
  return (
    <FontAwesomeIcon className={classes} {...restProps} />
  )
}

export default Icon
```

###### 样式

```scss
// 在_variables.scss中创建$theme-colors变量来方便将类名与color值关联起来;
// 创建主体颜色Map映射结构
$theme-colors:
(
  'primary': $primary,
  'secondary': $secondary,
  'success': $success,
  'info': $info,
  'warning': $warning,
  'danger': $danger,
  'light': $light,
  'dark': $dark
);
// components/Icon/_style.scss中使用@each命令来遍历$theme-color
@each $key, $value in $theme-colors {
  .icon-#{$key} {
    color: $value;
  }
}
```

##### 使用注意

如果像这样`<Icon theme={'primary'} icon={'angle-down'} className={'arrow-icon'} />`直接将icon定义为'angle-down'（省略fas前缀），则需要在项目中全局引入以下代码

```tsx
import { library } from '@fortawesome/fontawesome-svg-core'
// import { faCheckSquare, faCoffee } from '@fortawesome/free-solid-svg-icons'
import { fas } from '@fortawesome/free-solid-svg-icons'

// library.add(faCoffee, faCheckSquare)
library.add(fas)
```

在写storyies时，也需要在stories.tsx文件中这样引入才能让图标显示出来。

#### StoryBook

##### 特点

- 分别展示组件在不同属性下的状态
- 追踪组件的行为并具有属性调试功能
- 为组件自动生成文档和属性列表

##### 集成

`npx -p @storybook/cli sb init  `命令，相当于下载@storybook/cli并使用npx sb init来将storybook集成到当前项目中

##### 配置

新版storybook不需要添加typescript的额外配置

集成后，在项目根目录下会出现.storybook目录，其中main.js中会导出一个配置对象

```js
module.exports = {
  // stories字段表示stories的来源路径，这里被注释掉的是默认的，然后添加我们自己的组件stories路径
  "stories": [
    // "../src/**/*.stories.mdx",
    // "../src/**/*.stories.@(js|jsx|ts|tsx)",
    "../src/components/**/*.stories.tsx"
  ],
  "addons": [
    "@storybook/addon-links",
    "@storybook/addon-essentials",
    "@storybook/preset-create-react-app"
  ]
}
```

###### 添加样式文件

preview.js文件如下：

```js
export const parameters = {
  actions: { argTypesRegex: "^on[A-Z].*" },
  controls: {
    matchers: {
      color: /(background|color)$/i,
      date: /Date$/,
    },
  },
}

// 添加样式文件，将组件所需样式文件导入，这里可以直接导入scss样式文件不需要额外配置了
import '../src/styles/index.scss'
```

##### 编写story

story文件会被storybook使用，通常而言，会将story文件命名为*.**stories**.tsx。

###### CSF模式

Component Story Format模式，使用基于ESModule的方式

- 定义元信息对象：默认导出一个对象，提供一些组件元信息，帮助storybook展示组件。

  ```js
  // Button.stories.tsx
  
  import React from 'react';
  
  import { Meta } from '@storybook/react';
  
  import { Button, ButtonProps } from './Button';
  
  export default {
    title: 'Components/Button',
    component: Button,
  } as Meta;
  ```

- 定义stories：定义函数并导出，指向该函数的变量名称作为story的名称，函数的返回值会被storybook渲染。可以定义多个函数并导出，作为多个stories。

  ```tsx
  // Button.stories.ts | Button.stories.tsx
  
  import React from 'react'
  
  import { Meta } from '@storybook/react';
  
  import { Button } from './Button';
  
  export default {
    component: Button,
    title: 'Components/Button',
  } as Meta;
  
  export const Primary: React.VFC<{}> = () => <Button primary>Button</Button>;
  ```

###### StoriesOf API模式

这是传统的定义Stories的方式，使用如下

```tsx
import React from 'react';
import { storiesOf } from '@storybook/react';
import { action } from '@storybook/addon-actions'

import Button from './Button';

// 使用传统的Stories API来写stories

// 定义story case
const DefaultButton = () => {
  return <Button
    onClick={action('dianji')}
  >
    默认Button
  </Button>
}

const ButtonWithSize = () => {
  return (
    <>
      <Button size="lg">Large</Button>
      <Button size="sm">Small</Button>
    </>
  )
}

const ButtonWithType = () => {
  return (
    <>
      <Button btnType="primary">primary</Button>
      <Button btnType="danger">danger</Button>
      <Button btnType="link">link</Button>
    </>
  )
}
// storiesOf定义store的名称，使用add()添加每一个stories，使用这种方式可以定义中文的story case 名称，比CSF方式更加方便一些；
storiesOf('Button Component', module)
  .add('默认的Button',DefaultButton)
  .add('不同尺寸的Button', ButtonWithSize)
  .add('不同类型的Button', ButtonWithType)
```

##### 插件系统addons

###### Decorators类型

可以在全局添加decorators（全局配置文件中，所有组件都会被decorate），也可以在某个组件的stories上添加

###### Native类型

###### 文档插件

在storybook提供的页面中展示组件的各种信息，包括组件的描述、组件的使用样例代码、组件的props的含义、并可以将JSDoc的注释自动生成文档显示。

@storybook/addon-docs插件

- Preset addon，开箱即用，无需配置，当然可以手动添加配置。
- 需要使用CSF模式来写story，**storiesOf API无效**
- 使用JSDoc规范来给组件注释，插件可以直接生成文档

```tsx
// 创建Button组件props的形状
interface BaseButtonProps {
  className?: string;
  /**
   * 设置Button组件是否禁用
   */
  disabled?: boolean;
  /**
   * 设置Button组件的尺寸
   */
  size?: ButtonSize;
  /**
   * 设置Button组件的类型
   */
  btnType?: ButtonType;
  /**
   * 当类型为link时，设置href属性
   */
  href?: string;
  children: React.ReactNode;
}
/**
 * ## Button组件的说明
 * ### 引入说明
 * ~~~js
 * // ESModule方式
 * import { Button } from 'mxyrc'
 * // CommonJS
 * const { Button } = require('mxyrc')
 * ~~~
 * ### 简介
 * 按钮元素，适用于完成点击等特定的交互行为
 */
const Button: React.FC<ButtonProps> = (props) => {...}
```

#### Transition动画组件

##### 需求分析

基于react-transition-group 的CSS-Transition 组件二次封装，除了CSS-Transition自身的属性外，额外提供一个animation属性，值为字符串，我们预设了一些动画效果，提供给animation属性即可。如果用户给组件提供了classNames属性，则animation属性失效，以用户提供的classNames为准。

##### 编码

```tsx
import React from 'react'
import { CSSTransition } from 'react-transition-group'
import { CSSTransitionProps } from 'react-transition-group/CSSTransition'

type AnimationName = 'zoom-in-top' | 'zoom-in-left' | 'zoom-in-right' | 'zoom-in-bottom'

type TransitionProps = CSSTransitionProps & {
  animation?: AnimationName;
}

const Transition: React.FC<TransitionProps> = (props) => {
 const {
   children,
   classNames,
   animation,
   ...restProps
 } = props
 return (
   <CSSTransition 
    classNames={classNames? classNames: animation}
    {...restProps}
   >
     { children }
   </CSSTransition>
 )
}

Transition.defaultProps = {
  unmountOnExit: true,
  appear: true
}

export default Transition
```

##### 样式

使用mixin功能生成4种不同的动画效果

```scss
// _mixin.scss文件内
// 预设的动画效果，direction表示动画方向、scaleStart表示开始时的scale，scaleEnd表示结束时的scale，origin表示transform的原点
@mixin zoom-animation(
  $direction: 'top',
  $scaleStart: scaleY(0),
  $scaleEnd: scaleY(1),
  $origin: center top
) {
  .zoom-in-#{$direction}-enter {
    opacity: 0;
    transform: $scaleStart;
  }
  .zoom-in-#{$direction}-enter-active {
    opacity: 1;
    transform: $scaleEnd;
    transition: transform 300ms cubic-bezier(0.23, 1, 0.320, 1) 100ms, opacity 300ms cubic-bezier(0.23, 1, 0.320, 1) 100ms;
    transform-origin: $origin;
  }
  .zoom-in-#{$direction}-exit {
    opacity: 1;
    transform: $scaleEnd;
  }
  .zoom-in-#{$direction}-exit-active {
    opacity: 0;
    transform: $scaleStart;
    transition: transform 300ms cubic-bezier(0.23, 1, 0.320, 1) 100ms, opacity 300ms cubic-bezier(0.23, 1, 0.320, 1) 100ms;
    transform-origin: $origin;
  }
}

// _animation.scss文件内
@include zoom-animation('top', scaleY(0), scaleY(1), center top);
@include zoom-animation('left', scale(0.45, 0.45), scale(1, 1), top left);
@include zoom-animation('right', scale(0.45, 0.45), scale(1, 1), top right);
@include zoom-animation('bottom', scaleY(0), scaleY(1), center bottom);
```

#### Input组件

##### 需求分析

Input组件的各种状态

- 禁用状态
- 可指定规定尺寸的状态
- 带有图标（在Input末尾）
- 带有前缀和后缀

##### 编码

```tsx
import React, { ReactElement, InputHTMLAttributes, ChangeEvent } from 'react'
import classNames from 'classnames'
import { IconProp } from '@fortawesome/fontawesome-svg-core'
import Icon from '../Icon/Icon'

type InputSize = 'lg' | 'sm'

// 使用Omit来忽略InputHTMLAttributes<HTMLElement>中的size属性，让我们定义的size属性得以兼容
// 否则，我们定义的size会与InputHTMLAttributes<HTMLElement>的size属性不兼容
export interface InputProps extends Omit<InputHTMLAttributes<HTMLElement>, 'size'> {
  /**
   * 用于Input组件的禁用
   */
  disabled?: boolean;
  /**
   * 用于设定组件的大小
   */
  size?: InputSize;
  /**
   * 用于配置带图标的组件,传入icon图标的名称
   */
  icon?: IconProp;
  /**
   * 用于添加组件的前缀
   */
  prepend?: string | ReactElement;
  /**
   * 用于添加组件的后缀
   */
  append?: string | ReactElement;
  /**
   * 自定义onChange事件的回调函数
   */
  onChange?: (e: ChangeEvent<HTMLInputElement>) => void
}

/**
 * ## Input组件说明
 * 
 * ### 引入形式
 * ~~~js
 * // ESModule
 * import { Input } from 'mxyrc'
 * ~~~
 * ### 简介
 * Input输入框组件，通过鼠标或键盘输入内容，是最基础的表单域的封装, 支持所有的HTMLInput基本属性
 * @param props 
 */
const Input: React.FC<InputProps> = (props) => {
  const {
    disabled,
    size,
    icon,
    prepend,
    append,
    ...restProps
  } = props;
  // 处理受控组件的情况
  const fixControlledValue = (value: any) => {
    if (typeof value === 'undefined' || value === null) {
      return ''
    } 
  }
  // 处理受控组件的情况，禁止同时传入value和defaultValue两个属性
  if ('value' in props) {
    delete restProps.defaultValue
    fixControlledValue(props.value)
  }
  const classes = classNames('mxyrc-input-wrapper', {
    [`input-size-${size}`]: size,
    'is-disabled': disabled,
    'input-group': prepend || append,
    'input-group-append': !!append,
    'input-group-prepend': !!prepend
  })
  return (
    <div className={classes}>
      { prepend && <div className={'input-group-prepend'}>{ prepend }</div> }
      { icon && <div className={'icon-wrapper'}><Icon icon={icon} title={`title-${icon}`} /></div> }
      <input
        className={'mxyrc-input-inner'}
        disabled={disabled}
        {...restProps}
      />
      { icon ? null: append ? <div className={'input-group-append'}>{ append }</div>: null }
    </div>
  )
}

export default Input;
```

##### 样式

```scss
.mxyrc-input-wrapper {
  display: flex;
  width: 100%;
  margin-bottom: 30px;
  position: relative;
  .icon-wrapper {
    position: absolute;
    height: 100%;
    width: 35px;
    justify-content: center;
    color: $input-color;
    right: 0;
    top: 0;
    display: flex;
    align-items: center;
    svg {
      color: $input-placeholder-color;
    }
  }
}
.icon-wrapper+.mxyrc-input-inner {
  padding-right: 35px;
}
.mxyrc-input-inner {
  width: 100%;
  padding: $input-padding-y $input-padding-x;
  font-family: $input-font-family;
  font-size: $input-font-size;
  font-weight: $input-font-weight;
  line-height: $input-line-height;
  color: $input-color;
  background-color: $input-bg;
  background-clip: padding-box;
  border: $input-border-width solid $input-border-color;

  // Note: This has no effect on <select>s in some browsers, due to the limited stylability of `<select>`s in CSS.
  border-radius: $input-border-radius;

  box-shadow: $input-box-shadow;
  transition: $input-transition;

  &:focus {
    color: $input-focus-color;
    background-color: $input-focus-bg;
    border-color: $input-focus-border-color;
    outline: 0;
    box-shadow: $input-focus-box-shadow;
    // Avoid using mixin so we can pass custom focus shadow properly
  }
  &::placeholder {
    color: $input-placeholder-color;
    // Override Firefox's unusual default opacity; see https://github.com/twbs/bootstrap/pull/11526.
    opacity: 1;
  }
  &:disabled,
  &[readonly] {
    background-color: $input-disabled-bg;
    border-color: $input-disabled-border-color;
    // iOS fix for unreadable disabled content; see https://github.com/twbs/bootstrap/issues/11655.
    opacity: 1;
  }
}
.input-group-prepend,
.input-group-append {
  display: flex;
  align-items: center;
  padding: $input-padding-y $input-padding-x;
  margin-bottom: 0; // Allow use of <label> elements by overriding our default margin-bottom
  font-size: $input-font-size; // Match inputs
  font-weight: $font-weight-normal;
  line-height: $input-line-height;
  color: $input-group-addon-color;
  text-align: center;
  white-space: nowrap;
  background-color: $input-group-addon-bg;
  border: $input-border-width solid $input-group-addon-border-color;
  border-radius: $input-border-radius;
}
.input-group-append + .btn {
  padding: 0;
  border: 0;
}
.input-group > .input-group-prepend,
.input-group.input-group-append > .input-inner {
  @include border-right-radius(0);
}

.input-group > .input-group-append,
.input-group.input-group-prepend > .input-inner {
  @include border-left-radius(0);
}

.input-size-sm .mxyrc-input-inner {
  padding: $input-padding-y-sm $input-padding-x-sm;
  font-size: $input-font-size-sm;
  border-radius: $input-border-radius-sm;
}

.input-size-lg .mxyrc-input-inner {
  padding: $input-padding-y-lg $input-padding-x-lg;
  font-size: $input-font-size-lg;
  border-radius: $input-border-radius-lg;
}
```

##### 遗留问题

Input组件使用JSDoc的注释没有生成文档，无论是组件描述还是组件的props描述

#### AutoComplete组件

##### 需求分析

- 基于Input组件与DropDown菜单，获取到Input的值，由外部提供的筛选函数得到筛选后的数据，交给DropDown菜单展示，点击菜单某一项，将该项的值同步给Input组件，完成自动填充的整个过程
- 筛选函数由外部提供，可以是本地同步计算，也可以在内部使用异步网络请求来获取筛选后的数据
- 在点击菜单某一项后，对外暴露一个选取后的回调函数onSelect供外部使用

##### 编码

```tsx
import React, { FC, useState, ChangeEvent, useEffect, useRef, KeyboardEvent } from 'react'
import classNames from 'classnames'
import Input, { InputProps } from '../Input/Input'
import Icon from '../Icon/Icon'
import Transition from '../Transition/Transition'
import useDebounce from '../../hooks/useDebounce'
import useClickOutside from '../../hooks/useClickOutside'

export interface AutoCompleteProps extends Omit<InputProps, 'onSelect'> {
  /**
   * 筛选函数，根据输入的数据进行筛选，返回筛选后的数据
   */
  fetchSuggestions: (data: string) => DataSourceType[] | Promise<DataSourceType[]>;
  /**
   * 选择某一项后的回调函数
   */
  onSelect?: (item: DataSourceType) => void;
  /**
   * 自定义如何渲染待选的菜单项
   */
  renderOption?: (item: DataSourceType) => React.ReactElement;
}

interface DataSourceObj {
  value: string;
}

// DataSourceType必须是包含value属性的对象，更多的属性由泛型T指定
export type DataSourceType<T = {}> = T & DataSourceObj

/**
 * ## AutoComplete组件
 * ### 组件说明
 * #### 引入说明
 * ~~~js
 * // ESModule
 * import { AutoComplete } from 'mxyrc'
 * ~~~
 * #### 简介
 * 常用于根据用户输入内容进行自动提示补全的场景，避免用户输入太多
 * @param props 
 */
const AutoComplete: FC<AutoCompleteProps> = (props) => {
  const {
    fetchSuggestions,
    onSelect,
    value,
    renderOption,
    ...restProps
  } = props
  const [inputValue, setInputValue] = useState(value)
  const [suggestions, setSuggestions] = useState<DataSourceType[]>([])
  const [loading, setLoading] = useState(false)
  const [highlightIndex, setHighlightIndex] = useState(-1)
  const triggerSearch = useRef(true)
  const componentRef = useRef<HTMLDivElement>(null)
  const debouncedValue = useDebounce(300, inputValue)

  useClickOutside(componentRef, () => {
    setSuggestions([])
  })

  useEffect(() => {
    if (debouncedValue && triggerSearch.current) {
      const result = fetchSuggestions(debouncedValue)
      if (result instanceof Promise) {
        setLoading(true)
        result.then(data => {
          setLoading(false)
          setSuggestions(data)
        })
      } else {
        setSuggestions(result)
      }
    } else {
      setSuggestions([])
    }
    setHighlightIndex(-1)
  }, [debouncedValue])

  const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
    const value = e.target.value.trim()
    setInputValue(value)
    triggerSearch.current = true
  }
  const handleSelect = (item: DataSourceType) => {
    setInputValue(item.value)
    setSuggestions([])
    onSelect && onSelect(item)
    triggerSearch.current = false
  }

  const highlight = (index: number) => {
    if (index < 0) {
      index = 0
    }
    if (index >= suggestions.length) {
      index = suggestions.length - 1
    }
    setHighlightIndex(index)
  }

  const handleKeydown = (e: KeyboardEvent<HTMLInputElement>) => {
    switch(e.keyCode) {
      case 13:
        if (suggestions[highlightIndex]) {
          handleSelect(suggestions[highlightIndex])
        }
        break;
      case 38:
        highlight(highlightIndex - 1)
        break;
      case 40:
        highlight(highlightIndex + 1)
        break;
      case 27:
        setSuggestions([])
        break;
      default:
        break;
    }
  }

  const generateDropdown = () => {
    return (
      <Transition
        in={suggestions.length > 0}
        timeout={300}
        animation='zoom-in-left'
      >
        <ul className="mxyrc-suggestion-list">
          { 
            suggestions.map((item: DataSourceType, index: number) => {
              const cNames = classNames('suggestion-item', {
                'item-highlighted': index === highlightIndex
              })
              return (
                <li 
                  key={index}
                  className={cNames}
                  onClick={() => { handleSelect(item)}}
                >
                  { renderOption? renderOption(item): item.value }
                </li>
              ) 
            })
          }
        </ul>
      </Transition>
    )
  }
  return (
    <div className="mxyrc-auto-complete" ref={componentRef}>
      <Input
        value={inputValue}
        onChange={handleChange}
        onKeyDown={handleKeydown}
        {...restProps}
      />
      { loading && <ul><Icon icon={'spinner'} spin /></ul> }
      {/* { suggestions.length > 0 && generateDropdown() } */}
      { generateDropdown() }
    </div>
  )
}

export default AutoComplete
```

##### 样式

```scss
.mxyrc-auto-complete {
  position: relative;
}
.mxyrc-suggestion-list {
  list-style:none;
  padding-left: 0;
  white-space: nowrap;
  position: absolute;
  background: $white;
  z-index: 100;
  top: calc(100% + 8px);
  left: 0;
  border: $menu-border-width solid $menu-border-color;
  box-shadow: $submenu-box-shadow;
  width: 100%;
  .suggstions-loading-icon {
    display: flex;
    align-items: center;
    justify-content: center;
    min-height: 75px;
  }
  .suggestion-item {
    padding: $menu-item-padding-y $menu-item-padding-x;
    cursor: pointer;
    transition: $menu-transition;
    color: $body-color;
    &.is-active {
      background: $menu-item-active-color !important;
      color: $white !important;
    }
    &:hover {
      color: $menu-item-active-color !important;
    }
  }
  .suggestion-item.item-highlighted {
    color: $white;
    background: $primary;
  }
}
```

##### 遗留问题

AutoComplete组件使用JSDoc的注释没有生成文档，无论是组件描述还是组件的props描述

#### 打包项目

组件库项目是给别人用的，不需要进行代码打包为一个bundle包，以及代码压缩等效果，用户使用组件库后，用户会对自己的项目打包

##### 确定要打包的模块格式

在项目打包时，需要考虑对外发布的最终产品的模块格式。通常而言，有两种对外格式

- 浏览器直接引用：通过`script`标签或者`link`标签引入JavaScript或者CSS文件，通过暴露的全局变量来使用JavaScript功能，缺点在于引入的文件很大，有很多无效的代码，并且通过全局变量来使用不方便。
- 功能模块：浏览器无法直接使用，需要通过打包构建工具的参与才能交给浏览器使用。优点在于可以按需引用，无需直接引用很庞大的代码量，并且模块格式有很多其他的优势。**组件库一般都选择打包为模块格式，在此基础上，可能会再提供浏览器直接引用的方式。**模块格式一般选择ESModule，以及备选的CommonJS格式。

##### 创建项目入口文件

通过项目的入口文件，对外统一提供模块的引用路径。package.json文件的如下字段表示项目的入口文件：

- **main字段**：针对**CommonJS**规范提出，指定入口文件
- **module字段**：针对**ESModule**规范，指定入口文件

使用ESModule规范时，会根据module字段来查找项目的入口文件，如果没有该字段，才去查找main字段。

这里，module字段和main字段指定为同一个入口文件。

###### 组件入口文件

给每个组件创建入口文件，进行导入导出操作的中转。其中Menu组件比较特殊一点如下：

```tsx
// component/Menu/index.tsx
import Menu, { MenuProps } from './Menu'
import SubMenu, { SubMenuProps } from './SubMenu'
import MenuItem, { MenuItemProps } from './MenuItem'
import { FC } from 'react'

// 新建一个交叉类型，将Item和SubMenu属性挂载为静态属性到Menu上
export type MenuComponent = FC<MenuProps> & {
  Item: FC<MenuItemProps>;
  SubMenu: FC<SubMenuProps>
}

const TransMenu = Menu as MenuComponent

TransMenu.Item = MenuItem
TransMenu.SubMenu = SubMenu

export default TransMenu
```

我们想使用`<Menu.Item>`和`<Menu.SubMenu>`的方式来使用`<MenuItem>`和`<SubMenu>`

###### 项目入口文件

`src/index.tsx`中如下：

```tsx
// 添加对Icon组件的支持，不需要在指定icon的名称时，再加上前缀了
import { library } from '@fortawesome/fontawesome-svg-core'
import { fas } from '@fortawesome/free-solid-svg-icons'
library.add(fas)

export { default as Button } from './components/Button'
export { default as Icon } from './components/Icon'
export { default as AutoComplete } from './components/AutoComplete'
export { default as Input } from './components/Input'
export { default as Menu } from './components/Menu'
export { default as Transition } from './components/Transition'
```

##### typescript打包编译

将目前的typescript代码转换为ESModule代码

即`.tsx -> .jsx`，通过typescript编译器来完成

由于默认的tsconfig.json配置文件针对的是开发环境，所以我们需要新建一个针对生产环境打包编译的配置文件

###### tsconfig.build.json

新建一个typescript配置文件作为生产环境的打包配置文件。typescript的配置项很多，我们只需要针对自己所需来配置就好，其他配置项由typescript的默认值即可。

```json
{
  // 编译配置
  "compilerOptions": {
    // 编译后文件路径
    "outDir": "dist",
    // 编译后文件的模块规范，这里指定的是ESModule规范
    "module": "ESNext",
    // 编译后文件的语法标准，这里是es5标准
    "target": "es5",
    // 是否生成类型声明文件
    "declaration": true,
    // jsx编译结果，这里将jsx编译为react.createElement
    "jsx":"react",
    // 指定typescript编译器如何从一个模块标识符中查找给定文件，由于默认的classic模式在绝对路径下查找方式与node不一致，所以如果不指定该选项，会报错说找不到绝对路径下的一些文件
    "moduleResolution": "node",
    // 允许直接导入默认的导出成员，如import React from 'react', 如果不开启，则需要使用import * as React from 'react'
    "allowSyntheticDefaultImports": true
  },
  // 需要编译的文件
  "include": [
    // src路径下的所有文件
    "src"
  ],
  // 不需要编译的文件
  "exclude": [
    // 排除.test.tsx（测试）和stories.tsx（storybook）文件
    "src/**/*.test.tsx",
    "src/**/*.stories.tsx"
  ]
}
```

###### 添加编译命令

`"build-ts": "tsc -p tsconfig.build.json"`

##### 编译样式

将sass编写的样式文件编译为CSS文件

添加编译命令如下：

`"build-css": "node-sass ./src/styles/index.scss ./dist/index.css"`

#### 本地测试项目

在上传到npm包站点之前，需要在本地做一下测试。另外，如果项目进行了更新，避免上传以后再进行下载测试这种情况，更新后在本地测试更加有效率。

##### 将项目链接到全局路径

首先，在项目根目录下使用`npm link`命令创建一个软连接，该命令将会在node的全局包目录下创建一个软连接，该软连接指向项目（该方式类似于下载了一个全局安装的包，只是不是从网络下载，而是来自于本地项目），命令完成后如下

```bash
/Users/maxingyuan/.nvm/versions/node/v12.16.1/lib/node_modules/mxyrc -> /Users/maxingyuan/Sites/web_exercise/打造React组件库/react-component-library/mxyrc
```

全局包路径 -> 本地项目路径

##### 在测试项目下安装项目

在测试项目如test下，使用`npm link <package-name>`来下载上面创建的全局包，这条命令相当于在test项目下加载了package-name这个包。命令完成后如下

```bash
/Users/maxingyuan/Sites/web_exercise/打造React组件库/react-component-library/mxyrc-test/node_modules/mxyrc -> /Users/maxingyuan/.nvm/versions/node/v12.16.1/lib/node_modules/mxyrc -> /Users/maxingyuan/Sites/web_exercise/打造React组件库/react-component-library/mxyrc
```

测试项目的node_modules安装目录下 -> 全局安装目录下 -> 本地项目路径

##### 注意事项：两个不同React版本

在本地测试时，由于本地测试项目会安装一个React版本，而组件库本身也有一个React版本，这样由于存在两个不一致的版本会有错误发生。

###### 解决方案如下：

在组件库项目下，使用`npm link <test项目/node_modules/react>`命令，让组件库使用测试项目的react版本。

#### 发布项目

##### 配置package.json

除了默认的字段外，还需要如下字段

```json
{
  "name": "mxyrc",
  "version": "0.1.0",
  // 项目描述
  "description": "React Components Library",
  "private": false,
  "main": "./dist/index.js",
  "module": "./dist/index.js",
  "types": "./dist/index.d.ts",
  // license类型
  "license": "MIT",
  // 关键字
  "keywords": [
    "react",
    "component",
    "UI"
  ],
  // 项目的homepage
  "homepage": "https://gitee.com/mxydl2009/mxyrc",
  // 项目的仓库地址
  "repository": {
    "type": "git",
    "url": "https://gitee.com/mxydl2009/mxyrc"
  },
  // 需要发布到npm的文件，这里指定打包后的dist目录（当然，package.json、README.md等文件会自动发布），如果不指定files字段，会默认使用.gitignore文件作为参考
  "files": [
    "dist"
  ],
```

##### 精简依赖

开发依赖：只在开发过程中使用，在安装包时不会安装

运行依赖（生产依赖）：在使用npm install安装包时，这些依赖也会随之安装

###### 原则

- 类型声明文件不需要成为运行依赖
- 编译器（如typescript、node-sass）不需要成为运行依赖
- storybook和测试相关的不需要成为运行依赖
- 辅助开发的自动化脚本命令（如react-script）不需要成为运行依赖

###### 更改依赖

当我们开发的库或者插件需要依赖于一些环境（如我们的组件库依赖于react和react-dom），而这些环境同样也会成为使用我们组件库的项目的依赖环境，这样就会造成出现两个依赖环境版本的情况，进而混淆报错。

当我们的组件库需要依赖环境时，使用peerDependencies来指定依赖包。npm install安装组件库时，不会去下载peerDependencies指定的包，而是会在终端提示需要这些包，由用户来自行安装。如果用户已经在之前安装过这些依赖环境，则就不需要再安装了。

```json
"peerDependencies": {
  "react": "≥16.8.0",
  "react-dom": "≥16.8.0"
}
```

同时，为了我们开发方便，将react和react-dom移动到devDependencies里，这样我们本地安装时就会自动安装react了。

##### 发布前的代码规范检查与运行测试

###### 代码规范检查

在commit版本和发布前，需要对代码进行规范检查，避免不规范的代码进入版本和线上。

在开发过程中，由cra生成的项目已经自带ESLint规范，让我们在开发过程中可以随时看到规范检查的结果。不过，在某些节点（如commit，发布等）我们还是想整体再进行一次ESLint规范检查，这时候需要我们手动添加运行ESLint的命令。

`"lint": "eslint --ext js,ts,tsx src"`

运行ESLint，检查src目录下扩展名为js,ts,tsx的文件。

ESLint对warnings并不认为是error，所以可以通过。不过我们可以设置最大的warnings，超过最大warnings数量则会被认为不通过规范检查。

`"lint": "eslint --ext js,ts,tsx src --max-warnings 5"`

###### 运行测试

由react-script带来的npm test命令默认使用了watcher模式来运行测试用例，即运行后并不退出，而是等待文件更新后再次自动运行。

**如果想要只运行一次测试并退出，则需要设置环境变量**`CI=true` ：`"test:nowatch": "cross-env CI=true npm run test",`

###### 发布前运行测试与规范检查

此时，在发布前，我们运行一下测试与规范检查，通过后才发布到线上。

`"prepublish": "npm run test:nowatch && npm run lint && npm run build"`

由于`prepublish`命令会在`npm install `和` npm publish`时都会运行一次，所以这里需要使用`prepublishOnly`代替

###### 使用husky来进行git Hook

Husky 版本4的使用方式如下

在package.json中加入如下代码

```json
"husky": {
  "hooks": {
    "pre-commit": "npm run test:nowatch && npm run lint"
  }
},
```

#### 生成组件库的静态文档页面

目前，我们的storybook仅仅用来展示组件和组件相关的文档，并没有一个整体对组件库的介绍首页。

##### 添加首页

在src/IndexPage.stories.tsx中添加首页代码：

```tsx
import React from 'react'
import { Meta, Story } from '@storybook/react'

export default {
  title: 'IndexPage'
} as Meta


export const DefaultIndexPage: Story = () => {
  return (
    <div>
      <h2>mxyrc组件库</h2>
      <p>mxyrc是基于React的一套组件库，从零到一去学习如何构建自己的组件库</p>
      <h3>安装试试</h3>
      <code>
        npm install mxyrc -S
      </code>
    </div>
  )
}

DefaultIndexPage.storyName = '首页'
```

##### 配置stories的加载顺序（展示顺序）

默认情况下，stories的展示顺序是按照其加载顺序，通常是按照字母表顺序。这里我们需要自定义，将首页展示到第一个。

[参考资料](https://storybook.js.org/docs/react/writing-stories/naming-components-and-hierarchy)

在preview.js文件中加入如下配置：

```js
export const parameters = {
  options: {
    storySort: {
      method: '',
      order: ['IndexPage', 'components'],
    },
  },
};
```

##### 生成静态页面

运行`npm run build-storybook`，会在项目中生成storybook-static目录，里面是文档的静态页面，可以部署到服务器上。

###### 注意

在开发环境时（即`npm run storybook`），首页可以正常显示。但生成静态文档后，使用serve命令开启本地node服务器查看时，首页无法正常显示。如果点击其他组件（正常显示），再点击首页组件时，首页组件正常显示。

但是通过后续的CD服务将静态文档部署到GitHub的pages功能时，是可以正常访问的。

#### 持续集成CI

- 频繁地将代码集成到主干（通常是master）
- 快速发现错误
- 防止分支大幅度偏离

##### 使用Travis-ci和GitHub进行持续集成

- 将Travis-ci使用GitHub账户登录关联（**如果是初次注册，记得发送验证邮件，点击邮件里的验证，验证邮件可能会在垃圾邮件里**）
- 选择其中一个或者所有需要进行CI服务的仓库
- 在本地仓库中添加.travis.yml文件，文件里填写CI服务相关的信息
- 默认情况下，git push 会触发CI服务的运行，实际上，GitHub在收到新的commit时会通知Travis，由Travis来触发CI服务。

##### .travis.yml样例代码

默认情况下，触发的CI服务包含了install和script，其中install会下载依赖包，script会默认运行npm test。

在package-lock.json存在且npm版本支持下，install服务会使用`npm ci`来安装包，npm ci会根据package-lock.json来安装包，用package.json来验证版本号是否有误。如果存在问题，则停止服务。

```yaml
// CI服务的运行语言
language: node_js
node_js: 
  // 版本
  - "14.17.3"
// 默认的install命令会是npm ci或者npm install, 这里指定为npm install，因为node_js版本与node-sass版本存在兼容性问题，又懒得更改package-lock.json了。
install: 
  - "npm install"
// 指定需要进行缓存的目录，在每次运行CI服务时，不必重复下载安装包
case:
  directories:
  - node_modules
// 指定运行
env:
  - CI=true
```

#### 持续交付持续部署CD

- 频繁将软件的新版本交付给质量团队（QA）或者用户
- 代码通过评审后，自动部署到生产环境

组件库在完成持续集成后需要进行静态文档页面的部署，这样的持续部署CD行为可以通过Travis来解决

```yaml
language: node_js
node_js: 
  // 版本
  - "14.17.3"
// 默认的install命令会是npm ci或者npm install, 这里指定为npm install，因为node_js版本与node-sass版本存在兼容性问题，又懒得更改package-lock.json了。
install: 
  - "npm install"
// 指定需要进行缓存的目录，在每次运行CI服务时，不必重复下载安装包
case:
  directories:
  - node_modules
// 指定运行
env:
  - CI=true
// 指定script运行的命令, 这里暂时没考虑持续集成，只是考虑了持续部署，所以script默认的npm test被取代为npm run build-storybook
script:
  - npm run build-storybook
// 指定部署的配置，这里我们要将storybook-static静态文档页面部署到github pages，即创建一个gh-pages分支，将该分支代码作为pages要部署的静态资源
deploy:
  provider: pages
  skip_cleanup: true
  // github personal access token: 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
  github_token: $GITHUB_TOKEN  # Set in the settings page of your repository, as a secure variable, 在Travis中需要添加环境变量GITHUB_TOKEN为github personal access token
  // 指定本地的哪个文件夹要部署到github pages
  local_dir: storybook-static
  on:
  // 当master分支出现commit时触发deploy
    branch: master
```