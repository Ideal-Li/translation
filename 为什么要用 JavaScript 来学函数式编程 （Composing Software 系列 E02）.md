

【作者】：Eric Elliott

【出处】：Medium

【原文链接】：https://medium.com/javascript-scene/why-learn-functional-programming-in-javascript-composing-software-ea13afc7a257

注意：此文是Composing Software系列专题文章([现在已经出书啦][15])，该系列旨在帮助大家从零开始用 JavaScript ES6+ 学习函数式编程和基于组件的软件开发技术（compositional software techniques）。敬请关注，精彩不断。

下一节的链接[戳这里][1]

---

把你所知道的 JavaScript 暂时都搁一遍，把自己当小白重新开始学。为了一切从零开始，我们会从 JavaScript 最基本的概念开始讲。如果你刚好真的就是小白，那再好不过。让我们一起在 ES6 和函数式编程的知识的海洋里划船吧，从头开始。希望我们可以把所有的新概念都一起捋一遍，但可别指望是保姆式教学......

如果你是只老（程序）猿，在这行已混迹多年，而且对 JavaScript 或者某一种函数式编程语言已经门儿清，也许你会觉得用 JavaScript 来学函数式编程有点搞笑... 咱能不能先把这些想法（各种偏见或质疑）撂一边，用开放式的心态（open mind）去尝试接触下面的内容，你会发现 JavaScript 编程还可以修炼到另外一个段位，一种你不曾了解的境界（真！的！）。

这个系列名为 Composing Software，而函数式编程显然是首选方案来 Compose （用复合函数、高阶函数等等）。你可能会想，为什么不用 Haskell, ClojureScript 或者 Elm, 偏偏选 JavaScript？

因为 JavaScript 有函数式编程所需的最关键的几大特质：

1. 一等公民：JavaScript 支持把函数当做值来对待。即：函数可以被当做值来传参，也可以作为返回值，还可以把函数赋值给变量或者成为对象的属性。这样就可以用高阶函数啦，于是，偏函数应用（partial application）、柯里化（currying）和复合函数（composition）也就都可以用啦。

2. 匿名函数和精确的λ句法：`x => x * 2`在 JavaScript 中是合法的表达方式，这种表现形式可以让人在使用高阶函数的时候不那么痛苦哇。

3. 闭包：闭包是指把一个函数和它所在的作用域进行打包。创建函数之时，闭包形成。当一个函数在另一函数的内部被定义时，外部函数中的变量对内部函数都是可用的，哪怕是在外部函数创建完之后。通过闭包可以实现偏函数应用中的参数固定。固定参数（fixed argument）是指返回的那个函数的闭包作用域中的参数。比如，在`add2(1)(2)`这个函数里，1 就是`add2(1)`返回的那个函数中的固定参数。

## JavaScript 有什么不足？

JavaScript 是一个多范式语言，这也就意味着它支持很多不同的编程风格。JavaScript 支持的其他风格包括： 像 C 语言一样的命令式编程(在命令式编程中，函数作为命令的子程序存在，可以反复被调用)；面向对象的编程（在这种编程风格中，对象是最基本的单元，而非函数是最基本的单元）；当然还有函数式编程。这种支持多范式的语言的缺点在于：命令式编程和面向对象的编程中，所有东西或许都是可变的 (mutable)。。。

异变（Mutation) 是在适时的时候发生的数据结构的改变。比如：

```
const foo = {
  bar: 'baz'
};
foo.bar = 'qux'; // mutation
```

对象得是可变的，这样才能调用方法改变其属性。在命令式编程范式中，大部分的数据结构都是可变的，以便及时有效地处理对象和数组。

下面这些特性是其他函数式编程语言具备而 JavaScript 欠缺的：

