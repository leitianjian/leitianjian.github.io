---
title: Database concepts
tags:[database, concepts]
key: DatabaseConcepts
---

## ACID

### 1. 原子性（Atomicity）

事务被视为不可分割的最小单元，事务的所有操作要么全部提交成功，要么全部失败回滚。

回滚可以用回滚日志（Undo Log）来实现，回滚日志记录着事务所执行的修改操作，在回滚时反向执行这些修改操作即可。

### 2. 一致性（Consistency）

> [Consistency](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/Consistency_(database_systems)) ensures that a transaction can **only bring the database from one valid state to another**, maintaining database [invariants](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/Invariant_(computer_science)): **any data written to the database must be valid according to all defined rules, including [constraints](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/Integrity_constraints), [cascades](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/Cascading_rollback),[triggers](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/Database_trigger), and any combination thereof.** This prevents database corruption by an illegal transaction, but does **not guarantee that a transaction is \*correct\*.**

我对这段话的理解：

- 数据库事务的一致性是指：保证事务只能把数据库从一个有效（正确）的状态“转移”到另一个有效（正确）的状态。那么，什么是数据库的有效(正确）的状态？满足给这个数据库pred-defined的一些规则的状态都是 valid 的。这些规则有哪些呢，比如说[constraints](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/Integrity_constraints), [cascades](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/Cascading_rollback),[triggers](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/Database_trigger) 及它们的组合等。具体到某个表的某个字段，比如你在定义表的时候，给这个字段的类型是number类型，并且它的值不能小于0，那么你在某个 transaction 中给这个字段插入（更改）为一个 String 值或者是负值是不可以的，这不是一个“合法”的transaction，也就是说它不满足我们给数据库定义的一些规则（约束条件）。
- “This prevents database corruption by an illegal transaction, but does **not guarantee that a transaction is \*correct\*. ”** 这又怎么理解呢？在数据库的角度来看，它只关心 transaction 符不符合定义好的规则，符合的就是legal的，不符合的就是illegal的。transaction 是否正确是从应用层的角度来看的，数据库并不知道你应用层的逻辑意义，它不保证应用层的transaction的正确性，这个逻辑正确性是由应用层的programmer来保证的。 这么说估计还是抽象，那么看下面我们熟知的转账的例子。

```text
Table： Account
Columns:   Name(string), Balance（int)
约束条件：无

执行下面一个事务(A,B的初始余额均为1000，A给B转账1200）

1.  往表Account插入数据（A,1000)
2. 往表Account插入数据 （B,1000)
3. A给B转账1200，更新A的余额为-200，（A,-200)
4. B的余额增加1200，更新B的余额为2200（B,2200)
```

那么，数据库会认为这个 transaction 合不合法呢？也就是它满不满足我们给数据库的定义的规则呢？答案就是这个 transaction 是合法的，因为你定义表的时候没有约定 Balance 不能小于0。虽然我们从应用层的角度来看，这个transaction是不正确的，因为它不符合逻辑- balance不能小于0. 但我们数据库只关心你的 transaction 满不满足你的数据库定义的rule，不关心它具有什么业务的逻辑，这个业务逻辑是应该由应用层来理解并处理的。

修改一下上面这个例子

```text
Table： Account
Columns:   Name(string), Balance（int)
约束条件：Balance >= 0

执行下面一个事务(A,B的初始余额均为1000，A给B转账1200）

1.  往表Account插入数据（A,1000)
2. 往表Account插入数据 （B,1000)
3. A给B转账1200，更新A的余额为-200，（A,-200)
4. B的余额增加1200，更新B的余额为2200（B,2200)
```

注意，这里增加了约束条件Balance > 0, 上面的这个transaction违反了规则Balance>=0，那么这个事务数据库认为它是非法的，不满足一致性的要求，所以数据库执行这个事务会失败。

最后请再认真研读一下链接 [Consistency (database systems) ](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/Consistency_(database_systems))中的这段话。

> This(Consistency）does not guarantee correctness of the transaction in all ways the application programmer might have wanted (that is the responsibility of application-level code) but merely that any programming errors cannot result in the violation of any defined database constraints.[1]

### 3. 隔离性（Isolation）

Why dirty read require no commit ?

### 4. 持久性（Durability）

一旦事务提交，则其所做的修改将会永远保存到数据库中。即使系统发生崩溃，事务执行的结果也不能丢失。

系统发生奔溃可以用重做日志（Redo Log）进行恢复，从而实现持久性。与回滚日志记录数据的逻辑修改不同，重做日志记录的是数据页的物理修改。


## References:
1. [CS-Note 数据库原理](https://cyc2018.github.io/CS-Notes/#/notes/%E6%95%B0%E6%8D%AE%E5%BA%93%E7%B3%BB%E7%BB%9F%E5%8E%9F%E7%90%86)
2. [如何理解数据库事务中的一致性的概念？ - sleep deep的回答 - 知乎](https://www.zhihu.com/question/31346392/answer/569142076)