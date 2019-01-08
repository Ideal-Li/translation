

先推荐一本100岁了的英文写作书，[The Element of Style][5]，很短，很容易看完，很经典，很干货。

偶然发现，Eric Elliott的[一篇文章][6]提到了这本书，敲代码和写文章其实无比相似。


# JavaScript 风格的要素

1920年，William Strunk Jr 出版了《风格的要素》，一本关于英文写作的指南，不到一百页的小册子历经时间的考验，至今仍风靡全球。敲代码也是如此，遵守一些特定的准则一定可以让你的代码独具一格。

接下来介绍的就是咱们再写代码的时候应当遵守的准则，不过可别生搬硬套，有时候为了明确代码的语义当然可以不完全遵从。但是，你得很[谨慎，且知道自己写的是什么][7]。推荐这些准则是有原因的：这可都是前辈们总结出来的智慧。不想这么干也行，你得有上的了台面的理由，可别因为一时兴起或者为了追求个性而特立独行。

#### 几乎所有写作的基本原则都适用于你的源代码（通常写作书都会这么讲）：
- 文章以段落为单位：一段围绕一个核心思想而展开
- 不必要的词：删掉
- 用主动语态（避免被动语态）
- 避免出现连续松散的句子（上下文不紧凑）
- 相关的内容写在一起
- 观点陈述用肯定句
- 平行的概念就用平行结构写


#### 转换为代码思维，就变成了：
- 以函数为单元敲代码，一个函数负责搞定一件事儿
- 省略不必要的代码
- 用主动语态
- 避免连续写松散的语句（上下文联系紧密）
- 相关的代码写在一起
- 语句和表达式用肯定形式写（避免用否定式）
- 相似的内容写得对称

---

## 以函数为单位敲代码，一个函数搞定一件事儿
> Make the function the unit of composition. One job for each function.

软件开发的本质不就是搭积木吗，把模块、函数、数据结构拼起来。理解如何搭建和组合函数是软件工程师的基本技能！

模块（modules）其实就是一个或者多个函数与数据结构的集合，数据结构就是我们呈现程序状态(program state)的方式。其实不论是模块还是数据结构都挺无聊的，直到我们遇见了函数这么个精灵（贼好玩）。

JavaScript里有三种函数：
- 负责搞关系的函数（Communicating functions）： 负责执行输入/输出（I/O）
- 负责搞程序的函数（procedural functions）：很多函数在一起组队干活
- 负责映射的函数（Mapping functions）：特定的输入对应相应的输出

### 一函数只干一件事儿 / One thing for each function

如果你的函数是负责 I/O的，别把它和负责映射（或者说负责计算）的函数搞在一起。如果你的函数是负责映射的，别把它和负责 I/O 的函数弄混。Procedural 函数的定义（很多函数在一起组队干活）就决定了人家必然违反一个函数只干一个活的准则，不止如此，它还违背了另一个准则：避免松散的语句写一起。

最理想的函数得很简单、确定的、纯的：
- 输入同样的 input 总能返回同样的结果（output）
- 没有副作用 

详见[什么是纯函数][1]

---

## 省略不必要的代码 / Omit needless code

> 最牛逼的写作都是俭省的，字字珠玑，就像一幅画里所有细节都恰到好处，就像一台机器不会有任何多余的零部件。并不是说文章一定要短，也不是说要忽略细节只给提纲，而是每个词、每句话都用得无以伦比。 
--- By William Strunk Jr 《风格的要素》

代码也要讲求俭省（concise），越多的代码意味着更可能出现 bugs。
更少的代码 = bugs 可以藏的地方少 = 更少的bugs。

更俭省的代码也更易读，有价值的信息/噪音的比值(signal-to-noise ratio)更高。读者可以剔除不必要成分更快速地理解代码的语义。
更少的代码 = 更少的语义理解干扰 = 信息传递准确性更高

套用《风格的要素》里的话说就是俭省的代码更牛逼哄哄！(“Concise code is more vigorous”)

```js
function secret (message) {
  return function () {
    return message;
  }
};
```