1. 够纯（purity）：很多函数式编程语言中，`纯`是必须的，副作用（side-effects）是不合法的。
2. 不可变性 (Immutability)：很多函数式编程语言禁止异动 （mutations)。这些语言不会去修改已创建的数据结构（比如数组或对象），而是直接新建。听上去好像太低效了，但大部分的函数式编程语言会用树形结构 (trie data structures)，最大的特点是结构共享(structural sharing)，即原有对象和新创建的对象会指向相同的值（share references）。
3. 递归：递归是指函数可以引用自己进行迭代。在很多函数式编程语言中，递归是唯一的迭代途径。而并不依赖类似 for, while 之类的循环语句。


**纯度的问题**：在 JavaScript 中，纯度这件事情必须要约定才能实现。如果你没在用纯函数写大部分的应用，你就不是在用函数式的编程风格。很难受的地方在于，用 JavaScript 编程时，真的很容易一不小心就写了个不纯的函数。

**不可变性**：在很多纯函数编程语言中，不可变性 (immutability) 是强制的。JavaScript 没有这种大部分其他函数式编程语言在用的高效的、禁止异动的、基于树形(trie-based)的数据结构。好在有些库可以弥补这方面的不足，比如 [Immutable.js][2] 和 [Mori][3]。我个人非常希望后面 ECMAScript 考虑新增一些特性来禁止数据结构的异动。

有迹象显示 ECMAScript 已经开始这么干了，比如 ES6 中增加了关键字`const`。一个用`const`定义的变量不可以再被赋其他的值。`const`本身并不意味着变量的值不可以改动（而是变量指向的那个内存地址不得改动）。

一个用 `const` 定义的对象没办法在重新赋值指向一个不同地址值，但是指向的这个对象自身的属性是可以改变的。JavaScript 可以实现 `freeze()` 对象，但只能在根层级 (root level) 实现，这也就意味着套嵌对象 (nested object) 可以具备属性可变的属性。换言之，JavaScript 想要实现真正的不可变性还有很长的路要走。

**递归**: JavaScript 技术上支持递归，但大部分函数式编程语言都支持尾调优化（tail call optimization），允许归递函数(recursive functions)反复使用栈框架（stack frames）来实现递归调用。

如果没有尾调，一个调用栈 (a call stack) 会面对持续的负荷增加，直到出现栈溢出(stack overflow)。

在 ES6 中，JavaScript 的尾调优化形式本来就非常有限，外加只有一款浏览器引擎支持这个特性，而且还是部分支持，然后还从 Babel 里剔除了。。。（Babel 是一款最受欢迎的解析器，用于把 ES6 编译成 ES5，方便喜欢用较为传统的浏览器的用户）。

**强调**：目前为止，在大规模的迭代中用递归还是不建议，哪怕在尾部调用函数的时候真的得很小心。


## 有什么是 JavaScript 有而其他纯函数式编程语言没有的

一个坚持用纯函数的极客会坚持认为 JavaScript 的可变性是最大的弊端，确实如此。但是，有时候副作用和可变性会成为优点。事实上，大部分现在受欢迎的应用都巧妙地利用了副作用的特性。像 Haskell 一样的纯函数式编程语言利用副作用的时候会把它们用monads装起来，伪装成纯函数的样子，保持程序的纯度，哪怕 monads 本身代表着副作用、本身就是不纯的。


`Monads` 的问题在于，尽管用起来很简单，对一个不是很熟悉大量实例的人来说，解释`Monads` 是啥有点像给一个盲人解释什么是蓝色...

> “A monad is a monoid in the category of endofunctors, what’s the problem?” 这句话出自 James Iry ，引用的是 Saunders Mac Lane 所写的[《A Brief, Incomplete, and Mostly Wrong History of Programming Languages》][4]里的话，一直被误认为是在引用 Philip Wadler。

这种引用再引用之后的话语会让搞笑的事情变得更搞笑（也会让复杂的事情变得更复杂。。。），上面引用的那句话中，对 monads 的解释其实对原句做了极大的简化，原句长这样：

> “A monad in X is just a monoid in the category of endofunctors of X, with product × replaced by composition of endofunctors and unit set by the identity endofunctor." 出自 Saunders Mac Lane 所写[《Categories for the Working Mathematician》][5]

