
【作者】：Eric Elliott

【出处】：Medium

【原文链接】：https://medium.com/javascript-scene/a-functional-programmers-introduction-to-javascript-composing-software-d670d14ede30

> 注意：此文是`Composing Software`系列专题的介绍([现在出书了][0])，该系列旨在帮助大家从零开始用 JavaScript ES6+ 学习函数式编程和基于组件的软件开发技术（compositional software techniques）。敬请关注，精彩不断。
下一节的链接[戳这里][1]

---

对不熟悉 JavaScript 或者不熟悉 ES6+ 的选手们来说，这个系列的文章只起到师傅引进门的作用哈。不管你是 JavaScript 老鸟还是菜鸟，都能学到新东西，真的，哥从不忽悠人。接下来的内容也就是撩一下你，但足够吊足你胃口。如果真的被撩成功，还想了解更多，前方等你入坑。

学代码最好的办法就是写代码。建议你在学习时全程使用类似[CodePen][2]或者[Babel REPL][3]的交互式的编程环境。

或者，还可以用 node 跟浏览器的控制台 REPLs。

## 表达式和值

表达式：等于某个具体值的一坨代码。

下面都是 JavaScript 中有效的表达式:

```
7;
7 + 1; // 8
7 * 2; // 14
'Hello'; // Hello
```

一个表达式的值是可以有名字的。当你想给它起名字时，这个表达式的值会先计算，然后把计算结果赋值给某个具体的名字。为了实现这个步骤，我们用`const`关键字。`const`关键字不是唯一的途径，但将成为最常用的那个，现在开始我们会经常用到`const`:

```
const hello = 'Hello';
hello; // Hello
```

### var, let, 跟 const

JavaScript 还支持另外两种变量声明的关键字：`var` 和 `let`。个人喜欢对这三种选择进行优先级排序。通常情况下选最严格的模式`const`。一个用`const`声明的变量无法被重新赋值，且某个变量的最终值必须在变量声明时赋值。听上去有点死板，但这种约束其实是好事儿。这意味着：给这个变量名赋值以后不能再改啦。而且不需要通读整个函数或者通读作用域，一眼就能知道这个变量是干嘛的。

有时候我们确实需要重新对变量赋值。比如，如果你用的是手动的、命令式的迭代而不是用函数，这种情况下可以用 `let` 关键字对计数器 (counter) 进行迭代。

`var` 没办法直观地让人知道这个变量是干嘛的，所以`var`起不到很好的 signal 作用。自从开始用 ES6，我在项目里就再也没刻意用过`var`来声明变量。

注意哦：一旦变量是用`let`或者`const`声明的，任何尝试再次声明的企图都会报错。如果你是在 REPL (Read, Eval, Print Loop 读取-求值-输出) 环境中，希望有更多的自由度，可能用`var`来声明变量会更合适。`var`支持重复声明变量。

此文通篇都在用`const`，目的是为了帮你习惯在真实环境中默认用`const`，但这并不意味着不能用`var`，交互式环境中还是可以用下`var`的。

##  数据类型

截至目前我们看到过两类数据类型：数字和字符串。JavaScript 还有布尔值、数组、对象等等其他数据类型。晚点我们会讲。

数组是一组有序的值的组合。可以把数组视作一个装东西的盒子，比如：

```
[1, 2, 3];
```

当然，可以给👆起名字：

```
const arr = [1, 2, 3];
```

JavaScript 中的对象是键值对(value pairs)的组合，比如：
```
{
  key: 'value'
}
```

没错，对象也可以有名字：
```
const foo = {
  bar: 'bar'
}
```

如果你想用已经声明且赋值的变量及它的值作为某个对象键值对的键和值，有快捷方式啦。直接输入变量名在大括弧里就行。

```
const a = 'a';
const oldA = { a: a }; // 太长、太冗余
const oA = { a }; // 短而美对不对
```

因为实在是太好玩了，咱们再试一次：

```
const b = 'b';
const oB = { b };
```

而且对象可以很容易地进行组合，变成新对象。比如：
```
const c = {...oA, ...oB}; // { a: 'a', b: 'b' }
```

那些点点点是展开操作符(spread operator)。把`oA`的属性过一遍并复制给新对象，然后是`oB`，遇到新对象里已经存在的 keys 会直接覆盖原有值。这种展开对象的写法还比较新，还在实验性阶段，可能并不是所有的浏览器都支持。如果你的遇到浏览器无法解析时，可以用替代方法`Object.assign()`:

```
const d = Object.assign({}, oA, oB); // { a: 'a', b: 'b' }
```

上例中也就只多了一点需要额外输的内容，但，当需要组合很多个对象的时候，这种写法真的帮咱省了很多时间。注意，在用`Object.assign()`的时候，第一个参数得是最终想要拿到的对象，各种属性都会复制到这个最终的对象上。如果不幸忘记了，第一个参数直接没给，那么物理位置上第一个参数将会被修改。

