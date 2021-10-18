# 单例模式

>想一想：独生子，一对夫妻只能生一个孩子。

单例模式的定义是：保证一个类仅有一个实例，并提供一个访问它的全局访问点。

单例模式是一种常用的模式，有一些对象我们往往只需要一个，比如线程池、全局缓存、浏览器中的 <code>window</code> 对象等。在 <span style='color: #3eaf7c;'>JavaScript</span> 开发中，单例模式的用途同样非常广泛：

- <code>webSocket</code> ，全局只需要创建一个实例，数据处理、错误异常等等都只需要维护这个实例即可；
- 部分 UI 库的 <code>toast</code>、<code>message</code>、<code>notify</code> 弹窗通知，页面只需要创建一次浮窗，每次点击或其他需要唤起浮窗的操作，都是基于这个浮窗去操作（当然弹窗通知也可以是多个）。
    - [vant](https://youzan.github.io/vant/#/zh-CN/toast#dan-li-mo-shi) 的 <code>Toast</code>： 默认采用单例模式，即同一时间只会存在一个 Toast，如果需要在同一时间弹出多个 <code>Toast</code>，需要调用 <code>Toast.allowMultiple();</code>


## 实现单例模式

要实现一个标准的单例模式并不复杂，<span style='color: #f08d49;'>无非是用一个变量来标志当前是否已经为某个类创建过对象，如果是，则在下一次获取该类的实例时，直接返回之前创建的对象</span>。

从经典意义上来说，<span style='color: #3eaf7c;'>Singleton</span> 模式，在该实例不存在的情况下，可以通过一个方法创建一个类来实现创建类的新实例；如果实例已经存在，它会简单返回该对象的引用。

<span style='color: #3eaf7c;'>Singleton</span> 不同于静态类（或对象），因为我们可以推迟它们的初始化，这通常是因为它们需要一些信息，而这些信息在初始化期间可能无法获得。

对于没有察觉到之前的引用的代码，它们不会提供方便检索的方法。这是因为它既不是对象，也不是由一个 <span style='color: #3eaf7c;'>Singleton</span> 返回的“类”；它是一个结构。

<span style='color: #f08d49;'>思考一下闭包变量为何实际上并不是闭包，而提供闭包的函数作用域是闭包。</span>在 <span style='color: #3eaf7c;'>JavaScript</span> 中，<span style='color: #3eaf7c;'>Singleton</span> 充当共享资源命名空间，从全局命名空间中隔离出代码实现，从而为函数提供单一访问点。

```javascript
const TAG = "简单的单例模式";
const Singleton = function (name) {
  this.name = name;
};

Singleton.prototype.getName = function () {
  return this.name;
};

Singleton.getInstance = (function () {
  let instance = null;
  return function (name) {
    if (!instance) {
      instance = new Singleton(name);
    }
    return instance;
  };
})();

const a = Singleton.getInstance("dome-a");
const b = Singleton.getInstance("dome-b");
const result = a === b; //  true
console.log(`${TAG}:`, result);
```

我们通过 <code>Singleton.getInstance</code> 来获取 <span style='color: #3eaf7c;'>Singleton</span> 类的唯一对象，这种方式相对简单，但有一个问题，就是增加了这个类的“<span style='color: #3eaf7c;'>不透明性</span>”,  <span style='color: #3eaf7c;'>Singleton</span> 类的使用者必须知道这是一个单例类，<span style='color: #f08d49;'>跟以往通过new XXX的方式来获取对象不同</span>，这里偏要使用 <code>Singleton.getInstance</code> 来获取对象。

虽然现在已经完成了一个单例模式的编写，但这段单例模式代码的意义并不大。


## 透明的单例模式

我们现在的目标是实现一个“透明”的单例类，用户从这个类中创建对象的时候，可以像使用其他任何普通类一样（即通过 new XXX 的语法声明实例）。

```javascript
const TAG = "透明的单例模式";
const Singleton = (function () {
  let instance = null;

  const Singleton = function (name) {
    if (instance) {
      return instance;
    }
    this.name = name;
    return (instance = this);
  };

  Singleton.prototype.getName = function () {
    return this.name;
  };

  return Singleton;
})();

const a = new Singleton("dome-a");
const b = new Singleton("dome-b");
const result = a === b; //  true
console.log(`${TAG}:`, result);
```

虽然现在完成了一个透明的单例类的编写，但它同样有一些缺点。

为了把 <span style='color: #3eaf7c;'>instance</span> 封装起来，我们使用了自执行的匿名函数和闭包，并且让这个匿名函数返回真正的 <span style='color: #3eaf7c;'>Singleton</span> 构造方法，这增加了一些程序的复杂度，阅读起来也不是很舒服。

观察现在的 <span style='color: #3eaf7c;'>Singleton</span> 构造函数，实际上负责了两件事情：

- 第一是创建对象和执行初始化；
- 第二是保证只有一个对象。

虽然我们目前还没有接触过“<span style='color: #3eaf7c;'>单一职责原则</span>”的概念，但可以明确的是，这是一种不好的做法，至少这个构造函数看起来很奇怪。


## 用代理实现单例模式

现在我们通过引入代理类的方式，来解决上面提到的问题。

```javascript
const TAG = "代理的单例模式";
const Singleton = function (name) {
  this.name = name;
};

Singleton.prototype.getName = function () {
  return this.name;
};

const ProxySingleton = (function () {
  let instance = null;
  return function (name) {
    if (!instance) {
      instance = new Singleton(name);
    }
    return instance;
  };
})();

const a = new ProxySingleton("dome-a");
const b = new ProxySingleton("dome-b");
const result = a === b; //  true
console.log(`${TAG}:`, result);
```

通过引入代理类的方式，我们同样完成了一个单例模式的编写，跟之前不同的是，现在我们把负责管理单例的逻辑移到了代理类 <span style='color: #3eaf7c;'>ProxySingleton</span> 中。这样一来，<span style='color: #3eaf7c;'>Singleton</span> 就变成了一个普通的类，<span style='color: #f08d49;'>它跟 <span style='color: #3eaf7c;'>ProxySingleton</span> 组合起来可以达到单例模式的效果</span>。

本例是缓存代理的应用之一，在<span style='color: #3eaf7c;'>代理模式</span>中，我们将继续了解代理带来的好处。


## JavaScript 中的单例模式

前面提到的几种单例模式的实现，更多的是接近传统面向对象语言中的实现，单例对象从“类”中创建而来。在以类为中心的语言中，这是很自然的做法。

比如在 <span style='color: #3eaf7c;'>Java</span> 中，如果需要某个对象，就必须先定义一个类，对象总是从类中创建而来的。

但 <span style='color: #3eaf7c;'>JavaScript</span> 其实是一门无类（class-free）语言，也正因为如此，生搬单例模式的概念并无意义。

在 <span style='color: #3eaf7c;'>JavaScript</span> 中创建对象的方法非常简单，既然我们只需要一个“唯一”的对象，为什么要为它先创建一个“类”呢？这无异于穿棉衣洗澡，传统的单例模式实现在 <span style='color: #3eaf7c;'>JavaScript</span> 中并不适用。

<span style='color: #f08d49;'>单例模式的核心是确保只有一个实例，并提供全局访问。</span>全局变量不是单例模式，但在 <span style='color: #3eaf7c;'>JavaScript</span> 开发中，我们经常会把全局变量当成单例来使用。例如：

```javascript
var global = {};
```

当用这种方式创建对象 <span style='color: #3eaf7c;'>global</span> 时，对象 <span style='color: #3eaf7c;'>global</span> 确实是独一无二的。

如果 <span style='color: #3eaf7c;'>global</span> 变量被声明在全局作用域下，则我们可以在代码中的任何位置使用这个变量，全局变量提供给全局访问是理所当然的。这样就满足了单例模式的两个条件。

<span style='color: #f08d49;'>但是全局变量存在很多问题，它很容易造成命名空间污染。</span>

在大中型项目中，如果不加以限制和管理，程序中可能存在很多这样的变量。

<span style='color: #3eaf7c;'>JavaScript</span> 中的变量也很容易被不小心覆盖，相信每个 <span style='color: #3eaf7c;'>JavaScript</span> 程序员都曾经历过变量冲突的痛苦，就像上面的对象 <code>var global = {};</code> ，随时有可能被别人覆盖。

<span style='color: #3eaf7c;'>Douglas Crockford</span> 多次把全局变量称为 <span style='color: #3eaf7c;'>JavaScript</span> 中最糟糕的特性。

在对 <span style='color: #3eaf7c;'>JavaScript</span> 的创造者 <span style='color: #3eaf7c;'>Brendan Eich</span> 的访谈中， <span style='color: #f08d49;'><span style='color: #3eaf7c;'>Brendan Eich</span> 本人也承认全局变量是设计上的失误，是在没有足够的时间思考一些东西的情况下导致的结果。</span>

作为普通的开发者，我们有必要<span style='color: #f08d49;'>尽量减少全局变量的使用</span>，即使需要，也要把它的污染降到最低。以下几种方式可以相对降低全局变量带来的命名污染：

- <b>使用命名空间</b>

    适当地使用命名空间，并不会杜绝全局变量，但可以减少全局变量的数量。

- <b>使用闭包封装私有变量</b>

    把一些变量封装在闭包的内部，只暴露一些接口跟外界通信。一般用下划线来约定私有变量，例如： <span style='color: #3eaf7c;'>__name 和 __age</span> ，它们被封装在闭包产生的作用域中，外部是访问不到这两个变量的，这就避免了对全局的命令污染。


## 惰性单例

惰性单例指的是在需要的时候才创建对象实例。

惰性单例是单例模式的重点。

实际上在本节开头就使用过这种技术， <span style='color: #3eaf7c;'>instance</span> 实例对象总是在我们调用 <code>Singleton.getInstance</code> 的时候才被创建，而不是在页面加载好的时候就创建，代码如下：

```javascript
Singleton.getInstance = (function () {
  let instance = null;
  return function (name) {
    if (!instance) {
      instance = new Singleton(name);
    }
    return instance;
  };
})();
```

不过这是基于“类”的单例模式，前面说过，基于“类”的单例模式在 <span style='color: #3eaf7c;'>JavaScript</span> 中并不适用。


## 小结

我们先了解了传统的单例模式实现，也了解到因为语言的差异性，有更适合的方法在JavaScript中创建单例。还提到了代理模式和单一职责原则。

实际上也提到了<span style='color: #3eaf7c;'>闭包</span>和<span style='color: #3eaf7c;'>高阶函数</span>的概念。<span style='color: #f08d49;'>单例模式是一种简单但非常实用的模式，特别是惰性单例技术，在合适的时候才创建对象，并且只创建唯一的一个。</span>

更奇妙的是，创建对象和管理单例的职责被分布在两个不同的方法中，这两个方法组合起来才具有单例模式的威力。



# 单例模式的应用—— <span style='color: #3eaf7c;'>vant</span> 的组件 <code>Toast</code>

<span style='color: #3eaf7c;'>vant</span> 提供的组件 <code>Toast</code> ， 默认采用单例模式，即同一时间只会存在一个 <code>Toast</code> ，如果需要在同一时间弹出多个 Toast，可以执行 <code>Toast.allowMultiple();</code> 。参考 [vant 文档](https://youzan.github.io/vant/#/zh-CN/toast#dan-li-mo-shi)

## [Toast.allowMultiple()](https://github.com/youzan/vant/blob/a3380f08c06f5fd32443e9b033e3d8a29c1f726a/packages/vant/src/toast/function-call.tsx#L184)

想要了解 <code>Toast</code> 为什么默认是单例模式，为什么执行上面的方法就可以弹出多个 Toast，就要去看一看它的源码做了什么操作。

```javascript
Toast.allowMultiple = (value = true) => {
  allowMultiple = value;
};
```

发现 <code>Toast.allowMultiple();</code> 仅仅是设置了一个全局变量  <code>allowMultiple</code> 的值为 <code>true</code> 。

现在还看不出来为什么，接着往下看。

## [Toast.loading()](https://github.com/youzan/vant/blob/a3380f08c06f5fd32443e9b033e3d8a29c1f726a/packages/vant/src/toast/function-call.tsx#L141)

一般使用 <code>Toast</code> 是直接在 <span style='color: #3eaf7c;'>JavaScript</span> 代码需要的地方，直接调用 <code>Toast.loading()</code> 、
<code>Toast.success()</code> 、<code>Toast.fail()</code> ，或者直接调用 <code>Toast()</code> 就可以了。

当我们去调用 <code>Toast.loading()</code> 时，实际上是调用了 <code>Toast()</code> 。

```javascript
const createMethod = (type: ToastType) => (options: string | ToastOptions) =>
  Toast(extend({ type }, parseOptions(options)));

Toast.loading = createMethod('loading');
Toast.success = createMethod('success');
Toast.fail = createMethod('fail');
```

## [Toast()](https://github.com/youzan/vant/blob/a3380f08c06f5fd32443e9b033e3d8a29c1f726a/packages/vant/src/toast/function-call.tsx#L121)

现在去看看 <code>Toast()</code> 函数：

- 判断了终端，不是浏览器环境直接返回空对象；
- <code>getInstance()</code> 获取实例；
- 组装 <span style='color: #3eaf7c;'>options</span> 参数；
- 调用 <code>toast.open()</code> 方法。

```javascript
function Toast(options: string | ToastOptions = {}) {
  if (!inBrowser) {
    return {} as ComponentInstance;
  }

  const toast = getInstance();
  const parsedOptions = parseOptions(options);

  toast.open(
    extend(
      {},
      currentOptions,
      defaultOptionsMap.get(parsedOptions.type || currentOptions.type!),
      parsedOptions
    )
  );

  return toast;
}
```

## [getInstance()](https://github.com/youzan/vant/blob/a3380f08c06f5fd32443e9b033e3d8a29c1f726a/packages/vant/src/toast/function-call.tsx#L112)

- 定义了两个全局变量：
    - <code>queue</code> ，数组类型，代表所有的 <span style='color: #3eaf7c;'>toast</span> 的一个队列； 
    - <code>allowMultiple</code> ，布尔值，是否允许同时存在多个 <span style='color: #3eaf7c;'>toast</span>
- 当 <code>queue</code> 是空数组，或者 <code>allowMultiple = true</code> 的时候，才去调用 <code>createInstance()</code> 方法去创建实例，所以默认是<span style='color: #f08d49;'>单例模式</span>。要想同时可以存在多个 <span style='color: #3eaf7c;'>toast</span> ，需要设置 <code>allowMultiple</code> 的值为 <code>true</code> 。

```javascript
let queue: ComponentInstance[] = [];
let allowMultiple = false;

function getInstance() {
  if (!queue.length || allowMultiple) {
    const instance = createInstance();
    queue.push(instance);
  }

  return queue[queue.length - 1];
}
```

## [createInstance()](https://github.com/youzan/vant/blob/a3380f08c06f5fd32443e9b033e3d8a29c1f726a/packages/vant/src/toast/function-call.tsx#L72)

```javascript
function createInstance() {
  const { instance, unmount } = mountComponent({
    setup() {
      const message = ref('');
      const { open, state, close, toggle } = usePopupState();

      const onClosed = () => {
        if (allowMultiple) {
          queue = queue.filter((item) => item !== instance);
          unmount();
        }
      };

      const render = () => {
        const attrs: Record<string, unknown> = {
          onClosed,
          'onUpdate:show': toggle,
        };
        return <VanToast {...state} {...attrs} />;
      };

      // support dynamic modification of message
      watch(message, (val) => {
        state.message = val;
      });

      // rewrite render function
      (getCurrentInstance() as any).render = render;

      return {
        open,
        clear: close,
        message,
      };
    },
  });

  return instance;
}
```