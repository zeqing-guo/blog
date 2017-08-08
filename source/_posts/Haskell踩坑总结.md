---
title: Haskell踩坑总结
date: 2016-11-27 20:15:47
tags: Programming
---

这个是我前些年自学Haskell时整理的资料，现在可能有点过时了，不过应该还有些参考价值吧。

## 入门参考

- 参考 http://stackoverflow.com/questions/1012573/getting-started-with-haskell
  - 先看*LYAH*，看到Functor时可以开始做*haskell 99 problems*。不用全做完，做到差不多有感觉了开始看LYAH的Functor, Applicative和Monad。
  - 看*RWH*的Monad部分，看得懂就多看几章，看不懂就先放着。
  - 接着可以刷 https://wiki.haskell.org/Tutorials#Using_monads 这个页面里Monad的一些paper和文章。
    - *How to build a monadic interpreter in one day*: 挺好的文章，写一个迷你的解释器，可看可不看，建议不看而去看Philip Wadler的两篇Monad的论文。
    - *Monad Transformers Explained*, *MonadCont under the hood*: 这两篇讲的是MT和CPS，建议弄懂Monad后再挑战这种更蛋疼的概念。
    - *IO inside*: 是讲IO Monad的，这种Monad比较简单，文章写得很好。
    - *The Haskell Programmer's Guide to the IO Monad - Don't Panic*: 没看，讲范畴论（category theory）的，不做研究不需要了解，建议等想深入了再学。
    - *Systematic Design of Monads*: 没看，不过看上去挺有趣的。
    - *All About Monads*: 可以看一部分，有些内容比较难，那些东西晚点看。
    - 还有一些文章我也没接触，大家可以挑自己感兴趣的看看。
  - 和上面可以同时进行的是刷[Philip Wadler](http://homepages.inf.ed.ac.uk/wadler/)关于Monad的论文，推荐*Monads for functional programming*和*The essence of functional programming*。
  - 感觉对Monad有一些掌握后可以尝试用Haskell写一个Lisp的解释器 https://en.wikibooks.org/wiki/Write_Yourself_a_Scheme_in_48_Hours 
- 接下来可以看看Penn的*CIS194*，这门课每年的在Monad之后讲的内容不一样，包括一些并发、GUI和类型推导理论里的最前沿的内容。可以看看然后做做里面的作业。
- 接着可以开始做*[NICTA course](https://github.com/NICTA/course)*，非常好的Haskell进阶课程，形式也很有意思。**（强烈推荐这个课程，里面的代码一定要尽量写完，非常有意思）**
- 这个阶段后看自己的兴趣选择喜欢的东西学学。

## 资料汇总

- https://en.wikibooks.org/wiki/Haskell
  - 很重要的参考资料，遇到不懂的概念都可以去看看
- https://www.haskell.org/onlinereport/haskell2010/
  - 语言标准
- Hoogle: https://www.haskell.org/hoogle/
  - 查Package和Package源代码的地方，Haskell的代码写得都非常漂亮，流行Package的作者又全是大神，没事可以多看看他们的代码。
- Stackage: https://www.stackage.org/
  - 另一个查Package和源代码的地方，我记得这边有时能查到一些Hoogle查不到的Package。
- https://wiki.haskell.org/Typeclassopedia
  - 介绍了各种Type，写得非常好，推荐。
- http://www.seas.upenn.edu/~cis194/resources.html
  - 很多有用的资料。
- http://stackoverflow.com/questions/1012573/getting-started-with-haskell
  - 不全，但给了些学习的路线图。
- http://www.zhihu.com/topic/19593103
  - 知乎的Haskell页面，挺多好的中文资料，但装逼犯（比如轮子）比较多。
  - http://www.zhihu.com/question/20193745/answer/37300535 推荐这个帖子
- http://www.scs.stanford.edu/14sp-cs240h/
  - S大的Haskell课程，偏工程，讲课的两位都是大神，助教也是大神。
- http://courses.cms.caltech.edu/cs11/material/haskell/
  - 加州理工的Haskell课程，比较老了，一些资料不错。
- https://wiki.haskell.org/Learning_Haskell 和 https://wiki.haskell.org/Tutorials
  - 官方的资料收集页面，很多好东西，可以玩一年。
- http://research.microsoft.com/en-us/um/people/simonpj/papers/papers.html
  - SPJ大神的主页，Haskell的创造者之一（Wadler也是），理论和码功都是大神级的。
- http://dotat.at/tmp/danvy-filinski-mscs92.pdf
  - 一篇关于CPS的论文，CPS算是一种古老的黑科技，Lisp, Haskell, JS, Scala, C++里面都有，但是个人觉得Lisp和Haskell用得最优雅。
- *The Little Schemer*
  - 王垠在印第安纳的老板写的scheme的书，介绍了CPS和Y Combinator，学Haskell可以参考。
- Idris 
  - http://www.idris-lang.org/
  - 一个通用的依赖类型的函数式编程语言，可以看做升级版的Haskell。
- Coq
  - https://coq.inria.fr/
  - 一个用来定理证明的依赖类型的函数式编程语言。
  - *[Software Foundations](https://www.cis.upenn.edu/~bcpierce/sf/current/index.html)*，programming language领域很著名的书，没看过也要知道名字=w=
  
## 编辑器

- Emacs: Haskell mode
- Spacemacs: Haskell Layer （我目前用的，基于Emacs的Haskell mode）
- Vim: Haskell mode
- Intellij: Haskell plugin

推荐Emacs，Vim没用过，但Emacs的Haskell mode比Vim的出名很多（带有bias），所以Emacs写Haskell应该更方便。IntelliJ不够智能，快捷键不好用，对cabal和stackage集成效果也不如Emacs。*（前几年的情况，现在不知道了）*

## Haskell安装

- 推荐使用Haskell官网的Haskell Platform。

*注：这个好像过时了，现在比较流行用stackage，毕竟cabal hell太讨厌了。*
