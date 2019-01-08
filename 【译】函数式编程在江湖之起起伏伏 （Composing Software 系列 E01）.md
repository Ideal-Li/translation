
【作者】：Eric Elliott

【出处】：Medium

【原文链接】：https://medium.com/javascript-scene/the-rise-and-fall-and-rise-of-functional-programming-composable-software-c2d91b424c8c

> 注意：此文是Composing Software系列专题的第一篇，该系列旨在帮助大家从零开始用 JavaScript ES6+ 学习函数式编程和基于组件的软件开发技术（compositional software techniques）。敬请关注，精彩不断。

下一节的链接[戳这里][1]

6岁跟基友一起打游戏，他家有一间屋尼玛全是电脑。对哥来说，这种诱惑实在是太大了，简直迷幻。在哥们家花了好久把所有游戏都打了个遍。有一天我问他：这游戏咋做的？

他也不知道，我俩一起去找他爹。他爹从书架上拿出来一本教怎人用 Basic 写游戏的的书。从此哥就上道了。学校教数学的时候，哥觉着自己全会，编程基本上也都这点数学知识。信不信由你。

## 函数式编程的兴起

计算机科学还在萌芽阶段，也就是电脑变得流行之前，有两位大牛: Alonzo Church 和 Alan Turing。他俩设计的计算模型不一样，但都很受欢迎，可以用于计算任何可计算的东西。

Alonzo Church 创造了λ演算：λ演算是一个基于函数应用的古老的计算模型。Alan Turing发明了图灵机：一个抽象的计算模型，它定义了一个可以在纸带上操纵符号的抽象设备（图灵机）。

哥俩完美展现了λ演算和图灵机从函数角度来看是一样一样的。

λ演算其实就是复合函数。用复合函数来 compose software 超赞。这篇文章里，我们将会讨论软件设计中复合函数的重要性。

三个要点让λ演算与众不同：

 - 函数永远是匿名的。JavaScript 中，`const sum = (x, y) => x + y`的右侧就是函数表达式`function expression (x, y) => x + y.`的匿名表达。

 - λ演算只接收单一参数，即：永远都是一元函数。如果你希望传入不止一个参数，设定该函数的返回值依然是函数，然后再传参，如此循环往复。多元函数`(x, y) => x + y `可以表达为`x => y => x + y.`。这种从多元函数到一元函数的转型我们称作 currying。

 - 函数是一等公民，这也就意味着函数可以作为参数传给其他人，且函数的返回值可以是函数形式。

于是，这些特征一起构成了一个简单的方式来 compose 软件：用函数作为基础零部件。JavaScript 中，匿名函数和柯里化是可选项，而非不得不用。虽然 JavaScript 里可以用到很多λ演算的特性，但并不强制使用。

传统的复合函数的办法是将一个函数输出值作为参数传给另一个函数，比如：`f.g`可以写成
>  compose2 = f => g => x => f(g(x))

然后，我们就可以这么用啦：
```
double = n => n * 2
inc = n => n + 1
compose2(double)(inc)(3)
```

函数`compose2()`把 double 函数作为第一个参数，inc 函数是第二个参数，然后传入`3`，俩函数挨个传。

咱们一起仔细分析一下函数`compose2()`。f 代表 `double()`，g 表示`inc()`，传入的值为3。调用函数`compose2(double)(inc)(3)`其实是调用了三次不同的函数：

1. 首先传 double，返回一个新函数；
2. 返回的函数接收 inc 的值，然后又返回一个新函数；
3. 最新的函数接收到具体的传入值`3`，此时 `f(g(x)) =  double(inc(3))`；
4. x 等于3，传给 `inc()`；
5. inc(3)=4；
6. double(4)=8;
7. 整个函数拿到返回值8；

当我们在整合一个软件的时候，可以利用复合函数的图来解释（如下图所示），比如：

```
append = s1 => s2 => s1 + s2
append('Hello, ')('world!')
```

可视化效果如下：

![此处输入图片的描述][2]

λ演算在软件设计中非常好用。1980年之前，很多有影响力的行业大牛都在用复合函数写软件。Lisp 于1958年问世，深受λ演算的影响。直到现在，(Lisp) 都还是最古老（排第二）且依然在用的计算机语言。

我开始接触 Lisp 是因为 AutoLISP，这是一款众多计算机辅助设计 (CAD) 软件中最受欢迎的 AutoCAD  所用到的脚本语言。AutoCAD 非常火，几乎所有其他的 CAD 应用（出于兼容性考虑）都支持 AutoLISP。计算机科学专业的课程里也会涉及到 Lisp，三个理由：

 - 简单：一天内就能学完 Lisp 的基本句法和语义；
 - Lisp 其实就是函数的复合，而用复合函数造软件简直巧夺天工；
 - 我读过的[最棒的一本计算机科学的书][3]就是用的 Lisp.

