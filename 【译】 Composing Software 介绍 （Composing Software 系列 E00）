

【作者】：Eric Elliott

【出处】：Medium

【原文链接】：https://medium.com/javascript-scene/composing-software-an-introduction-27b72500d6ea


> 注意：此文是`Composing Software`系列专题的介绍([现在出书了][1])，该系列旨在帮助大家从零开始用 JavaScript ES6+ 学习函数式编程和基于组件的软件开发技术（compositional software techniques）。敬请关注，精彩不断。


下一节的链接[戳这里][2]

> 词典里对`组合/复合`(composition)的解释如下: 把必要的元素/部分合并成一个整体的行为。

读高中的时候，编程老师在第一次课上就说过：软件开发不过就是把一个复杂的问题先分解为若干个小问题，然后把这些小问题的解决方案拼凑起来，原本复杂的问题就这样愉快地解决啦。

人生诸多后悔的事情之一就是当时没好好重视这句话，过了好久好久才领悟到软件设计的本质。(啊...多么痛的领悟~)

面过上百个开发者以后发现，其实我并不孤独。几乎没几个在职的工程师真正掌握了软件开发的本质，他们压根没意识到超赞的工具远在天边近在眼前，或者根本不知道该怎么用好。所有软件开发从业者都曾或多或少困惑过这个领域（软件开发）中最最重要的问题：

- 到底什么是函数组合/复合（function composition）？
- 以及，什么是对象组合/复合（object composition）？


问题在于你不可能因为搞不懂就可以不管它(composition)，抬头不见低头见，早晚都要见。也可以假装它不存在，不就是多点 bugs 吗，不就是写的代码遭人唾弃吗。这种大事，随性的代价太大了: 维护代码的时间比从 0 到 1 的开发阶段用的时间还多，而且这些 bugs 会影响到全球数十亿用户的体验。


现在全世界哪哪的生活都离不开软件。每台车就是驾驶在轮子上的一台迷你超级计算机，软件设计出问题真的就会直接引发交通事故，甚至人员伤亡。2013年，丰田汽车出事，代码（Spaghetti code）里尼玛上万个全局变量，陪审团调查后，最终把[事故][3]咎于软件开发团队的鲁莽、失职。

世界各地的黑客和各国政府都在各种[攒bugs][4](stockpile bugs)，用来监视公众、盗取信用卡、利用计算资源（computing resources）进行阻断服务攻击(DDoS attack)、破解密码、甚至对选举进行暗箱操作等。

我们（开发者们）必须更给力（We must do better）。


### 你每天都在 Compose Software

如果你是一个软件工程师，不论你是否意识到这件事，其实你每天都在组合函数、组合数据结构。当然，你是可以有意识地去组合(函数和数据结构)且做得更好，或者你就是不小心用到的，只不过用的效果有点像是在瞎用封箱胶带或者502（方言解释为“用得蛮挫”）。

软件发展的过程其实就是把大问题分解为若干个小问题，构建组件(components)来逐个解决这些小问题，然后把所有的组件拼凑在一起构成一个完整的应用。

### 复合函数 Composing Functions

复合函数其实就是用一个函数输出另一个函数的过程。在`代数`里，给定两个函数 `f` 和 `g`，数学表达`(f ∘ g)(x) = f(g(x))` 中的 `∘ ` 代表组合运算符，读做`与 xxx 组合` 或者 `after`。比如上式可读作`函数 f 和 g 组合等于 f(g((x)) `或者`f after g = f(g(x))`。我们读作`f after g`是因为 g(x) 的值会首先计算，所得值作为参数传给函数f(x)。

每当代码长这样时，你就是在用复合函数(compose functions)啦：
```
const g = n => n + 1;
const f = n => n * 2;
const doStuff = x => {
  const afterG = g(x);
  const afterF = f(afterG);
  return afterF;
};
doStuff(20); // 42
```

每次你写 `promise` 链的时候，也是在用复合函数(compose functions)

```
const g = n => n + 1;
const f = n => n * 2;
const wait = time => new Promise(
  (resolve, reject) => setTimeout(
    resolve,
    time
  )
);
wait(300)
  .then(() => 20)
  .then(g)
  .then(f)
  .then(value => console.log(value)) // 42
;
```
同样的，每次你把数组方法、Lodash 方法、或者诸如 RxJS 的 observables 使用组合计的时候，你就是在复合函数啦。把各种东西串起来 (chain) 用其实就是在组合。把一个（函数的）返回值传给其他的函数是组合；在一段程序里调用两个方法（用 this 作为传入值）也是在组合。