可以简化为：
```js
const secret = msg => () => msg;
```

明显后者易读性更强（对熟悉ES6箭头函数的选手来说），省去了不必要的句法：括号、function 关键字、和 return 语句都省了！

ES6是2015年开始执行的标准，花时间[慢慢熟悉][2]吧！

### 省去不必要的变量 / Omit Needless Variables

有时候我们总有给东西起名字的冲动，不管有没有必要。而[人脑记忆缓存是有上限][3]的，每多一个变量都得多占用一点脑容量。

于是，工程师们开始略去各种没必要存在的变量。

比如，大部分的情况下，你都不应该给为了给返回值取名字而多定义一个变量。函数名字也需要尽可能提供关于返回值的信息。对比如下代码：

```js
const getFullName = ({firstName, lastName}) => {
  const fullName = firstName + ' ' + lastName;
  return fullName;
};
```
和

```js
const getFullName = ({firstName, lastName}) => (
  firstName + ' ' + lastName
);
```

另一个工程师们常用的减少变量定义的招：利用函数组合和无参风格的编程(point-free style)。

Point-free style是一种定义函数的方式，压根就没参数什么事儿。常见的方式：柯里化函数和函数组合。

下面这个例子就用到了柯里化函数（currying）:
```js
const add2 = a => b => a + b;
// Now we can define a point-free inc()
// that adds 1 to any number.
const inc = add2(1);
inc(3); // 4
```

注意 inc()这个函数：人家没用 function 关键字喔，人家也没用箭头函数的语法，人家没参数哟，人家返回的是一个知道自己该怎么干活的函数。

一起看一个函数组合的例子。函数组合（function composition）的意思是用一个函数处理另一个函数的结果。不论你是否听说过这个概念，你肯定用过。但凡用到链式方法，比如 map()或者 promise.then()，都是函数组合。最基本的形式就是 f(g(x))。

俩函数组合在一起意味着省去了一个定义变量的步骤是不是。

带你们感受一把函数组合怎么省代码：

```js
const g = n => n + 1;
const f = n => n * 2;
// With points:
const incThenDoublePoints = n => {
  const incremented = g(n);
  return f(incremented);
};
incThenDoublePoints(20); // 42
// compose2 - Take two functions and return their composition
const compose2 = (f, g) => x => f(g(x));
// Point-free:
const incThenDoublePointFree = compose2(f, g);
incThenDoublePointFree(20); // 42
```

那啥，(省代码)也可以通过函子（functor）实现。[函子][4]就是可以来一轮映射的东西，比如数组（Array.map()）或者 promise(比如promise.then())。咱用函数组合的map链改写上述代码里的compose2：

```js
const compose2 = (f, g) => x => [x].map(g).map(f).pop();
const incThenDoublePointFree = compose2(f, g);
incThenDoublePointFree(20); // 42
```
每次用 promise 链其实都是在干这事儿呗。

几乎所有函数式编程的库都至少用到了两种组合应用：compose()和 pipe()。前者从右往左、后者从左往右执行函数。

Lodash管它们叫 compose()和 flow()。哥用 Lodash的时候，经常这么引：

```js
import pipe from 'lodash/fp/flow';
pipe(g, f)(20); // 42
```

但是，这并不意味着更多的代码，它可以实现同样的功能：
```js
const pipe = (...fns) => x => fns.reduce((acc, fn) => fn(acc), x);
pipe(g, f)(20); // 42
```
要是感觉懵逼，或者不确定自己会不会用，仔细思考如下内容：

软件开发的实质是拼接。用更小的模块、函数、数据结构的组合搭建自己的应用。

其实吧，函数和对象组合的功能就像盖房子用的电钻和射钉枪。而用命令式代码通过变量把函数拼装在一起的过程就像在用宽胶带和强力胶呗。

记住：
- 如果可以用更少代码实现且不改变语义、不会导致语义模糊，就应该用更少代码！
- 如果可以用更少变量实现且不改变语义、不会导致语义模糊，就应该用更少变量！

--- 

## 用主动语态 / Use Active Voice

