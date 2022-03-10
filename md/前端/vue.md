## Vue，Angular和React

### 数据驱动页面

Angular1：最老套的脏检查。所谓的脏检查，指的是 Angular 1 在对数据变化的检查上，遵循每次用户交互时都检查一次数据是否变化，有变化就去更新 DOM 这一方法。

Vue1：使用响应式，初始化的时候，Watcher 监听了数据的每个属性，这样数据发生变化的时候，我们就能精确地知道数据的哪个 key 变了，去针对性修改对应的 DOM 即可。我们在网页中使用{{}}渲染一个变量，Vue 1 就会在内容里保存一个监听器监控这个变量，我们称之为 Watcher，数据有变化，watcher 会收到通知去更新网页。

React：在页面初始化的时候，在浏览器 DOM 之上，搞了一个叫虚拟 DOM 的东西，也就是用一个 JavaScript 对象来描述整个 DOM 树。我们可以很方便的通过虚拟 DOM 计算出变化的数据，去进行精确的修改。

``` html
<div id = "app">
    <p class = "item">Item1</p>
    <div class = "item">Item2</div>
</div>
虚拟DOM对象：
{
  tag: "div",
  attrs: {
    id: "app"
  },
  children: [
    {
      tag: "p",
      attrs: { className: "item" },
      children: ["Item1"]
    },
    {
      tag: "div",
      attrs: { className: "item" },
      children: ["Item2"]
    }
  ]
}
```

这个对象完整地描述了 DOM 的树形结构，这样数据有变化的时候，我们生成一份新的虚拟 DOM 数据，然后再对之前的虚拟 DOM 进行计算，算出需要修改的 DOM，再去页面进行操作。浏览器操作 DOM 一直都是性能杀手，而虚拟 DOM 的 Diff 的逻辑，又能够确保尽可能少的操作 DOM，这也是虚拟 DOM 驱动的框架性能一直比较优秀的原因之一。

### Vue和React的对比

在 Vue 框架下，如果数据变了，那框架会主动告诉你修改了哪些数据；而 React 的数据变化后，我们只能通过新老数据的计算 Diff 来得知数据的变化。

性能瓶颈：

- 对于 Vue 来说，它的一个核心就是“响应式”，也就是数据变化后，会主动通知我们。响应式数据新建 Watcher 监听，本身就比较损耗性能，项目大了之后每个数据都有一个 watcher 会影响性能。
- 对于 React 的虚拟 DOM 的 Diff 计算逻辑来说，如果虚拟 DOM 树过于庞大，使得计算时间大于 16.6ms(60fps，一秒钟每一帧的计算时间)，那么就可能会造成性能的卡顿。

解决方案：

- React 为了突破性能瓶颈，借鉴了操作系统时间分片的概念，引入了 Fiber 架构。通俗来说，就是把整个虚拟 DOM 树微观化，变成链表，然后我们利用浏览器的空闲时间计算 Diff。一旦浏览器有需求，我们可以把没计算完的任务放在一旁，把主进程控制权还给浏览器，等待浏览器下次空闲。在这 16.6 毫秒里，浏览器自己的渲染更新任务执行后，会有一部分的空闲时间，这段时间我们就用来计算 Diff。![image-20220310100543747](C:\xx\GitHub\LearningDocument\md\前端\vue.assets\image-20220310100543747.png)

- Vue 2 大胆引入虚拟 DOM 来解决响应式数据过多的问题。这个解决方案使用虚拟 DOM 解决了响应式数据过多的内存占用问题，又良好地规避了 React 中虚拟 DOM 的问题， 还通过虚拟 DOM 给 Vue 带来了跨端的能力。**响应式数据是主动推送变化，虚拟 DOM 是被动计算数据的 Diff，一个推一个拉，它们看起来是两个方向的技术，但被 Vue 2 很好地融合在一起，采用的方式就是组件级别的划分。**

对于 Vue 2 来说，组件之间的变化，可以通过响应式来通知更新。组件内部的数据变化，则通过虚拟 DOM 去更新页面。这样就把响应式的监听器，控制在了组件级别，而虚拟 DOM 的量级，也控制在了组件的大小。

除了响应式和虚拟 DOM 这个维度，Vue 和 React 还有一些理念和路线的不同，在模板的书写上，也走出了 template 和 JSX 两个路线。

![img](C:\xx\GitHub\LearningDocument\md\前端\vue.assets\669188c294d8e306072ef4273ec2630f.png)

React 的世界里只有 JSX，最终 JSX 都会在 Compiler 那一层，也就是工程化那里编译成 JS 来执行，所以 React 最终拥有了全部 JS 的动态性，这也导致了 React 的 API 一直很少，只有 state、hooks、Component 几个概念，主要都是 JavaScript 本身的语法和特性。

而 Vue 的世界默认是 template，也就是语法是限定死的，比如 v-if 和 v-for 等语法。有了这些写法的规矩后，我们可以在上线前做很多优化。Vue 3 很优秀的一个点，就是在虚拟 DOM 的静态标记上做到了极致，让静态的部分越过虚拟 DOM 的计算，真正做到了按需更新，很好的提高了性能。