> 用`链`就是在组合 (If you’re chaining, you’re composing).

有目的性地组合函数，效果会更好。

如果我们有目的性地组合函数，上例中函数`doStuff()`的代码可以升级为（一行搞定）：
```
const g = n => n + 1;
const f = n => n * 2;
const doStuffBetter = x => f(g(x));
doStuffBetter(20); // 42
```

往往不推荐这样写代码往往是因为它很难 debug。那么，我们该如何巧用复合函数来实现呢？

```
const doStuff = x => {
  const afterG = g(x);
  console.log(`after g: ${ afterG }`);
  const afterF = f(afterG);
  console.log(`after f: ${ afterF }`);
  return afterF;
};
doStuff(20); // =>
/*
"after g: 21"
"after f: 42"
*/
```


首先，我们先简化一下 “after f”, “after g”，把它们升级为复用性更强的`trace()`:

```
const trace = label => value => {
  console.log(`${ label }: ${ value }`);
  return value;
};
```
然后，代码变这样：
```
const doStuff = x => {
  const afterG = g(x);
  trace('after g')(afterG);
  const afterF = f(afterG);
  trace('after f')(afterF);
  return afterF;
};
doStuff(20); // =>
/*
"after g: 21"
"after f: 42"
*/
```

一些比较流行的函数式编程的库，比如 Lodash 和 Ramda 包含很多这样设计好了的可以直接拿来就用的简化过程，大大简化了函数复合。于是乎，上面的函数还可以长这样：

```
import pipe from 'lodash/fp/flow';
const doStuffBetter = pipe(
  g,
  trace('after g'),
  f,
  trace('after f')
);
doStuffBetter(20); // =>
/*
"after g: 21"
"after f: 42"
*/
```

如果你不想 import 任何工具，可以自己定义 pipe 函数
```

// pipe(...fns: [...Function]) => x => y
const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x);
```

要是没看懂，别急。晚点我们会更仔细地探讨复合函数。事实上，复合函数的概念太重要了，我们会反复提及，且，会重复很多次，帮助你熟悉然然后用得更6666666。做一个用复合函数的程序猿吧。


`pipe()`创建了一连串的函数，把一个函数的输出值作为输入值传给下一个函数。当你使用 `pipe()` (以及它的好朋友 `compose()`函数)时，压根不需要额外增加变量在中间搭桥、帮助传参。不涉及参数的函数我们称之为无参风格（point-free style）。为了实现函数无参，我们调用一个返回值为函数的函数，而不是直接声明一个函数（然后还给它取个名字），这也就意味着代码里无需出现`function`关键字，无需箭头函数的箭头。

无参这个话题可以扯很远，这里只讲一点点，后面再详细说。那些(在函数间)帮助传参的变量(们)会无谓地增加函数的复杂度。

(为什么要考虑函数复杂度的问题？)降低函数的复杂程度有很多好处：


### 工作内存 [Working Memory][8]

普通人的大脑中只有很小部分的空间可以用来处理离散的(不怎么相关的)数据信息（discrete quanta），每个变量都会潜在地消耗脑容量。每增加一个变量，回忆起变量名字的能力就会下降。通常人脑可以处理4-7个单元的离散信息。超过这个上限，犯错率就会明显增加。

通过刚才的函数串联模式，我们减少了三个变量命名的需求，释放了几乎一半的脑容量上限来处理其他的事情，大大地减缓了大脑的认知负荷。软件工程师貌似比普通人更擅长把数据分块处理，而且还不影响数据的实质性内容。

### 信号噪声比 Signal to Noise Ratio

更精简的代码还会增加代码的型号噪声比。这就好比收听广播节目，当调频不够精准时，会听到很多"滋滋"响的白噪音，非常刺耳。调到精准的频段以后，噪声立马消失，连耳朵都觉得听歌更愉悦了。

代码也是如此。更简洁的代码可以帮助实现更精准的理解。有的代码是关键信息，而有的代码是在浪费彼此时间。如果一段代码删掉以后不影响读者对代码的理解，删吧。读者会更感激你。



### 八阿哥的表面积  Surface Area for Bugs

