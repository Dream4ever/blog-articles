# jQuery 中的 $("foo.bar") 与 $(foo.bar)

在 jQuery 中，`$("div.className")` 这样的代码很常见，其实就是 `document.querySelector("div.className")` 或  `document.querySelectorALl("div.className")` 的简写，但是 `$(form.textMsg)` 这样传入变量的代码还是第一次见，这篇文章就介绍了对这种代码的剖析过程。

---

今天上午闲来无事，就想着把 [前端相关资源汇总](https://github.com/Dream4ever/Coding-Life/blob/master/Front-End/Front-End%20Resource%20Collection.md) 这篇文章中收集的链接整理一下，看到 [《jQuery === 面条式代码？》](https://fed.renren.com/2017/09/03/jquery-not-noodle-code/) 这篇文章的时候，感觉里面的代码挺有意思 ，于是就一边读文章，一边照着敲代码。

敲到文章第一节中一段 JS 代码的时候，自己就有些迷糊了，因为看到了以前从来没见到过的用法。

第一行 JS 代码完全能看懂，就是用 jQuery 选择指定的 DOM 元素，代码最后加了 `[0]`，是因为用 `$()` 选择器得到的结果是数组。

可第二行的 `$(form.texMsg)` 究竟是怎么定位到 `textarea` 元素上的？
z
最开始以为是 `$("div.className")` 这种语法，于是把第一行 JS 代码注释掉，发现代码就不能用了，看来猜测不正确。而且！如果是 `$("div.className")` 这种语法的话，括号里面的双引号可是不能省略的！

另外，自己还查了 jQuery 的 API 文档，`$()` 这种语法的参数好像只接收字符串啊，可 `form.textMsg` 不是变量么？莫非 jQuery 源码中对非字符串类型的参数做了处理？

那就用代码试试吧。于是重新写了 HTML 和 JS 进行测试。结果发现，如果变量 `dd1` 是一个具体的 DOM 元素的话，`$(dd1)` 这样的语法是可以成功选择该元素的。

但是，不管子元素是什么类型，`$(dd1.d2)` 这样的语法都无法选择子元素。这个时候，父元素是 `div` 类型。那么如果我参照前面的代码，把父元素改成 `form` 类型的呢？经过测试，发现这回的猜测还真对了。只有在父元素是 `form` 类型，子元素是有效的表单元素：`input`、`textarea` 和 `button`，这种情况下，`$(dd1.d2)` 这样的语法才能选中子元素。

这个时候，自己又看了看另一句代码：`form.tweet`，这完全就是原生 JS 的用法了，既然这种语法也是有效的，那么是不是就意味着：在原生 JS 中，如果变量 `dd1` 是 `form` 类型的 DOM 元素，其子元素是有效的表单元素，并且子元素指定 `name` 属性值为 `d2` 的话，也可以用 `dd1.d2` 这种语法选择子元素？测试了一下，果然如此！

再拓展一下，如果多个子元素具有相同的 `name` 属性值，那么用上面的代码，应该会选中所有这些元素吧？试了一下，嗯，也印证了自己的想法。

短短的两行代码，花了自己一上午才研究清楚，也是蛮有意思的，哈哈。