即便感觉像在看天书，我也觉得没必要畏惧`monads`。了解 monads 最好的方式不是去看一堆书然后发朋友圈分享或者吐槽啥的，直接上手开始用。就像函数式编程所涉及的很多内容一样，那些晦涩的术语老难了，相信我，你不需要理解 Saunders Mac Lane 说的那句话也能学好函数式编程。


虽然 JavaScript 不是那么绝对地完美到适合每种编程风格，但毋庸置疑，它是最具通用性的，甭管您之前是用什么语言，习惯哪种编程风格的，都能上道。

就像 [Brendan Eich][6] 所说， JavaScript 从娘胎里出来的时候就具备这种气质。（当时）网景公司(Netscape) 不得不同时支持两个派系的程序员：


> "... 一类是用组件的工程师们，他们习惯用 C++/JAVA；还一类就是写脚本的，业余选手或者行家，他们直接在 HTML 里写代码。"

一开始的时候，网景公司只是想设计成支持两种不同语言的东西，这种脚本语言可能和 Scheme 类似 (Scheme是Lisp的衍生品来的)。Brendan Eich 自己都说：

> 我去网景的时候还给我承诺过，浏览器都用 Scheme 

JavaScript 必须再立门户。

> 上级管理部门的命令是得造出来一个看上去和 Java 很像的语言，这也就是说，Perl, Python, Tcl, 还有 Scheme 都不符合要求。

所以，Brendan Eich 的老板最初的理念就是：

1. 整一个浏览器里的 Scheme
2. 跟 Java 很像

结果就是，比大杂烩还大杂烩。

> 我并不以此为荣，但我很高兴自己选的是类似 Scheme 的一等函数设计，以及以非常私有化的原型为基础（尽管单一）。万一像 Java 那样影响`千年虫`以及`原型 VS 对象`的区分的问题那就惨了。

JavaScript 还有很多糟糕的类似 Java 一样的特性：

 - 构造函数和 new 关键字，还有工厂函数的不同的调用和使用方式
 - class 关键字， 出现从单一 ancestor 逐层拓展的层级制度
 - 用户可能会把类当做是静态类型（其实并不是）

我的建议：以上能避免的赶紧避免。

好在 JavaScript 最终发展成为现在的样子，脚本方面优于很多其他竞争对手（如今Java, Flash, 和 ActiveX 的扩展在很多浏览器里都已经不支持了）

最终，只有一种语言直接得到浏览器的支持：JavaScript。

这也就意味着浏览器可以不再那么体积庞大，bugs 也少了，因为只用考虑支持一种语言了呀：JavaScript。你也许会想 WebAssembly 不就是个例外吗？但，WebAssembly 的设计初衷之一也是为了用具有兼容性的抽象语法树（Abstract Syntax Tree）实现共享 JavaScript 的绑扎（bindings）。事实上，第一个编译 WebAssembly 为 JavaScript 的例子就是 ASM.js.

作为唯一一个可以符合大多数需求的标准化编程语言，JavaScript 真的是赶上了时代红利，在软件开发的世界里乘风破浪。

> 各种应用软件吞嗤着这个世界，网络淹没着这些应用，而 JavaScript 贪食着整个互联网。

通过[各种][7][途径][8],[JavaScript][9]已经成为全世界最流行的编程语言。

JavaScript 并不是函数式编程最完美的编程语言，但它是构建大型应用时的首选: (JavaScript 支持) 需要大型团队且团队人员分散情况下，不同的团队可以有不同的设计理念。

一些团队负责专注于把大家写的脚本年合起来，这种情况下，命令式编程风格更好用。其他人可能专攻项目的架构，这种时候采用面向对象的思维模式也不是不行。还有一部分人专攻函数式编程，用纯函数减少用户在决策和测试管理方面的操作。如果所有这些人，都用同一种语言，这也就意味着他们之间更容易交流想法，彼此学习，而且合作更融洽、更加亲密无间。