> 主动语态会比被动语态更直接、更有力！ 
--- By William Strunk Jr 《风格的要素》

命名的时候越直接越好：
- myFunction.wasCalled() 优于 myFunction.hasBeenCalled() 
- createUser() 优于 User.create()
- notify() 优于 Notifier.doNotification()

给判断条件(predicates)和布尔值命名的时候，就好像是在问是非题：
- isActive(user) 优于 getActiveStatus(user)
- isFirstRun = false; 优于 firstRun = false;

用动词的形式命名函数
- increment() 优于 plusOne()
- unzip() 优于 filesFromZip()
- filter(fn, array) 优于 matchingItemsFromArray(fn, array)

### 事件处理 / Event Handlers

事件处理和生命周期的方法(LifeCycle Methods)是不适用于刚刚提到的尽量用动词命名的规则，因为它俩是用于限定的（used as qualifiers），它俩不是用于表达“要干嘛(what to do)”，而是用来传递“什么时候干(when to do)”。它们的命名最好能体现“什么时候干”相关的信息。比如:

- element.onClick(handleClick) 优于 element.click(handleClick)
- component.onDragStart(handleDragStart) 优于 component.startDrag(handleDragStart)

第二种形式看上去好像是要去触发事件，而不是响应它们。

### 生命周期的方法 / LifeCycle Methods

考虑接下来的情况：一个组件生命周期的一个方法想在组件更新之前调一个函数：

- componentWillBeUpdated(doSomething)
- componentWillUpdate(doSomething)
- beforeUpdate(doSomething)

第一个例子用的是被动语态（用will be updated 而不是 will update），不仅读起来拗口，也没比其他俩更简洁

与之相比，第二个例子就好很多，但这个生命周期的方法其实只不过是想调一个处理(handler)，componentWillUpdate(handler)读起来就好像是要去更新 handler，有歧义喂。我们想要实现的是：在组件更新之前调用 handler。用第三个选项的 beforeComponentUpdate() 就刚刚好。

还可以进一步简化，组件本身已经内置了，在方法的命名中再次提及其实是冗余的（比如下例中component出现了两次）。试想一下，调用这些方法的时候会出现什么情况：component.componentWillUpdate()。呵呵哒。听起来很像"吉米吉米午餐吃牛排哟"。一句话的主语没必要出现两次吧。

- component.beforeUpdate(doSomething) 优于 component.beforeComponentUpdate(doSomething)

**函数混合**(Functional mixins)是指给一个对象添加属性和方法的多个函数。单一函数挨个调用，很像组装生产的流水线。每个 functional mixin 输入一个 instance，加工，传递给下一个函数。

哥喜欢用形容词给 functional mixins 起名字。你也可以用-ing 或-able 后缀的词。比如：
- const duck = composeMixins(flying, quacking);
- const box = composeMixins(iterable, mappable);

---

## 避免连续用松散的语句 / Avoid a Succession of Loose Statements

> ...一系列的 soon 听起来超级冗余和啰嗦
--- By William Strunk Jr 《风格的要素》

工程师们常常把一些列的事件拼接在一起（成为一个procedure）：数个松散、相关的语句挨个执行。可用的多了读起来就会像老太太的裹脚布（a recipe for spaghetti code）。

这些 procedures 经常会以对称的形式反复出现，每个 procedure 会有些许不同。比如，一个用户接口组件(interface component)会和几乎所有用户接口的组件有高度重复。其实可以通过划分生命周期阶段，用不同函数来处理这种情况。

比如接下的代码：
```js
const drawUserProfile = ({ userId }) => {
  const userData = loadUserData(userId);
  const dataToDisplay = calculateDisplayData(userData);
  renderProfileData(dataToDisplay);
};
```

这个函数处理三个不同的事情：加载数据、计算、渲染。

大部分前端应用中，这三者是分离的。各自独立的话，可以专门安排特定的函数干特定的活。

比如，我们甚至可以彻底替换渲染的部分，而不影响其他功能的执行。就像 React里有很多 custom renderers: ReactNative for native iOS & Android apps, AFrame for WebVR, ReactDOM/Server for server-side rendering,等等。