我自己的经验是如果修改一个现有的对象，而不是新建一个对象，常常会导致 bug。至少，很容易出问题，所以用`Object.assign()`的时候一定要小心。

## 解构 Destructuring

对象和数组都支持解构，这也就意味着：可以从对象/数组中提取值，然后把它们赋值给已声明的变量：

```
const [t, u] = ['a', 'b'];
t; // 'a'
u; // 'b'
const blep = {
  blop: 'blop'
};

// The following is equivalent to:
// const blop = blep.blop;
const { blop } = blep;
blop; // 'blop'
```

就像上述数组的例子，我们可以一次性做很多赋值的动作。下面这行代码你会在很多 Redux 项目里看到：

```
const { type, payload } = action;
```

下面演示的是解构赋值如何用于 reducer （后面还会详细讲）：

```
const myReducer = (state = {}, action = {}) => {
  const { type, payload } = action;
  switch (type) {
    case 'FOO': return Object.assign({}, state, payload);
    default: return state;
  }
};
```

当然，如果你想用不同的名字的话，可以自己再取一个的：

```
const { blop: bloop } = blep;
bloop; // 'blop'
```
读作: 给`bloop`赋值`blep.blop`

## 比较关系 & 三元运算

我们可以用严格相等运算符进行值的比较（三个等号）：

```
3 + 1 === 4; // true
```

还有非严格模式下的相等（俩等号），通常称作相等运算符。非正式情况下，用俩等号。有时候确实可以用双等号，但是，最好默认只用严格相等（仨等号）。

其他比较运算符还包括：

- `>` 大于
- < 小于
- `>=` 大于等于
-  `<=` 小于等于
- != 不等于
- !== 不严格等于
- && 逻辑与
- || 逻辑或


三元表达式用于先进行判断，而后根据 Y/N 给出不同值：
```
14 - 7 === 7 ? 'Yep!' : 'Nope.'; // Yep!
```

## 函数 Functions

JavaScript 支持给函数表达式赋名：
```
const double = x => x * 2;
```
上式等同于数学函数`f(x) = 2x`，如果念出来的话就是"f(x)等于2x"，这个函数只有当带入某个具体 x 的值时才有意义。套用函数的话，可以直接写`f(2)`，也就是`4`。

换言之，`f(2)=4`。咱可以把数学函数当做是从输入值到输出值的映射。在`f(x)=2x`函数里，`f(x)`就是在把输入值`x`映射为相应的倍数。

JavaScript 中，一个函数表达式的值就是这个函数本身：


```
double; // [Function: double]
```

我们可以用`.toString()`方法来查阅函数的定义：
```
double.toString(); // 'x => x * 2'
```

如果你想某个函数应用于某些值身上，可以通过函数调用来实现。给函数传参进行调用，然后可以拿到一个返回值。

我们可以用`<functionName>(argument1, argument2, ...rest).`来实现函数调用。比如，当我们想要调用那个翻倍的函数时，直接在括号内输入传入值即可：
```
double(2); // 4
```

和其他的函数式编程语言不同的是，JavaScript 的函数调用必须带括号。没括号的话会报错。
```
double 4; // SyntaxError: Unexpected number
```

## 函数签名 Signatures 

函数签名(函数的关键信息)包含：

1. 函数的名字：这个是可选的，没有专属的名字也可以；
2. 一系列参数类型，用括号括起来。参数是否有自己的名字也是可选项；
3. 返回值的类型。

JavaScript 不要求类型签名必须具体化。JavaScript 的引擎会在运行时分析出类型。当你提供的信息足够多，一些类似 IDEs (集成开发环境) 和 [Tern.js][4] （Tern.js用的是数据流分析）的开发者工具也会自行推断出类型信息。

JavaScript 缺自己的函数签名标识，所以会有一些标准在尝试解决这个问题：JSDoc 之前很火，但是有点啰嗦，且没人及时去更新代码，很多 JavaScript 开发者已经不用了。

TypeScript 和 Flow 是现在比较火的，但我用着不得(dei)劲儿。我用的是 [Rtype][5]，处理有关文档的信息时用一下。有人退而求次用 Haskell 的 curry-only [Hindley–Milner 类型][7]。我倒是希望未来会出来一个针对 JavaScript 的标准化函数签名标识系统，针对性地解决文档相关问题，现在的解决方案都很一般。现阶段的话，暂时先观望吧，实时关注那些和你现在用的有点不一样、哪怕看起来有异类的类型签名就行。

```
functionName(param1: Type, param2: Type) => Type
```

double函数的签名可以表示如下：
```
double(x: n) => Number
```

