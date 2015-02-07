---
title: Haskell并行编程笔记（1）
layout: post
date:   2015-02-07 10:47:01
categories: Hasekll
---


最近读过了Haskell并发编程，一本很有趣的书。整体分为两个部分，前半部分关于如何使用相对高级的库对数据结构进行并行操作，而后半部分主要关于束河使用Haskell相关的线程机制和数据分享机制。

而谈起Haskell，永远避免不了的就是Monad。Monad是种极其强大的原语，也是保证许多特性可以简单而优雅的实现的根本，比如软件事物内存(STM)。

Haskell的软件事物内存与Clojure的Ref相似，都是通过包在一层特定的执行环境内，来对多个共享状态进行操作。最基本的数据结构是TVar a，一种可以存储a类型数据的容器。而所有对TVar的操作，都包含在STM这个Monad之中，比如说，我们可以做到。

	op = do
		a <- takeTVar tvar
		putTVar tvar (a + 1)

之类的操作，而op的类别则是`STM`。而STM操作，则可以被转化为`IO` Monad，通过`atomically`函数。