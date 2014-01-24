# CSS 代码建议标准
---

## 写在前面

CSS 是一种[领域语言](http://book.51cto.com/art/201007/209600.htm)（DSL），层叠与继承赋予了 CSS 优雅多姿的无限创造力。正是由于它如此「简单」，
我们需要一些规范来使其变得更加可依赖。在使用 CSS 的时候不要把它当做一种编程语言，应该把它当做画画或者创作。所以本文档不仅仅是一份 CSS 书写规范，更像是 CSS 创作的调色盘。

> 「作为成功的项目的一员，很重要的一点是意识到只为自己写代码是很糟糕的行为。如果将有成千上万人
使用你的代码，那么你需要编写最具明确性的代码，而不是以自我的喜好来彰显自己的智商。」  
—— Idan Gazit

在参与规模庞大、历时漫长且参与人数众多的项目时，所有开发者能遵守如下规则极为重要：

+ **保持 CSS 便于维护**
+ **保持代码清晰易懂**
+ **保持代码的可拓展性**

### 目录

* [CSS 文档分析](#CSS_document_analysis)
  * [总则]
  * [文件管理]
  * [目录]
  * [区块标题]
* [代码顺序]
* [CSS 规则集分析]
  * [BEM]
  * [JavaScript 钩子]
* [注释]
  * [注释的拓展用法]
    * [准修饰选择器]
    * [代码标签]
    * [继承标记]
* [编写 CSS](#)
  * [遵循一个标准的声明顺序](#)
  * [编写新组件](#)
  * [面向对象 CSS](#)
  * [布局](#)
  * [UI 尺寸](#)
    * [字号](#)
  * [简写](#)
  * [ID](#id)
* [选择器](#)
  * [过修饰选择器](#)
  * [选择器性能](#)
  * [使用 CSS 选择器的目的](#)
  * [`!important`](#important)
*  [使用技巧]()
  * [凑数与绝对定位](#)
  * [条件判断](#)
  * [Debugging](#debugging)
  * [CSS 预处理器](#css-)
*  [值得围观的示例]()
  *  [过度约束]()
  *  [滥用后代选择符](#)
  *  [链式（交集）选择符]()
  *  复合语法
  *  不必要的命名空间
  *  不必要的重复
  *  精简规则
  *  良好的代码格式


<a name="CSS_document_analysis"></a>
## CSS 文档分析

### 总则
* 代码使用4个空格缩进；
* “{”不换行，在 “{” 和 “}” 前后加空格；
* 每个属性描述都用分号结束；
* 属性尽量写在一行，尽量将每行控制在80字符内，渐变色等特殊情况可单独一行；

### 文件管理

有人喜欢在一份文件文件中编写所有的内容，而我们在迁移至 Seajs 之后开始将样式拆分成多个小文件。这都是很好的做法。无论你选择哪种，下文的规则都将适用，而且如果你遵守这些规则的话你也不会遇到什么问题。这两种写法的区别仅仅在于目录以及区块标题。

### 目录

在 CSS 的开头，写一份目录，例如：

    /*------------------------------------*\
        $CONTENTS
    \*------------------------------------*/
    /**
     * CONTENTS............You’re reading it!
     * RESET...............Set our reset defaults
     * FONT-FACE...........Import brand font files
     */

这份目录可以告诉其他开发者这个文件中具体含有哪些内容。这份目录中的每一项都与其对应的区块标题相同。

如果你在维护一份单文件 CSS，对应的区块将也在同一文件中。如果你是在编写一组小文件，那么目录中的每一项应当对应相应的 @include 语句。

* CSS文件头应保留声明：

    @charset “utf-8″ 

    [参考阅读：CSS中文注释引起样式失效](http://www.cnblogs.com/kaiye/archive/2011/07/30/3039045.html) 


### 区块管理

目录应当对应区块的标题。请看如下示例：

    /*------------------------------------*\
        $RESET
    \*------------------------------------*/

区块标题前缀 `$` 可以让我们使用 [Cmd|Ctrl]+F 命令查找标题名的同时<strong>将搜索范围限制在区块标题中</strong>。

如果你在维护一份大文件，那么在区块之间空 5 行，如下：

    /*------------------------------------*\
        $RESET
    \*------------------------------------*/
    [Our
    reset
    styles]
    
    
    
    
    
    /*------------------------------------*\
        $FONT-FACE
    \*------------------------------------*/

在大文件中快速翻动时这些大块的空档有助于区分区块。

如果你在维护多份、以 @include 连接的 CSS 的话，在每份文件头加上标题即可，不必这样空行。

## 代码顺序

尽量按照特定顺序编写规则，这将确保你充分发挥 CSS 缩写中第一个 <i>C</i> 的意义：cascade，层叠。

一份规划良好的 CSS 应当按照如下排列：

1. **Reset** 跨浏览器适应的金钥匙
2. **元素类型** 没有 class 的 `h1`、`ul` 等
3. **对象以及抽象内容** 最一般、最基础的设计模式 例如header、footer、silder等
4. **子元素** 由对象延伸出来的所有拓展及其子元素
5. **修补** 针对异常状态

如此一来，当你依次编写 CSS 时，每个区块都可以自动继承在它之前区块的属性。这样就可以减少代码相互抵消的部分，减少某些特殊的问题，组成更理想的 CSS 结构。

关于这方面的更多信息，强烈推荐 Jonathan Snook 的 [SMACSS](http://smacss.com)。

## CSS 规则集分析

    [selector]{
        [property]:[value];
        [<- Declaration ->]
    }

    [选择器]{
        [属性]:[值];
        [<- 声明 ->]
    }

编写 CSS 样式时，我们习惯遵守这些规则：

* class 名称以连字符（-）连接，除了下文提到的 BEM 命名法；
* 缩进 4 空格；
* 声明拆分成多行；
* 声明以相关性顺序排列，而非字母顺序；
* 有前缀的声明适当缩进，从而对齐其值；
* 缩进样式从而反映 DOM；
* 保留最后一条声明结尾的分号。

例如：

    .widget{
        padding:10px;
        border:1px solid #BADA55;
        background-color:#C0FFEE;
        -webkit-border-radius:4px;
           -moz-border-radius:4px;
                border-radius:4px;
    }
        .widget-heading{
            font-size:1.5rem;
            line-height:1;
            font-weight:bold;
            color:#BADA55;
            margin-right:-10px;
            margin-left: -10px;
            padding:0.25em;
        }

我们可以发现，`.widget-heading` 是 `.widget` 的子元素，因为前者比后者多缩进了一级。这样通过缩进就可以让开发者在阅读代码时快速获取这样的重要信息。

我们还可以发现 `.widget-heading` 的声明是根据其相关性排列的：`.widget-heading` 是行间元素，所以我们先添加字体相关的样式声明，接下来是其它的。

以下是一个没有拆分成多行的例子：

    .t10    { width:10% }
    .t20    { width:20% }
    .t25    { width:25% }       /* 1/4 */
    .t30    { width:30% }
    .t33    { width:33.333% }   /* 1/3 */
    .t40    { width:40% }
    .t50    { width:50% }       /* 1/2 */
    .t60    { width:60% }
    .t66    { width:66.666% }   /* 2/3 */
    .t70    { width:70% }
    .t75    { width:75% }       /* 3/4*/
    .t80    { width:80% }
    .t90    { width:90% }

在这个例子（来自[inuit.css’s table grid system](https://github.com/csswizardry/inuit.css/blob/master/inuit.css/partials/base/_tables.scss#L88)）中，将 CSS 放在一行内可以使得代码更紧凑。

## 命名规范

一般情况下我们都是以连字符（-）连接 class 的名字（例如 `.foo-bar` 而非 `.foo_bar` 或 `.fooBar`），不过在某些特定的时候我会用 BEM（Block, Element, Modifier）命名法。

<abbr title="Block, Element, Modifier">BEM</abbr> 命名法可以使得选择器更规范，更清晰，更具语义。

该命名法按照如下格式：

    .block{}
    .block__element{}
    .block--modifier{}

其中：

* `.block` 代表某个基本的抽象元素；
* `.block__element` 代表 `.block` 这一整体的一个子元素；
* `.block--modifier` 代表 `.block` 的某个不同状态。

打个比方：

    .person{}
    .person--woman{}
        .person__hand{}
        .person__hand--left{}
        .person__hand--right{}

这个例子中我们描述的基本元素是一个人，然后这个人可能是一个女人。我们还知道人拥有手，这些是人体的一部分，而手也有不同的状态，如同左手与右手。

这样我们就可以根据亲元素来划定选择器的命名空间并传达该选择器的职能，这个选择器是一个子元素（`__`）还是亲元素的不同状态（`--`）？

由此，`.page-wrapper` 是一个独立的选择器。这是一个符合规范的命名，因为它不是其它元素的子元素或其它状态；然而 `.widget-heading` 则与其它对象有关联，它应当是 `.widget` 的子元素，所以我们应当将其重命名为 `.widget__heading`。

BEM 命名法虽然不太好看，而且相当冗长，但是它使得我们可以通过名称快速获知元素的功能和元素之间的关系。与此同时，BEM 语法中的重复部分非常有利于 gzip 的压缩算法。

无论你是否使用 BEM 命名法，你都应当确保 class 命名得当，力保一字不多、一字不少；将元素命名抽象化以提高复用性（例如 `.ui-list`，`.media`）。由此延伸出去的元素命名则要尽量精准（例如 `.user-avatar-link`）。不用担心 class 名的数量或长度，因为写好的代码 gzip 也能有效压缩。

### HTML 中的 class

为了确保易读性，在 HTML 标记中用两个空格隔开 class 名，例如：

    <div class="foo--bar  bar__baz">

增加的空格应当可以使得在使用多个 class 时更易阅读与定位。（建议，不强推）


### JavaScript 钩子

**千万不要把 CSS 样式用作 JavaScript 钩子。**把 JS 行为与样式混在一起将无法对其分别处理。

如果你要把 JS 和某些标记绑定起来的话，写一个 JS 专用的 class。简单地说就是划定一个前缀 `.js-` 的命名空间，例如 `.js-toggle`，`.js-drag-and-drop`。这意味着我们可以通过 class 同时绑定 JS 和 CSS 而不会因为冲突而引发麻烦。

    <th class="is-sortable  js-is-sortable">
    </th>

上面的这个标记有两个 class，你可以用其中一个来给这个可排序的表格栏添加样式，用另一个添加排序功能。

## 注释

我使用行宽不超过 80 字节的块状注释：

    /**
     * This is a docBlock style comment
     * 
     * This is a longer description of the comment, describing the code in more
     * detail. We limit these lines to a maximum of 80 characters in length.
     * 
     * We can have markup in the comments, and are encouraged to do so:
     * 
       <div class=foo>
           <p>Lorem</p>
       </div>
     * 
     * We do not prefix lines of code with an asterisk as to do so would inhibit
     * copy and paste.
     */


    /**
     * 这是一个文档块（DocBlock）风格的注释。
     *
     * 这里开始是描述更详细、篇幅更长的注释正文。当然，我们要把行宽控制在 80 字以内。
     *
     * 我们可以在注释中嵌入 HTML 标记，而且这也是个不错的办法：
     *
        <div class=foo>
            <p>Lorem</p>
        </div>
     *
     * 如果是注释内嵌的标记的话，在它前面不加星号，否则会被复制进去。
     */

在注释中应当尽量详细描述代码，因为对你来说清晰易懂的内容对其他人可能并非如此。每写一部分代码就要专门写注释以详解。


---

# 编写 CSS

之前的章节主要探讨如何规划 CSS，这些都是易于量化的规则。本章将探讨更理论化的东西，也将探讨我们的态度与方法。

## 遵循统一的属性声明顺序

虽然有一些排列CSS属性顺序[常见的方式](http://css-tricks.com/new-poll-how-order-css-properties/)，我们还是应遵循统一的方式。

    .someclass {
     /* Positioning */
     /* Display & Box Model */
     /* Background and typography styles */
     /* Transitions */
     /* Other */
    }

## 编写新组件

编写新组件时，要在着手处理 CSS <strong>之前</strong>写好 HTML 部分。这可以令你准确判断哪些 CSS 属性可以继承，避免重复浪费。

先写标记的话，你就可以关注数据、内容与语义，在这之后再添加需要的 class 和 CSS 样式。

## 面向对象 CSS

我以面向对象 CSS 的方式写代码。我把组件分成结构（对象）与外观（拓展）。正如以下分析：

    .room{}
    
    .room--kitchen{}
    .room--bedroom{}
    .room--bathroom{}

我们在屋子里有许多房间，它们都有共同的部分：它们都包含地板、天花板、墙壁和门。这些共享的部分我们可以放到一个抽象的 `.room{}` class 中。不过我们还有其它与众不同的房间：一个厨房可能有地砖，卧室可能有地毯，洗手间可能没有窗户但是卧室会有，每个房间的墙壁颜色也许也会不一样。面向对象 CSS 的思路使得我们把相同部分抽象出来组成结构部分，然后用更具体的 class 来拓展这些特征并添加特殊的处理方法。

所以比起编写大量的特殊模块，应当努力找出这些模块中重复的设计模式并将其抽象出来，写成一个可以复用的 class，将其用作基础然后编写其它拓展模块的特殊情形。

当你要编写一个新组件时，将其拆分成结构和外观。编写结构部分时用最通用 class 以保证复用性，编写外观时用更具体的 class 来添加设计方法。

## 布局

所有组件都不要特意声明宽度，而由其父元素或格栅系统来决定。

**坚决不要**声明高度。高度应当仅仅用于尺寸已经固定的东西，例如图片和 CSS Sprite。在 `p`，`ul`，`div` 等元素上不应当声明高度。如果需要的话可以写 `line-height`，这个更加灵活。

格栅系统应当当作书架来理解。是它们容纳内容，而不是把它们本身当成内容装起来，正如你先搭起书架再把东西放进去。比起声明它们的尺寸，把格栅系统和元素的其它属性分来开处理更有助于布局，也使得我们的前端工作更高效。

你在格栅系统上不应当添加任何样式，他们仅仅是为布局而用。在格栅系统内部再添加样式。在格栅系统中任何情况下都不要添加盒模型相关属性。

## UI 尺寸

我们有很多方法设定 UI 尺寸，包括百分比，`px`，`em`，`rem` 以及干脆什么都不用。

理想情况下，格栅系统应当用百分比设定。如上所述，如果用格栅系统来固定栏宽和页宽，就可以不用理会元素的尺寸。

我们建议用 rem 定义字号，并且辅以 px 以兼容旧浏览器。这可以兼具 em 和 px 的优势。下面是一个非常漂亮的 Sass Mixin，假设你在别处声明了基本字号（base-font-size）的话，用它就可以生成 rem 以及兼容旧浏览器的 px。

    @mixin font-size($font-size){
        font-size:$font-size +px;
        font-size:$font-size / $base-font-size +rem;
    }

只在已经固定尺寸的元素上使用 px，包括图片以及尺寸已经用 px 固定的 CSS Sprite。

关于rem的更多介绍请参考：[CSS3的REM设置字体大小](http://www.w3cplus.com/css3/define-font-size-with-css3-rem)



### 字号

定义一些与格栅系统原理类似的 class 来声明字号。这些 class 可以用于双重标题分级，关于这点请阅读 [Pragmatic, practical font-sizing in CSS](http://csswizardry.com/2012/02/pragmatic-practical-font-sizing-in-css)。

## 简写

**CSS 简写应当谨慎使用。**
编写像 `background:red;` 这样的属性的确很省事，但是你这么写的意思其实是同时声明 `background-image:none; background-position:top left; background-repeat: repeat; background-color:red;`。虽然大多数时候这样不会出什么问题，但是哪怕只出一次问题就值得考虑要不要放弃简写了。这里应当改为 `background-color:red;`。

类似的，像 `margin:0;` 这样的声明的确简洁清爽，但是还是应当<strong>尽量写清楚</strong>。如果你只是想修改底边边距，就要具体一些，写成 `margin-bottom:0;`。

反过来，你需要声明的属性也要写清楚，不要因为简写而波及其它属性。例如如果你只想改掉底部的 `margin`，那就不要用会把其它边距也清零的 `margin:0`。

简写虽然是好东西，但是注意切勿滥用。

## ID

在我们开始处理选择器之前，牢记这句话：

**在 CSS 里坚决避免使用 ID。**

在 HTML 里 ID 可以用于 JS 以及锚点定位，但是在 CSS 里只需要用 class，一个 ID 也不要用。

Class 的优势在于复用性，而且私有度也并不高。私有度非常容易导致问题，所以将其降低就尤为重要。ID 的私有度是 class 的 **255** 倍，所以在 CSS 中不要使用。

在一种情况下，即开发提供给不同网站使用的插件时，为了避免跟对方网站CSS冲突，可以使用插件最外层使用ID，子元素都用该ID选择的方法。

## 选择器

务必保持选择器简短高效。

通过页面元素位置而定位的选择器并不理想。例如 `.sidebar h3 span{}` 这样的选择器就是定位过于依赖相对位置，所以把 span 移到 h3 和 sidebar 外面时就很难保持其样式。

结构复杂的选择器将会影响性能。选择器结构越复杂（如 `.sidebar h3 span` 为三层，`.content ul p a` 是四层），浏览器的消耗就越大。

尽量使得样式不依赖于其定位，尽量保持选择器简洁清晰。

作为一个整体，选择器应当尽量简短（例如只有一层结构），但是 class 名则不应当过于简略，例如 `.user-avatar` 就远比 `.usr-avt` 好。

**牢记：**class 无所谓是否语义化；应当关注它们是否合理。不要强调 class 名要符合语义，而要注重使用合理且不会过时的名称。

### 过度修饰的选择器

由前文所述，过度修饰的选择器并不理想。

过度修饰的选择器是指像 `div.promo` 这样的。很可能你只用 `.promo` 也能得到相同的效果。当然你可能偶尔会需要用元素类型来修饰 class（例如你写了一个 `.error` 而且想让它在不同的元素类型中显示效果不一样，例如 `.error{ color:red; }` `div.error{ padding:14px;}`），但是大多数时候还是应当尽量避免。

再举一个修饰过度的选择器例子，`ul.nav li a{}`。如前文所说，我们马上就可以删掉 `ul` 因为我们知道 `.nav` 是个列表，然后我们就可以发现 `a` 一定在 `li` 中，所以我们就能将这个选择器改写成 `.nav a{}`。

### 选择器性能

虽然浏览器性能日渐提升，渲染 CSS 速度越来越快，但是你还是应当关注效率。使用间断、没有嵌套的选择器，不把全局选择器（`*{}`）用作核心选择器，避免使用日渐复杂的 CSS3 新选择器可以避免这样的问题。

译注，核心选择器：浏览器解析选择器为从右向左的顺序，最右端的元素是样式生效的元素，是为核心选择器。

## 使用 CSS 选择器的目的

比起努力运用选择器定位到某元素，更好的办法是直接给你想要添加样式的元素直接添加一个 class。我们以 `.header ul{}` 这样一个选择器为例。

假定这个 `ul` 就是这个网站的全站导航，它位于 header 中，而且目前为止是 header 中唯一的 `ul` 元素。`.header ul{}` 的确可以生效，但是这样并不是好方法，它很容易过时，而且非常晦涩。如果我们在 header 中再添加一个 `ul` 的话，它就会套用我们给这个导航部分写的样式，哪怕我们设想的不是这个效果。这意味着我们要么要重构许多代码，要么给后面的 `ul` 新写许多样式来抵消之前的影响。

你的选择器必须符合你要给这个元素添加样式的原因。思考一下，<strong>「我定位到这个元素，是因为它是 `.header` 下的 `ul`，还是因为它是我的网站导航？」</strong>这将决定你应当如何使用选择器。

确保你的核心选择器不是类型选择器，也不是针对高级对象或抽象选择器。例如不要使用 `.sidebar ul{}` 或者 `.footer .media{}` 这样的选择器。

表达清晰：直接找到你要添加样式的元素，而非其亲元素。不要想当然地认为 HTML 不会改变。<strong>用 CSS 直接命中你需要的元素，而非投机取巧。</strong>

更多介绍内容请参考文章 [Shoot to kill; CSS selector intent](http://csswizardry.com/2012/07/shoot-to-kill-css-selector-intent/)

## `!important`

只在起辅助作用的 class 上用 `!important`。用 `!important` 提升优先级也可以，例如如果你要让某条规则<strong>一直</strong>生效的话，可以用 `.error{ color:red!important; }`。

避免主动使用 `!important`。例如 CSS 写得很复杂时不要用它来取巧，要好好整理并重构之前的部分，保持选择器简短并且避免用 ID 将效果更好。

## 凑数与绝对定位

凑数（Magic Number）是指那些「凑巧有效果」的数字，这东西非常不好，因为它们只是治标不治本而且缺乏拓展性。

例如 `.dropdown-nav li:hover ul{ top:37px; }` 把下拉菜单移动下来远非良策，因为这里的 37px 就是个凑数。37px 会生效的原因是因为这时 `.dropbox-nav` 碰巧高 37px 而已。

这时你应该用 `.dropdown-nav li:hover ul{ top:100%; }`，也即无论 `.dropbox-down` 多高，这个下拉菜单都会往下移动 100%。

每当你要在代码中放入数字的时候，请三思而行。如果你能用一个关键字（例如  `top:100%` 意即「从上面拉到最下面」）替换之，或者有更好的解决方法的话，就尽量避免直接出现数字。

你在 CSS 中留下的每一个数字，都是你许下而不愿遵守的承诺。

## 条件判断

专门为 IE 写的样式基本上都是可以避免的，唯一需要为 IE 专门处理的是为了处理 IE 不支持的内容（例如 PNG）。

简而言之，如果你重构 CSS 的话，所有的布局和盒模型都不用额外兼容 IE。也就是说你基本上不用 `<!--[if IE 7]> element{ margin-left:-9px; } < ![endif]-->` 或者类似的兼容 IE 的写法。 

## Debugging

如果你要解决 CSS 问题的话，<strong>先把旧代码拿掉再写新的</strong>。如果旧的 CSS 中有问题的话，写新代码是解决不了的。

把 CSS 代码和 HTML 部分删掉，直到没有 BUG 为止，然后你就知道问题出在哪里了。

有时候写上一个 `overflow:hidden` 或者其它能把问题藏起来的代码的确效果立竿见影，但是 overflow 方面可能根本就没问题。所以<strong>要治本，而不是单纯治标</strong>。

## CSS 预处理器

我们的项目中，有ruby环境，所以可以优先考虑 Sass。使用时应当<strong>灵活运用</strong>。用 Sass 可以令你的 CSS 更强大，但是不要嵌套得太复杂。在 Vanilla CSS 中，只在必要的地方用嵌套即可，例如：

    .header{}
    .header .site-nav{}
    .header .site-nav li{}
    .header .site-nav li a{}

这样的写法在普通 CSS 里完全用不到。以下为<strong>不好的</strong> Sass 写法：

    .header{
        .site-nav{
            li{
                a{}
            }
        }
    }

如果你用 Sass 的话，尽量这么写：

    .header{}
    .site-nav{
        li{}
        a{}
    }

## 值得围观的示例

### 过度约束
作为一般规则，不添加不必要的约束。

    // 糟糕
    ul#someid {..}
    .menu#otherid{..}
    
    // 好的
    #someid {..}
    #otherid {..}

<a name="滥用后代选择符"></a>
### 滥用后代选择符
不仅性能低下而且代码很脆弱，html代码和css代码严重耦合，html代码结构发生变化时，CSS也得修改，这是多么糟糕，特别是在大公司里，写html和css的往往不是同一个人。
    
    // 坑爹呢
    html div tr td {..}


### 链式（交集）选择符
这和过度约束的情况类似，更明智的做法是简单的创建一个新的CSS类选择符。

    // 糟糕
    .menu.left.icon {..}
    
    // 好的
    .menu-left-icon {..}

### 复合语法
尽可能使用复合语法。

    // 糟糕
    .someclass {
     padding-top: 20px;
     padding-bottom: 20px;
     padding-left: 10px;
     padding-right: 10px;
     background: #000;
     background-image: url(../imgs/carrot.png);
     background-position: bottom;
     background-repeat: repeat-x;
    }
    
    // 好的
    .someclass {
     padding: 20px 10px 20px 10px;
     background: #000 url(../imgs/carrot.png) repeat-x bottom;
    }

### 不必要的命名空间
    // 糟糕
    .someclass table tr.otherclass td.somerule {..}
    
    //好的
    .someclass .otherclass .somerule {..}
### 不必要的重复

    // 糟糕
    .someclass {
     color: red;
     background: blue;
     font-size: 15px;
    }
    
    .otherclass {
     color: red;
     background: blue;
     font-size: 15px;
    }
    
    // 好的
    .someclass, .otherclass {
     color: red;
     background: blue;
     font-size: 15px;
    }

### 尽可能精简规则
在上面规则的基础上，可以进一步合并不同类里的重复的规则。

    // 糟糕
    .someclass {
     color: red;
     background: blue;
     height: 150px;
     width: 150px;
     font-size: 16px;
    }
    
    .otherclass {
     color: red;
     background: blue;
     height: 150px;
     width: 150px;
     font-size: 8px;
    }
    
    // 好的
    .someclass, .otherclass {
     color: red;
     background: blue;
     height: 150px;
     width: 150px;
    }
    
    .someclass {
     font-size: 16px;
    }
    
    .otherclass {
     font-size: 8px;
    }

### 良好的代码格式
代码的易读性和易维护性成正比。下面是我们建议的格式化方法。

    // 糟糕
    .someclass-a, .someclass-b, .someclass-c, .someclass-d {
     ...
    }
    
    // 好的
    .someclass-a, 
    .someclass-b, 
    .someclass-c, 
    .someclass-d {
     ...
    }
    
    // 好的做法
    .someclass {
    background-image:
    linear-gradient(#000, #ccc),
    linear-gradient(#ccc, #ddd);
    box-shadow:
    2px 2px 2px #000,
    1px 4px 1px 1px #ddd inset;
    }

显然，这些只是极少数的示例，是我们在日常中积累的规则。如果你想阅读更多的知识，建议阅读MDN上的编写[高效的CSS](https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Writing_efficient_CSS)和谷歌指南上的[优化浏览器渲染](https://developers.google.com/speed/docs/best-practices/rendering#UseEfficientCSSSelectors)。