尽管 JavaScript 并不强制要求标识函数签名，但理解什么是签名以及能看懂签名啥意思很有用，可以帮助大家理解相关的函数应该怎么用，以及这些函数是如何构建的。很多可以复用的函数工具都要求传入具有相同类型签名的函数。

## 默认参数值 Default Parameter Values

JavaScript 支持默认参数。下面的这个函数的工作原理很像恒等函数（返回值等于传入值的函数）。传入`undefined`或者不提供参数时返回值为零，其他情况下返回传入值：

```
const orZero = (n = 0) => n;
```

想要设置默认值，简单地在传参的时候写成`参数等于默认值`即可，就像上例中的`n=0`一样。当咱们用这种方式设置默认值的时候，类似 Tern.js、Flow、或者 TypeScript 这样的类型推断工具会自动推测函数的类型签名，哪怕你没清楚地标识出类型相关的细节。

于是乎，当你的编辑器或者 IDE 安装了合适的插件时，你是可以在敲调用函数的代码时看到相应滴函数签名的。然后一眼就能明白基于这些签名该怎么正确调用函数了。当需要用到参数默认值的时候，合理正确地使用（默认值）可以让你的代码更清晰易懂（self-documenting code）。

> 注意：有设置默认值的形参并不会算作函数的`.length`属性中(译注：ES6中如果给某个参数设置了默认值，函数的 length 将返回第一个具有默认值的参数之前的参数个数，即 length 属性会失真)，这就可能会造成依赖函数的`.length`属性的自动柯里化（autocurry）的工具（utilities）没法用。好在有的工具设计者们（比如 lodash/curry）提前想到了这种问题，让大家可以通过[自定义实参数量][10]（custom arity）来解决。


## 具名实参 Named Arguments

JavaScript 的函数可以将对象的字面量作为实参，并在形参签名中用解构赋值来实现传参。注意，你也可以通过给形参设置默认值来实现：

```
const createUser = ({
  name = 'Anonymous',
  avatarThumbnail = '/avatars/anonymous.png'
}) => ({
  name,
  avatarThumbnail
});
const george = createUser({
  name: 'George',
  avatarThumbnail: 'avatars/shades-emoji.png'
});
george;
/*
{
  name: 'George',
  avatarThumbnail: 'avatars/shades-emoji.png'
}
*/
```

## ES6 的剩余运算符（Rest）和扩展运算符（Spread）

JavaScript 中一个常见的特性是在函数签名中使用剩余运算符（the rest operator）把那些剩余实参汇总地表达出来：`...`

举个例子，下面的这个“砍头”函数直接剔除第一个参数，返回剩余内容构成的数组：

```
const aTail = (head, ...tail) => tail;
aTail(1, 2, 3); // [2, 3]
```

`Rest`运算符把单个的元素组合起来变成一个数组；而`spread`运算符恰好相反：`spread`用于把数组内的各个元素拆分为个体。比如：

```
const shiftToLast = (head, ...tail) => [...tail, head];
shiftToLast(1, 2, 3); // [2, 3, 1]
```

在使用`spread`运算符时，JavaScript 中会有一个迭代器被激活。对数组中的每个元素来说，迭代器都会给出一个值。在`[...tail, head]`表达式中，迭代器从后往前复制文字记号（literal notation）值来组建成一个新的对象。由于该例中`head`已经是一个单一元素了，我们直接把首位元素安插在数组末端即可。

## 柯里化 Currying

一个柯里化的函数是指对于多个形参，每次只传一个参数：传一个参数，返回一个函数，然后继续传下一个参数，再返回一个新函数，以此类推，直到所有的参数传递完毕，该过程结束，最后拿到返回值。

将函数的返回值设计为函数形式可以实现柯里化和偏函数应用（partial application）。


```
const highpass = cutoff => n => n >= cutoff;
const gt4 = highpass(4); // highpass() returns a new function
```

（要是不会）你也可以不用箭头函数来写。JavaScript 支持用`function`关键字。我们用箭头函数的仅仅是为了少打字而已。上面的写法等同于下面的代码：
```
const highpass = function highpass(cutoff) {
  return function (n) {
    return n >= cutoff;
  };
};
```

JavaScript 代码中，箭头差不多可以等同于`function`关键字来理解。但是也存在一些关键性的差异，取决于你用在什么类型的函数中，（用箭头的表达方式是没有`this`关键字的，也就是说：箭头函数不会用于构造函数），晚点我们遇到的时候会详细讲。就现在而言，看到`x => x`的时候理解为这是一个以`x`为参数且返回值为`x`的函数就好。所以呢，`const highpass = cutoff => n => n >= cutoff`可以理解为：

给一个名为`highpass`的函数传入`cutoff`，返回一个新函数，这个新函数传入`n` 返回`n>=cutoff`的值。