看看改造前和改造后的代码。是不是很像这个函数努力瘦身且减肥成功了。给代码塑身这件事情很重要，因为更大的表面积意味着更多的地方可以藏 bug，这也就意味着更多的 bugs。

> 更少的代码量 = 更少地方藏 bugs = 更少的 bugs 

## 组合对象 Composing Objects

> 四人帮(The Gang of Four)的书[“模式设计：软件开发中可复用的对象元素”][6]提到："对象组合比类继承好用"(“Favor object composition over class inheritance” )


> 维基百科：“计算机科学里，组合数据类型（composite data type）或者说复合数据类型（compound data type）是指可以通过该语言的原始数据类型或者其他组合类型来构建的任意数据类型[...]。构建该复合类型的动作就叫做`composition`”

这些就是原始数据类型：
```
const firstName = 'Claude';
const lastName = 'Debussy';
```
而下面的是组合数据类型：
```
const fullName = {
  firstName,
  lastName
};
```

同样的，所有Arrays, Sets, Maps, WeakMaps, TypedArrays 等都是组合数据类型。 但凡构建非原始数据类型的数据结构，都是在组合对象。

注意，四人帮的书里定义了一个所谓`组合模式`的模式：一种特定类型的递归对象的组合，既可以用来应对个体组件，还能用同样的方式处理多个组件构成的整体（composite）。有的开发人员会误以为`组合模式`是对象组合（object composition）的唯一形式。不是的！还有很多复合对象的方式。

书中还说了：在设计模式里，你会看到对象组合在设计模式中反复应用。书中提及了三种对象组合的关系：delegation，acquaintance 和 aggregation（本段没有完整翻译术语部分的解释，感兴趣的同学请参考原文了解）。 

类继承可以用于构建组合对象，但是使用有局限，且容易出问题。书中所说的“对象组合比类继承好用”其实就是建议使用更灵活的方式组合对象，而不是用死板的、紧密耦合的类继承。


个人比较推崇`对象组合`在这本书里（1989年出版的[《Categorical Methods in Computer Science: With Aspects from Topology》][5]）更通俗的定义：


> 复合对象就是简单地把对象放在一起，令后者成为前者的一个部分。（“Composite objects are formed by putting objects together such that each of the latter is ‘part of’ the former.”）


还有一本不错的参考资料是 1975 年 Glenford J Myers 写的 《Reliable Software Through Composite Design》。这两本书都不重印了，不过如果你想在技术领域深入研究复合对象的话，可以在亚马逊或者 eBay 找找看。

类继承只不过是构建复合对象的一种形式。所有的类都可以创建复合对象，但是并不是所有的复合对象都是通过类或者类继承得来。“对象组合比类继承好用”的本意是建议通过小的组件构建复合对象，而不是在某个类层级结构中从一个 ancestor 继承所有属性，这种传统的方式（类继承）在面向对象的设计中会引发各种问题：


**紧密耦合的问题**：因为子类依赖父类的执行，类继承是在面向对象的设计中是最最最紧密耦合的。

**脆弱的基类问题**(fragile base class problem): 因为紧密耦合的关系，基类的变动很有可能会造成大量子类的异动(bugs 可能的藏身处之一)，而且可能会影响第三方维护的代码。作者可能甚至不知道自己在牵一发而动全身。

**呆板的层级问题**：在单一 ancestor 由上而下的类层级结构中，但凡时间足够长、迭代足够多，新环境下(for new use-cases)必出问题。

**复制问题**：因为呆板的层级制度，出现新 case 往往都是通过复制来实现，而不是拓展(extension)，这就会导致出现相似但又不同的类。结果就是：一旦需要开始复制，都不知道应该去复制谁以及为什么复制。


**（经典的）大猩猩/香蕉问题**: 引用 Joe Armstrong 的[《Coders at Work》][9] ：“...面向对象的语言存在的问题在于太不直接。你只想要一个香蕉，结果你拿到的是在一片丛林里的一只大猩猩手里拿着你想要的那根香蕉和这一整片丛林。”

JavaScript 中最常见的对象组合形式是对象链接 (object concatenation)（或者说 Mixin 组合）。 有点像做冰淇淋：手握一个香草味的冰淇淋杯，然后往里加东西 - 坚果、焦糖、巧克力酱 - 然后你就有了一个有坚果的巧克力酱冰淇淋啦。

用类继承实现复合对象：