在模板的书写上，除了 Vue 和 React 走出的 template 和 JSX 两个路线，还出现了 Svelte 这种框架，没有虚拟 DOM 的库，直接把模板编译成原生 DOM，几乎没有 Runtime，所有的逻辑都在 Compiler 层优化，算是另外一个极致。

![img](C:\xx\GitHub\LearningDocument\md\前端\vue.assets\bae38yye39eyy1609260caf90271cbb9.png)

## Vue3和Vue2

### Vue2

![image-20220310103431210](C:\xx\GitHub\LearningDocument\md\前端\vue.assets\image-20220310103431210-16468796726953.png)

首先从开发维护的角度看，Vue 2 是使用 Flow.js 来做类型校验。但现在 Flow.js 已经停止维护了，整个社区都在全面使用 TypeScript 来构建基础库，Vue 团队也不例外。

然后从社区的二次开发难度来说，Vue 2 内部运行时，是直接执行浏览器 API 的。但这样就会在 Vue 2 的跨端方案中带来问题，要么直接进入 Vue 源码中，和 Vue 一起维护，比如 Vue 2 中你就能见到 Weex 的文件夹。

**Vue 2 响应式并不是真正意义上的代理，而是基于 Object.defineProperty() 实现的。**这个 API 并不是代理，而是对某个属性进行拦截，所以有很多缺陷，比如：删除数据就无法监听，需要 $delete 等 API 辅助才能监听到。**并且，Option API 在组织代码较多组件的时候不易维护。**

### Vue3 新特性

#### 响应式系统

Vue 2 的响应式机制是基于 Object.defineProperty() 这个 API 实现的，此外，Vue 还使用了 Proxy，这两者看起来都像是对数据的读写进行拦截，但是 defineProperty 是拦截具体某个属性，Proxy 才是真正的“代理”。

``` js
//当项目里“读取 obj.title”和“修改 obj.title”的时候被 defineProperty 拦截，但 defineProperty 对不存在的属性无法拦截，所以 Vue 2 中所有数据必须要在 data 里声明。而且，如果 title 是一个数组的时候，对数组的操作，并不会改变 obj.title 的指向，虽然我们可以通过拦截.push 等操作实现部分功能，但是对数组的长度的修改等操作还是无法实现拦截，所以还需要额外的 $set 等 API。
Object.defineProperty(obj, 'title', {
  get() {},
  set() {},
})

//虽然 Proxy 拦截 obj 这个数据，但 obj 具体是什么属性，Proxy 则不关心，统一都拦截了。而且 Proxy 还可以监听更多的数据格式，比如 Set、Map，这是 Vue 2 做不到的。Proxy 存在一些兼容性问题，这也是为什么 Vue 3 不兼容 IE11 以下的浏览器的原因。
new Proxy(obj, {
  get() { },
  set() { },
})
```

#### 自定义渲染器

Vue 2 内部所有的模块都是揉在一起的，这样做会导致不好扩展的问题。Vue 3 是怎么解决这个问题的呢？那就是拆包，使用最近流行的 monorepo 管理方式，响应式、编译和运行时全部独立了，变成下图所示的模样：![image-20220310104517992](C:\xx\GitHub\LearningDocument\md\前端\vue.assets\image-20220310104517992-16468803188464.png)

在 Vue 3 的组织架构中，响应式独立了出来。而 Vue 2 的响应式只服务于 Vue，Vue 3 的响应式就和 Vue 解耦了，你甚至可以在 Node.js 和 React 中使用响应式。**渲染的逻辑也拆成了平台无关渲染逻辑和浏览器渲染 API 两部分 。**

在这个架构下，Node 的一些库，甚至 React 都可以依赖响应式。在任何时候，如果你希望数据被修改了之后能通知你，你都可以单独依赖 Vue 3 的响应式。你想使用 Vue 3 开发小程序、开发 canvas 小游戏以及开发客户端的时候，就不用全部 fork Vue 的代码，只需要实现平台的渲染逻辑就可以。![image-20220310104742104](C:\xx\GitHub\LearningDocument\md\前端\vue.assets\image-20220310104742104-16468804633215.png)

#### 全部模块使用 TypeScript 重构

#### Composition API 组合语法

#### 新的组件

- Fragment: Vue 3 组件不再要求有一个唯一的根节点，清除了很多无用的占位 div。
- Teleport: 允许组件渲染在别的元素内，主要开发弹窗组件的时候特别有用。
- Suspense: 异步组件，更方便开发有异步请求的组件。

#### 新一代工程化工具 Vite

Vite 不在 Vue 3 的代码包内，和 Vue 也不是强绑定，Vite 的竞品是 Webpack，而且按照现在的趋势看，使用率超过 Webpack 也是早晚的事。Vite 主要提升的是开发的体验，Webpack 等工程化工具的原理，就是根据你的 import 依赖逻辑，形成一个依赖图，然后调用对应的处理工具，把整个项目打包后，放在内存里再启动调试。由于要预打包，所以复杂项目的开发，启动调试环境需要 3 分钟都很常见，Vite 就是为了解决这个时间资源的消耗问题出现的。

Webpack:

![image-20220310110010568](C:\xx\GitHub\LearningDocument\md\前端\vue.assets\image-20220310110010568.png)

Vite:

![image-20220310110046329](C:\xx\GitHub\LearningDocument\md\前端\vue.assets\image-20220310110046329.png)