函数不分离还会有一个问题: 你没办法部分获取想要的结果，也不能跳过前面的步骤得到后面的结果。如果其他地方已经执行过部分内容（比如已经加载过数据了）会怎样？后续可能会做很多不必要的重复作业。

分离计算还有利于测试的便利性。哥喜欢给代码划块儿（以 unit 为单位），边写的时候就边测试。但是，如果渲染和数据加载的代码并不独立的话，咋测？从头到尾来一轮，麻烦死了！各种乱！

我不用挨个测每个代码 unit，函数分离令人可以一起测很多个 units（他们之间是独立的），获取及时反馈。

上面的例子已经分离了各函数，我们可以分别置于不同生命周期当钩子。组件ok 以后触发 loading 函数，当浏览状态更新后再执行计算和渲染。

结果是软件的各个部分权责明确，界限分明。每个组件都可以反复使用相同的结构，反复用生命周期的钩子，明显性能更优。不需要执行很多不必要的重复计算。

---

## 相关的代码搁一块儿 / Keep related code together.

很多框架和boilerplates硬性规定了程序里各文件规整的方法，当你只需要开发一个很小的计算器或者时间管理的app时还凑合用，但当项目特别大的时候，最好按性能（feature）分组置放各文件。

比如，这里有两种文件规整的方式（设计一个时间管理 app 时）：

- 按类型划分

```
.
├── components
│   ├── todos
│   └── user
├── reducers
│   ├── todos
│   └── user
└── tests
    ├── todos
    └── user
    
```
- 按性能划分
```
.
├── todos
│   ├── component
│   ├── reducer
│   └── test
└── user
    ├── component
    ├── reducer
    └── test
```

按性能分类文件可以避免在众多文件中上下滚屏(比如需要定位并修改单一文件名时)。

> 按性能把相关的文件进行整合

---

## 用肯定形式陈述语句和表达式 / Put statements and expressions in positive form

> 用明确的肯定形式。避免中庸、无感情色彩、犹豫不决、非直接的表述。用词不是作为拒绝、提出异议的手段，措辞永远不要模棱两可。
--- By William Strunk Jr 《风格的要素》

- isFlying 优于 isNotFlying
- late 优于 notOnTime

### If 语句

```js
if (err) return reject(err);
// do something...
```

优于
```js
if (!err) {
  // ... do something
} else {
  return reject(err);
}
```

### 三元运算 / Ternaries
```js
{
  [Symbol.iterator]: iterator ? iterator : defaultIterator
}
```
优于
```js
{
  [Symbol.iterator]: (!iterator) ? defaultIterator : iterator
}
```

### 推荐用强否定语句 / Prefer Strong Negative Statements

有时候我们只关心是否有变量missing，所以用一个非否定形式的名字可能会逼着我们不得不用‘！’运算符。这种情况下，用否定形式取名字。“not”这个词和‘！’运算符会给你的代码减分（create weak expressions）。

- if (missingValue) 优于 if (!hasValue)
- if (anonymous) 优于 if (!user)
- if (isEmpty(thing)) 优于 if (notDefined(thing))

### 避免把 null 和 undefined 传给函数 / Avoid null and undefined arguments in function calls

调用函数的时候，避免把 undefined 或者 null 作为可选参数传给函数。最好用 named options objects:

```js
const createEvent = ({
  title = 'Untitled',
  timeStamp = Date.now(),
  description = ''
}) => ({ title, description, timeStamp });''
// later...
const birthdayParty = createEvent({
  title: 'Birthday Party',
  description: 'Best party ever!'
});
```
优于
```js
const createEvent = (
  title = 'Untitled',
  timeStamp = Date.now(),
  description = ''
) => ({ title, description, timeStamp });''
// later...
const birthdayParty = createEvent(
  'Birthday Party',
  undefined, // This was avoidable
  'Best party ever!'  
);
```

---

## 对称的概念就用对称的代码 / Use Parallel Code for Parallel Concepts

> 平行结构要求表达的内容具有可比性且表达形式相似。结构和语义的对称让读者更易理解。
--- By William Strunk Jr 《风格的要素》