```
class Foo {
  constructor () {
    this.a = 'a'
  }
}
class Bar extends Foo {
  constructor (options) {
    super(options);
    this.b = 'b'
  }
}
const myBar = new Bar(); // {a: 'a', b: 'b'}
```

用 mixin 实现复合 

```
const a = {
  a: 'a'
};
const b = {
  b: 'b'
};
const c = {...a, ...b}; // {a: 'a', b: 'b'}
```

晚点我们会深入讲解复合对象的其他方式。现在你需要理解的是：

1.不止一种方式可以实现；
2.有一些方式会比其他的方式更好；
3.面对问题的时候，选最简单、最灵活的解决方案

## 结论

这个专题不是在讲函数式编程和面向对象的编程的比较，也不是分析一种语言和其他语言相比的利弊。`组件`涉及更广：函数、数据结构、类等等。不同的编程语言会用到组件不同的元素。比如 Java 用类(class)，Haskell 用函数等等。但不论你喜欢哪种语言或者编程范式，你都不可避免地要用到函数组合和数据结构。最终，还是会不得不面对这些问题。

我们会涉及很多函数式编程，因为函数组合什么的最好用啦，而且函数式编程爱好者比较多，他们投入了大量的时间为整个生态做贡献，很多函数组合的技术都已日趋规范和成熟。

但我们不会说函数式编程比面向对象的编程更好，也不会说你得选其中的某一种。这两者之间二选一是一个错误的二分法。反正，现实生活中我所看到的每一个JavaScript 应用都有同时、大量地涉及到两种方式。

我们会用复合对象来创建函数式编程的数据类型，用函数式编程来为面向对象的编程创建对象。

> 不论你怎么造软件，都得学会**善用 COMPOSE.** (No matter how you write software, you should compose it well.)


> 软件开发的本质其实就是组合/复合(composition)。


一个不懂得 composition 的软件开发工程师就像是不知道啥是钉子啥是锤子的人想造房子。没有 composition 意识的软件开发简直就是在用宽胶带和502盖房子。粘吧粘吧的了嘿您咧。

**求简**。最简单的方法就是去追寻事物的本质。问题在于，貌似这个行业没几个人真正弄明白什么是精华。整个行业可能让你觉得干这行挺失落的，但现在，我们有责任去改变这个行业，从培养更优秀的工程师开始。我们必须做得更好，去承担责任。现代社会离不开软件，从经济发展到医疗设备。我们的软件开发质量影响着世界的每个角落，我们需要对自己的工作有一个更清晰的认知(以及自己的工作会造成的影响)。

是时候开始学如何 compose software 了。

[1]: https://leanpub.com/composingsoftware
[2]: https://medium.com/javascript-scene/the-rise-and-fall-and-rise-of-functional-programming-composable-software-c2d91b424c8c

[3]: http://www.safetyresearch.net/blog/articles/toyota-unintended-acceleration-and-big-bowl-%E2%80%9Cspaghetti%E2%80%9D-code
[4]: https://www.technologyreview.com/s/607875/should-the-government-keep-stockpiling-software-bugs/
[5]: https://www.amazon.com/Categorical-Methods-Computer-Science-Topology/dp/0387517227/ref=as_li_ss_tl?ie=UTF8&qid=1495077930&sr=8-3&keywords=Categorical+Methods+in+Computer+Science:+With+Aspects+from+Topology&linkCode=ll1&tag=eejs-20&linkId=095afed5272832b74357f63b41410cb7
[6]: https://www.amazon.com/Design-Patterns-Elements-Reusable-Object-Oriented/dp/0201633612/ref=as_li_ss_tl?ie=UTF8&qid=1494993475&sr=8-1&keywords=design+patterns&linkCode=ll1&tag=eejs-20&linkId=6c553f16325f3939e5abadd4ee04e8b4
[7]: https://www.amazon.com/gp/product/1430219483?ie=UTF8&camp=213733&creative=393185&creativeASIN=1430219483&linkCode=shr&tag=eejs-20&linkId=3MNWRRZU3C4Q4BDN
[8]: http://www.nature.com/neuro/journal/v17/n3/fig_tab/nn.3655_F2.html
[9]: https://www.amazon.com/gp/product/1430219483?ie=UTF8&camp=213733&creative=393185&creativeASIN=1430219483&linkCode=shr&tag=eejs-20&linkId=3MNWRRZU3C4Q4BDN
