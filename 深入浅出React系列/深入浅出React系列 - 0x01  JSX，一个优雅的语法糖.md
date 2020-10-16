## 深入浅出React系列 - 0x01  JSX，一个优雅的语法糖

> 这篇文章是我的 **深入浅出React系列** 的第一篇。这个系列主要是从基础到原理以及源码的解读，一起体验 React 一个又一个划时代的设计。这个系列不光会介绍 React 这个UI框架，也会探索 React 牵扯出的生态比如：Redux、React Router等等。当然还会分享一些工程化的实践

### 如何使用React？

在`React`的[官方文档](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/docs/getting-started.html)以及很详细的给出了如何使用安装、使用`React`的生态。为了能够专注于这篇文章的内容，笔者采用的是官方的`create-react-app`这么一个工具链，具体使用方法在文档里也说的十分清晰。

笔者在这里已经键了一个工程目录名叫`react-learning`，后续与大家探讨的内容也将是使用这个工程目录，这样能够保证一定的内容连贯性。

![07Mbu9.png](https://s1.ax1x.com/2020/10/15/07Mbu9.png)



### JSX的简单认识

这篇文章的目的是通过`JSX`这一个点谈谈`React`是怎么通过这么一个语法糖将我们的元素进行挂载、渲染的。

首先我们要清楚的是，`JSX`并不是一个新的语言、一个模板语法或者一个库。`React`官方 给出的定义是：

> JSX，是一个 JavaScript 的语法扩展

我们可以从这个段话中揣摩到一点：`JSX`是`JS`的一个语法扩展，说明`JSX`是支持`JS`的所有功能的，这是毫无疑问的。

在`React`中，我们通过`JSX` 生成 **元素**。

```jsx
const el = <h1>Hello, React</h1>
```

看到`JSX` 这样的写法，很多人难免会想到类似于`EJS`这样的模板库，或者`JSP`这样的前后端耦合模式。这样`UI`逻辑与数据逻辑耦合不是会导致逻辑冗余吗？这也是`React`一个具有前瞻性或者划时代性的设计思想：组件化。`React`通过将`UI`和`逻辑`共同放在一个称之为`组件`单元中来实现我们业务上的**关注点分离**。这是一个很优秀的思想，在后面的逐渐深入中我们会明显地感受到。

我们再来多看点`JSX`的使用加深印象：

```jsx
// demo1
const name = 'Uni';
const el = <div>Hello, this is {name}</div>    // 渲染结果：Hello，this is Uni
```

可以看到，我们通过`{}`可以轻松地在`JSX`中使用我们的`JS`语法，这也就是说：

```js
const el = <div>Hello, this is {name}</div>  // JSX
```

这是一个`JSX（或称作JSX表达式）`。而我们的`const name = 'Uni';`则是一段普通的`JS` 表达式

具体的`JSX`的使用可以详见[官网](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/docs/introducing-jsx.html)，笔者便不在此进行没营养的阐述，将墨水更多地花在我们接下来的深入理解部分。

### 如何渲染？

我们可以看到的时候，我们刚刚的`demo1`代码知识两个简单的表达式，但是没有被渲染。那么，如何渲染呢？

在`React`中我们通过`ReactDOM.render` 这么一个`API`进行元素的渲染或者说挂载，这个`API`通过`react-dom`引入

我们来看看`ReactDOM.render`的入参：

```js
ReactDOM.render(
    // 需要渲染的元素
    element, 
    // 元素挂载的目标容器（一个真实DOM）
    container,
    // 回调函数，可选参数，可以用来处理渲染结束后的逻辑
    [callback]
)
```

我们先来看看第二个参数：container。这个参数是我们的一个`真实DOM`，用于挂载、管理我们的`React元素`。我们来看看我们的`create-react-app`工具链：

![07JDVH.png](https://s1.ax1x.com/2020/10/15/07JDVH.png)

再来看看`src/index.js` 的内容：

![07g0pT.png](https://s1.ax1x.com/2020/10/16/07g0pT.png)

可以理解我们的`React元素`是一律挂载在我们这个`DOM`下的，也就是说我们的`React`能操控的只有这个`DOM`下的内容，对其他的`DOM`没有控制权。

这样做的目的可见一斑，我们能够集中管理我们的`React应用`，能够做到多框架的开发或者其他库的注入。

我们再来看看我们的`ReactDOM.render`的第一个参数：`element`。

前面笔者有提到我们是通过`JSX`生成`React 元素`，`React 元素`是什么我们马上研究，但是我们可以知道第一次参数我们可以写入一个`JSX`，我们可以将这个`JSX`生成的元素挂载在我们的第二个参数（也就是一个真实的DOM）下：

![07gUkq.png](https://s1.ax1x.com/2020/10/16/07gUkq.png)

可以看到我对这个文件进行了一个重写，我们的目的是将`el`这个元素渲染在我们的页面上，在当前工程目录下运行`npm start`命令或者`yarn start`即可启动这个项目：

![07tgu8.png](https://s1.ax1x.com/2020/10/15/07tgu8.png)

我们可以看到页面上成功渲染了元素，而且是在渲染在我们的`root`元素下的。



### React元素深入解析

细心的同学可以观察到笔者在重写`src/index.js`文件的时候，写了这样一句话：`import Reat from 'react'`

不难理解我们引入了`react`库中的内容，但是在重写文件中笔者却一次都没有使用这个`React`，那为什么还要引入呢？

回顾一下我们篇文章的标题：`JSX,一个优雅的语法糖`。

没错，`JSX`是一个语法糖，在我们使用`JSX`构造元素后其实是在使用`React.createElement`这个`API`构建我们的`React元素`

比如我们有这样一段`JSX`：

```jsx
const tech = (   // 使用括号进行包裹能够使得我们的换行书写，代码可读性更高
	<div className="front-end">   // 请自行翻阅官网了解 React 采用驼峰式命名属性的动机
    	This is React
    </div>
)
```

其实通过`Babel`会转换为这样的代码(关于Babel后面在`Webpack`系列文章会进行讲解，请先自行查阅有个粗略的认识)

```js
const tech = React.createElement(
	'div',
    {
        className: 'front-end'
    },
    'This is React'
)
```

这也是`JSX`构建出`React 元素`的真正实现。

所以我们现在可以来看看`React.createElement`的源码以及参数解读：

```js
/**
 * React的创建元素方法
 * @param type 标签名字符串(如’div‘或'span')，也可以是React组件类型，或是React fragment类型
 * @param config 包含元素各个属性键值对的对象
 * @param children 包含元素的子节点或者子元素
 */
function createElement(type, config, children) {
  let propName; // 声明一个变量，储存后面循环需要用到的元素属性
  const props = {}; // 储存元素属性的键值对集合
  let key = null; // 储存元素的key值
  let ref = null; // 储存元素的ref属性
  let self = null;  // 下面文章介绍
  let source = null; // 下面文章介绍

  if (config != null) { // 判断config是否为空，看看是不是没有属性
    // hasValidRef()这个方法就是判断config有没有ref属性，有的话就赋值给之前定义好的ref变量
    if (hasValidRef(config)) {
      ref = config.ref;
    }
    // hasValidKey()这个方法就是判断config有没有key属性，有的话就赋值给之前定义好的key变量
    if (hasValidKey(config)) {
      key = '' + config.key; // key值看来还给转成了字符串 
    }
    // __self和__source下面文章做介绍，实际也没搞明白是干嘛的
    self = config.__self === undefined ? null : config.__self;
    source = config.__source === undefined ? null : config.__source;
    // 现在就是要把config里面的属性都一个一个挪到props这个之前声明好的对象里面
    for (propName in config) {
      if (
        // 判断某个config的属性是不是原型上的
        hasOwnProperty.call(config, propName) &&  // 这行判断是不是原型链上的属性
        !RESERVED_PROPS.hasOwnProperty(propName) // 不能是原型链上的属性，也不能是key，ref，__self以及__source
      ) {
        props[propName] = config[propName]; // 乾坤大挪移，把config上的属性一个一个转到props里面
      }
    }
  }
  // 处理除了type和config属性剩下的其他参数
  const childrenLength = arguments.length - 2; // 抛去type和config，剩下的参数个数
  if (childrenLength === 1) { // 如果抛去type和config，就只剩下一个参数，就直接把这个参数的值赋给props.children
    props.children = children; // 一个参数的情况一般是只有一个文本节点
  } else if (childrenLength > 1) { // 如果不是一个呢？？
    const childArray = Array(childrenLength); // 声明一个有剩下参数个数的数组
    for (let i = 0; i < childrenLength; i++) { // 然后遍历，把每个参数赋值到上面声明的数组里
      childArray[i] = arguments[i + 2];
    }
    props.children = childArray; // 最后把这个数组赋值给props.children
  } // 所以props.children要不是一个字符串，要不就是一个数组

  // 如果有type并且type有defaultProps属性就执行下面这段
  // 那defaultProps属性是啥呢？？
  // 如果传进来的是一个组件，而不是div或者span这种标签，可能就会有props，从父组件传进来的值如果没有的默认值
  if (type && type.defaultProps) {
    const defaultProps = type.defaultProps;
    for (propName in defaultProps) { // 遍历，然后也放到props里面
      if (props[propName] === undefined) {
        props[propName] = defaultProps[propName];
      }
    }
  }
  // 所以props里面存的是config的属性值，然后还有children的属性，存的是字符串或者数组，还有一部分defaultProps的属性
  // 然后返回一个调用ReactElement执行方法，并传入刚才处理过的参数
  return ReactElement(
    type,
    key,
    ref,
    self,
    source,
    ReactCurrentOwner.current,
    props,
  );
}
```

这段源码及注释摘自：https://zhuanlan.zhihu.com/p/68283472

我想具体解读一下核心部分：

首先我们来看看入参：

```js
export function createElement(type, config, children)
```

我们来理解下这三个参数：

- type:  这个参数是我们节点的类型。在`React`中节点分为两个类型：一个是我们普通的标签节点，例如：`div`、`span`。第二个是我们的`React组件`，例如：`Fragment`、自定义组件等等
- config：通过我们`JSX`转`React.createElement`的写法也能够看出来，这个参数是一个对象，以键值对的形式存放当前节点的属性
- children:  通过参数命名我们不难知道，这个参数是用于记录我们的当前节点的子节点的，是一个对象

举个例子来帮你理解下这段入参解读：

```jsx
const key = Date.now();   // 假设当前时间是：1602774378190

const el = (
	<div className="uni" tech="react" key={key}>
    	<p className="title">Welcome</p>
    </div>
)
```

转化为`React.createElement`：

```js
const key = Date.now();  // 假设当前时间是：1602774378190

const el = React.createElement(
	'div',
    {
        className: 'uni',
        tech: 'react',
        key: 1602774378190
    },
    React.createElement(
    	p,
        {
            className: 'title'
        },
        "Welcome"
    )
)
```

我们可以看到`React.createElement`函数的返回（或者说出参）是一个新的函数`ReactElement`调用。

我们先来看看`ReactElement`的核心源码：

```js
const ReactElement = function(type, key, ref, self, source, owner, props) {
  const element = {
    
    $$typeof: REACT_ELEMENT_TYPE,  // 值是一个常量，用于表示我们的 React Element
    type: type,
    key: key,
    ref: ref,
    props: props,
    _owner: owner,  // 记录创造该元素的组件
  };
 	
  // something else...
    
    
  return element;
};
```

`ReactElement`所做的事情其实很简单，就是把传入的参按照一定的规则创建出我们的`element`对象，然后返回给我们的`createElement`。

而这个`element`对象的实例是一个对`DOM`的描述，也就是我们`虚拟DOM`，这也揭示了我们的`虚拟DOM`其实就是`JavaScript 对象`。这也是为什么`虚拟DOM`的概念被称赞为一个划时代的概念。原因不仅仅是因为操作`JavaScript`对象的开销要远小于操作`DOM API`的开销从而节省了性能，这也使得跨端开发成为可能。因为`DOM`只能存在于我们的`B/S`架构中的浏览器里，但是`JavaScript对象`是可以存在于其他端（或其他架构中的），达到了一个高移植性。

看到这里`React.creatElement`的用途也不言而喻，无非就是将我们节点的数据进行一定规则的`格式化`，然后将这些数据传递给我们的`ReactElement`去组装我们的`element`对象。



### 回归

那为什么不直接用`React.creatElemet`来书写我们的`React`引用呢？第一是因为`JSX`使得我们能够很方便、简介地书写我们的`React`引用，第二点是这种`UI`与逻辑的内耦合其实能够让我们更加好地关注我们的业务，以及分离我们的业务关注点



