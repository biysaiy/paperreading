# Automating Failure Testing Research at Internet Scale

这篇文章主要讲了Netflix公司在混沌工程上的一项研究和实践成果. Netflix中有一群非常著名的猴子(chaos monkey), 他们随机注入错误来帮助系统发现潜在的可用性问题. 关于如何注入错误, 在Netflix使用了两种方法:

1. 随机搜索. 这种方法虽然简单, 但是效果不高, 很可能无法发现深层次问题. 如果一个系统有100个子服务, 那么就意味着2的100次方的搜索空间.
2. 程序员指定规则. 在Netflix每个服务都有对应的owenr, 借用他们的领域知识, 可以制定出复杂的规则来生成深层次故障注入. 但是这种方法无法扩展, 需要每个服务owner的专业领域知识介入才行.

这篇文章反其道而行之. 假设我们已经掌握了整体系统的所有信息, 当获取到正确的结果之后, 我们反问正确的结果是怎么获取到的, 言外之意就是如何获取不到正确的结果? 通过不断的追问, 我们可以揭示出各种不同的冗余路径来获取正确的结果. 这些路径就是我们可以考虑注入故障的地方, 叫做lineage-driven fault injection(LDFI).

![](https://github.com/elithnever/paperreading/blob/master/images/netflix/2018-08-20%20at%206.46%20PM.png)

## Lineage-driven Fault Injection
![](https://github.com/elithnever/paperreading/blob/master/images/netflix/2018-08-20%20at%206.47%20PM.png)

以这个图所示的服务为例, 所有的调用路径一共有2的5次方, 也就是32条, 通过每次调用产生的正确或者错误结果, 来求解这个系统的关键路径. 文章中使用Boolean编码来形式化整个过程. 以下图为例说明:

![](https://github.com/elithnever/paperreading/blob/master/images/netflix/2018-08-20%20at%206.49%20PM.png)
1. 每行代表产生正确结果的必要条件
2. 行与行之间代表可以产生结果的并行条件. 也就是说单独运行每行都可以产生正确的结果.

所以经过化简之后, RepA与RepB和Bcast1与Bcast2就是关键路径, 故障注入应该发生在他们之间, 但是不能同时发生. 实际运行过程中, 如果注入故障之后, 不能得到正确的结果, 那么就说明系统的容错性有问题了.

## 总结
这篇论文是针对故障注入更高级的运用了, 基于callgraph信息可以实现更精细化的故障注入能力, 从而让系统更加健壮.