开发过程中，很少会遇到前人未解决的问题。我们常常是在不断重复大家做过了的事情，这也就意味着，我们可以抽象化相似的过程。找到那些不断重复的动作，设计套路，时间和精力只需要用来应对少量的不同。而套路和框架，已经有N多第三方库在帮我们做且做好了！

UI 组件就是个极佳的例子。几年前，用 jQuery 搞定应用逻辑和网络 I/O还挺常见的，后来大家发现 MVC 可以在客户端用于 Webapp，于是从 UI update 的逻辑里分离出了众多模块。

最终，web app 着陆在组件模块上了，那么我们就可以用组件化开发了，比如用 JSX 或者 HTML 的 templates.

所以呢，现在大家都在用这样的方式：每个组件用同样的 UI update logic, 而不是针对每个组件一一定制。

对组件化开发很熟悉的人很快就能理解每个组件是怎么一起工作的：declarative markup 负责 express UI 元素，事件处理(Event Handlers)和行为挂钩，生命周期通过钩子函数按需调用回调函数。

当我们在不断反复用相似的思路解决相似的问题时，对熟悉套路的人来说，搞定代码就是很轻松的事情了。

---

## 总结：代码应该简约、不简单 / Conclusion: Code Should Be Simple, Not Simplistic

> Vigorous writing is concise. A sentence should contain no unnecessary words, a paragraph no unnecessary sentences, for the same reason that a drawing should have no unnecessary lines and a machine no unnecessary parts. **This requires not that the writer make all sentences short, or avoid all detail and treat subjects only in outline, but that every word tell.** 
--- By William Strunk Jr 《风格的要素》

> 一整段译为中文只用四个字：**字字玑珠**


ES6早在2015年就标准化了，然而，到了2017年都还有很多人没开始用更简单的箭头函数、更简洁的返回方式、rest 和 spread 运算符等等，坚持用自己更熟悉但并不简洁的方式。这其实是大错特错。熟能生巧！熟悉之后，ES6的语法用起来当然更简洁，更优质！

> 代码应该简约，而不简单。

**俭省的代码**应该具备如下特征：更少 bug 可能、更易 debug；更好写、更易读、更易维护

**BUGS**：维护起来代价特别高；会引发更多 bugs；影响开发过程的流畅性


花时间培训工程师去写更简洁的代码(通过尽可能用俭省的句法、柯里化、函数组合的方式等)，一定是值得的！如果担心读者‘对这些内容不熟悉’作为借口不去改变（坚持用旧的语法写代码），其实会给读者一种高高在上的感觉（以为读者看不懂才用更简单、原始的办法写代码）。甚至读者会以为你是为了故意让他看懂才这样写，低估了人家的水平。

作者可以假设读者不了解、不曾听说文章的相关话题/代码的相关格式，但千万别觉得读者蠢，也千万别以为读者看不懂。

简单、直接，别刻意写low。刻意写地简单（像成年人对小朋友说话的口吻）不仅浪费彼此时间精力，还会让读者觉得自己智商被羞辱了。花时间多练，熟悉以后掌握更优质的编程词汇，更牛逼的范儿。

> 代码应该通俗易懂，但想做到并不简单。

---

译得很粗糙，大家凑合着看。发现错误/不专业的地方记得告诉我，谢谢你！

[1]:https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-pure-function-d1c076bec976
[2]: https://medium.com/javascript-scene/familiarity-bias-is-holding-you-back-its-time-to-embrace-arrow-functions-3d37e1a9bb75
[3]: http://www.nature.com/neuro/journal/v17/n3/fig_tab/nn.3655_F2.html
[4]: https://medium.com/javascript-scene/functors-categories-61e031bac53f
[5]: http://www.jlakes.org/ch/web/The-elements-of-style.pdf
[6]: https://medium.com/javascript-scene/elements-of-javascript-style-caa8821cb99f
[7]: https://medium.com/javascript-scene/familiarity-bias-is-holding-you-back-its-time-to-embrace-arrow-functions-3d37e1a9bb75