## 函数式编程遭遇撼动

大概在 1970-1980 年代，软件开发模式不再基于简单的代数，而是给电脑提供一系列线性指令的模式，比如 1978 年出现的 K&R C语言和小型 BASIC 解译器，这些程序经常出现在当时早期的电脑中。

1972年，Alan Kay 设计的 Smalltalk 语言开始出道，以对象为基本单位的概念开始被接受。Smalltalk 中关于组件封装和信息传递这么屌的想法在20世纪八九十代被 C++ 和 Java 整成了尼玛继承的层级关系跟 `is-a` 继承关系啊啊啊啊啊...

尽管 Smalltalk 是一种函数式面向对象式的编程语言，但是当 C++ 和 Java 火起来了以后，函数式编程开始慢慢被边缘化，只有小众和学术界还在倡导函数式编程：对编程着迷的极客们、大学教授、以及那些在1990-2010这20年间没有被 Java 潮带偏的幸运儿们。

对我们当中的大部分人来说，做软件的经历了差不多30年的噩梦。黑暗之光一直笼罩着我们...

> 注意：我学编程的时候用的是Basic, Pascal, C++, 和 Java. 学 AutoLisp 是为了3D图像。只有AutoLisp是函数式的编程语言。开始接触编程的头几年，压根不知道在矢量图编程之外还有这么个选择。。。。


## 函数式编程再次迎来曙光

大约2010年的时候，发生了件大事儿：JavaScript 突然就火了。大概还在06年之前，JavaScript 还被认为是小儿科，不就是在网页浏览器里整一些动态效果吗？但，其实里面大有文章。即：JavaScript 中涉及λ演算中最重要的特性。大家开始私下议论这么个酷酷滴话题：函数式编程。

2015年的时候，用复合函数造软件又开始火了。简单来说就是 JavaScript 迎来了自己的春天，而且增加了箭头函数，让函数更易读更容易柯里化更更λ。

箭头函数就像是火箭的助燃剂，让 JavaScript 的函数式编程彻底爆发自己的小宇宙。现在已经找不到哪个大型项目不用函数式编程了。

## 函数式编程一直在，活着好好的呢

尽管经常被人指指点点、广受众议，但函数式编程一直活着好好的呢。Lisp和Smalltalk曾经是C语言在80/90年代最大的竞争对手。Smalltalk 曾经是包括 JP 摩根在内的世界500强的公司所用的软件解决方案，Lisp 都用在 NASA 的火星飞行器上了。

Lisp 直到现在还用在 SRI 的 AI 和生物学信息研究。（SRI 就是苹果公司 SIRI 背后的英雄）。YCombinator，硅谷名气最大的创业者孵化器，发起人之一 Paul Graham 就是 Lisp 的支持者。很火的新闻网站 Hacker News 用的 Ark 也是 Lisp 衍化而来的。

Clojure（Lisp 生态的另一个衍生品）是 Rich Hickey 在 2007 年写出来的，很快得到认可，很多诸如亚马逊苹果脸书的科技公司都用这个。

Erlang, 另一个很受欢迎的函数式编程语言，当初为了电话交换机的使用在Ericsson 开发出来的。现在 T-Mobile 的移动网络中还在用。亚马逊的云计算涉及的 SimpleDB 和 EC2 也是用 Erlang 写的。

所有这些，都是为了告诉大家：JavaScript，作为网络标准化语言，是世界上是最受欢迎的函数式编程语言，而且史无前例地让那么那么多的开发者们踏入函数式编程的大门。

下集更精彩：[为毛线用 JavaScript 学函数式编程？][4]



  [1]: https://medium.com/javascript-scene/why-learn-functional-programming-in-javascript-composing-software-ea13afc7a257
  [2]: https://cdn-images-1.medium.com/max/1600/1*LSXnRbKzQ4yhq1fjZjvq6Q.png
  [3]: https://www.amazon.com/Structure-Interpretation-Computer-Programs-Engineering/dp/0262510871/ref=as_li_ss_tl?ie=UTF8&linkCode=ll1&tag=eejs-20&linkId=4896ed63eee8657b6379c2acd99dd3f3
  [4]: https://medium.com/javascript-scene/why-learn-functional-programming-in-javascript-composing-software-ea13afc7a257#.i6vf0q8uy