在 JavaScript 中，所有这些想法都 ok，所以有更多人加入到 JavaScript 的生态中来，直接导致[世界上最大开源包][10]的出现，2017年2月的 npm。

JavaScript 的真正魅力在于想法的多样性和生态系统里用户的多样性。对函数式编程的脑残粉来说 JavaScript 可能不是最佳方案，但它真的是当很多人需要用同一种语言一起做事情时候的首选，你能想到的各种平台都支持，相信那些从其他语言转来 JavaScript 的人都明白我在说啥，不管之前是写 Java，Lisp 还是 C。他们一开始用的时候肯定有点不习惯，但上手以后超级爽，而且效率老高了。

我同意这种看法：JavaScript不是最好的函数式编程语言。但是，没有其他函数式编程语言敢说自己可以实现 ES6 那样的包容性：JavaScript 可以实现，而且可以让对函数式编程感兴趣的用户有更好的使用体验。与其嫌弃几乎所有公司都在用的 JavaScript 和它强大的生态系统，还不如选择用着试试，然后让它在你的软件开发世界里发光发热。

事实是：JavaScript 已经是一个足够好用的函数式编程语言了，大家已经在用 JavaScript 函数式编程它开发各种好用、且有意思的东西了。Netflix 以及各种用 Angular 2+ 开发的 app 都在用基于 RxJS 的函数实用程序(functional utilities)。脸书已经在用纯函数、高阶函数、和 React 里面的高阶组件来维护 Facebook 和 Instagram 的站点。[PayPal, KhanAcademy 和 Flipkart 都在用 Redux][13] 进行状态管理（state management）。

还有呢：Angular, React, Redux, 和 Lodash 是 JavaScript 应用生态系统中一些名气比较大的框架和库，他们都在很大程度上依赖函数式编程；Lodash 和 Redux 甚至就是为了实现在真实 JavaScript 应用中使用函数式编程而问世。


“为什么选 JavaScript？”因为JavaScript是大部分公司用来开发软件时所用的语言。不论你喜欢还是不喜欢，“最受欢迎的函数式编程语言”之名哪怕曾经被颁给 Lisp 几十年，现在给 JavaScript 抢过来了。确实，从函数式编程的概念来看，Haskell 可能是最适合当领头羊(standard bearer)，但事实就是很少有人在现实生活中用 Haskell。

在美国，任何时候都有成千上万个和 JavaScript 相关的工作机会，全世界范围的话，那就多了去了。学 Haskell 的话会让你了解很多函数式编程的东西，但学 JavaScript 更实用，更有助于现学现用（还等什么，赶紧行动起来吧）。


> 各种应用软件吞嗤着这个世界，网络淹没着这些应用，而 JavaScript 贪食着整个互联网。


[【下集更精彩】][14]

[1]: https://medium.com/javascript-scene/a-functional-programmers-introduction-to-javascript-composing-software-d670d14ede30

[2]: https://facebook.github.io/immutable-js/
[3]: https://github.com/swannodette/mori
[4]: https://james-iry.blogspot.com/2009/05/brief-incomplete-and-mostly-wrong.html
[5]: https://www.amazon.com/Categories-Working-Mathematician-Graduate-Mathematics/dp/0387984038//ref=as_li_ss_tl?ie=UTF8&linkCode=ll1&tag=eejs-20&linkId=de6f23899da4b5892f562413173be4f0
[6]: https://brendaneich.com/2008/04/popularity/
[7]: https://redmonk.com/sogrady/2016/07/20/language-rankings-6-16/
[8]: https://insights.stackoverflow.com/survey/2016
[9]: https://octoverse.github.com/
[10]: http://www.modulecounts.com/
[11]: https://www.npmjs.com/
[13]: https://github.com/reduxjs/redux/issues/310
[14]: https://medium.com/javascript-scene/a-functional-programmers-introduction-to-javascript-composing-software-d670d14ede30
[15]: https://leanpub.com/composingsoftware
