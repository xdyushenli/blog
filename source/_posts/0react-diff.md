---
title: React 中的 Diffing 算法
date: 2019-12-15 13:58:55
tags: [ react ]
---
# 概述
在某一时间点调用 React 的 `render()` 方法, 会创建一棵由 React 元素组成的树。在下一次 state 或 props 更新时, `render()` 方法会返回一棵不同的树。React 需要基于这两棵树之间的差别来判断如何有效率地更新 UI 以保证当前 UI 和最新的树保持一致。
这个问题本质上是生成一棵树转换成另一棵树的最小操作数。然而, 即使在最前沿的算法中, 该算法的复杂度都是 O(n^3), 其中 n 是树中元素的数量。
为了改善这一行为, React 在以下两个假设的基础上提出了一套

# 对比不同类型的DOM元素
# 对比同一类型的DOM元素
# 对比不同类型的组件元素

# 笔者说

# 参考资料
[https://react.docschina.org/docs/reconciliation.html](https://react.docschina.org/docs/reconciliation.html)