由于`highpass()`返回的是一个函数，我们可以用它来造更具体点的函数：

```
const gt4 = highpass(4);
gt4(6); // true
gt4(3); // false
```

自动柯里化（Autocurry）可以自动让你的函数变得柯里化，敲灵活。比如你有一个函数 `add3()`：

```
const add3 = curry((a, b, c) => a + b + c);
```

有了自动柯里化，你可以各种玩，返回啥取决于你传了多少实参：
```
add3(1, 2, 3); // 6
add3(1, 2)(3); // 6
add3(1)(2, 3); // 6
add3(1)(2)(3); // 6
```

对 Haskell 的粉丝来说，不好意思了，JavaScript 没有内置的自动柯里化机制，不过你可以从 Lodash 里 import 一个:

```
$ npm install --save lodash
```

然后，在你的加载模块里:

```
import curry from 'lodash/curry';
```

或者，可以用下面这段神奇的有魔法的代码，芭拉芭拉变~
```
// Tiny, recursive autocurry
const curry = (
  f, arr = []
) => (...args) => (
  a => a.length === f.length ?
    f(...a) :
    curry(f, a)
)([...arr, ...args]);
```

## 函数复合

当然，你还可以复合函数。函数复合是指把一个函数的返回值作为参数传给另一个函数的过程。数学表达式是：`f . g`。

在 JavaScript 里体现为：`f(g(x))`

从里往外算哈：
- 计算 x
- 把 x 传给函数`g()`
- 把`g()`返回值传给函数`f()`

比如：
```
const inc = n => n + 1;
inc(double(2)); // 5
```

数字`2`传入给函数`double()`, 拿到返回值`4`以后传给函数`inc()`,最后拿到返回值`5`。

任意形式的表达式都可以作为实参传给一个函数，应用函数之前会首先计算传入的参数值。

```
inc(double(2) * double(2)); // 17
```
因为`double(2)`的值为`4`，所以`inc(4*4)`本质上就是在计算`inc(16)`，结果为`17`。

函数复合是函数式编程的核心概念，晚点我们会详细讲。


## Arrays

数组有很多内置方法。方法就是跟一个对象相关的函数：通常情况下是这个对象的属性，比如：
```
const arr = [1, 2, 3];
arr.map(double); // [2, 4, 6]
```
上栗中，`arr`是一个对象。`.map()`就是这个对象的属性，通过一个函数拿到一个返回值。调用`map()`函数的时候，参数带入，函数计算，拿到结果。当`map`方法被触发、开始调用函数的时候后台就已经自动设置好的参数带入的动作，`this`值帮助`.map()`拿到数组`arr`里的值。

注意我们是在把`double`函数作为值传给map方法，而不是调用`double`函数。因为上例中我们是在把一个函数作为参数传给`map`并将参数内的每个元素逐一带入，最后的结果就是这些元素逐一带入`double()`函数后拿到的值构成的数组。

注意，这个过程中原数组`arr`的值不变：
```
arr; // [1, 2, 3]
```

## 方法链/链式编程 Method Chaining

咱们还可以把方法链起来用。链式编程是在一个函数的返回值的基础上直接调用一个方法，而不再通过引用命名的形式实现：

```
const arr = [1, 2, 3];
arr.map(double).map(double); // [4, 8, 12]
```

布尔值函数 (predicate) 是指返回值为布尔值（true or false）的函数。给`.filter()`函数传入一个返回值是布尔值的函数，然后得到一个新的数组，只筛选/选取那些布尔值函数返回值为`true`的情况。

```
[2, 4, 6].filter(gt4); // [4, 6]
```

往往，我们需要先从一个数组里筛选元素，然后逐一映射到新数组：

```
[2, 4, 6].filter(gt4).map(double); [8, 12]
```

注意，这个系列后面的文章还会提供一种更有效的方式 (transducer) 来实现同步进行上述的两个动作。不过要先来点儿铺垫（心急吃不了热豆腐哈~）。

## 总结 Conclusion

如果你看完觉得有点懵逼，没关系，别急。我们暂时还只是讲了点皮毛，凡是有价值的东西都需要深挖和探索的。放心，我们会反复提到这些内容帮助大家复习以及深入的。


[下节更精彩][8]


[0]: https://leanpub.com/composingsoftware
[1]: https://medium.com/javascript-scene/higher-order-functions-composing-software-5365cf2cbe99
[2]: https://codepen.io/
[3]: https://babeljs.io/repl/
[4]: http://ternjs.net/
[5]: https://github.com/ericelliott/rtype
[7]: http://web.cs.wpi.edu/~cs4536/c12/milner-damas_principal_types.pdf
[8]: https://medium.com/javascript-scene/higher-order-functions-composing-software-5365cf2cbe99
[10]: https://lodash.com/docs/4.17.11#curry
