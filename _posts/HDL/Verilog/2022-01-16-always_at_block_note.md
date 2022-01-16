---
layout: post
title: Alwyas Blocks Note
date: 2022-01-16
update: 2022-01-16
category: "HDL"
tags: [Verilog]
author: yuz
comment: true
---

Verilog 提供两种描述电路的方式，一种是像画电路图一样描述电路，称为结构化描述，另一则是可以通过描述电路的行为生成电路，称为行为级描述。我们常常使用`always@`块去描述一些复杂电路的行为，尤其是那些事件触发的电路！`always@`块既可以描述组合逻辑，也可以描述时序逻辑，在描述的方式上略有不同，但无论如何，我们应该用数字逻辑电路去指导我们编码，这样自然而然地就可以避免`always@`中的一些坑。


#### always@块 框架

``` verilog
always @( sensitivity list ) begin
	elements
end
```

当感知列表满足条件时， elements则会更新。


#### 阻塞赋值语句和非阻塞赋值语句

`always@`块在信号的传播时两个十分重要的概念；阻塞赋值语句(blocking)`=`，非阻塞赋值语句(non-blocking)`<=`。注：只有在`always@`块中才会谈及这两个概念。

- 阻塞赋值语句`=`：Verilog会先描述完阻塞赋值语句后，再描述其下面的语句。
- 非阻塞赋值语句`<=`：Verilog会同时描述所有非阻塞赋值语句。

下面几小节将会看到它们的区别。


#### always@ 描述组合逻辑

组合逻辑是没有状态、无环路的。在`alwyas@`块中，组合电路的感知列表是**电平触发**的。组合逻辑的信号传递通常使用阻塞赋值语句(bloking) `=`，可以回想一下，我们绘制组合逻辑或者观察组合逻辑电路都是从左到右（或者信号是从电路的上游开始一直沿着路径流到下游，这个过程是应该被理解为阻塞的，即上游电路元素描述完后才会描述下游）。

组合逻辑中使用`<=`和`=`的差别是很微妙的（结果不会被影响）。举个例子：

```verilog
always @( * ) begin
	B = A;
	C = B;
	D = C;
end
```

```verilog
always @( * ) beign
	B <= A;
	C <= B;
	D <= C;
end
```

这两个代码片描述的电路都是：

A - > B  
A - > C  
A - > D

`>`是延时门。差别只在于verilog对它们的描述是并行执行还是顺序的。但为了符合规范，应该使用`=`。



#### always@ 描述时序逻辑

时序逻辑电路（通常指同步时序电路）是有状态的，所有时序元素共用一个同步时钟。组合逻辑的信号传递通常使用非阻塞赋值语句(non-bloking) `<=`。寄存器与寄存器之间的信号传播都是同时进行的。这里不再过多赘述，详细见[always@ blocks](https://inst.eecs.berkeley.edu/~eecs151/fa20/files/verilog/always_at_blocks.pdf)。



#### 生成锁存器(Latch)

用`always@`描述组合逻辑时，如果对一个元素的赋值没有在所有条件下都覆盖，Verilog就会推断出一个锁存器，这有可能不是我们希望看到的。下面是一个生成Latch的代码

```verilog
always @( * ) begin
    if (trigger) begin
    	A = Pass;
    end
end
/*
          _______
pass---> | latch |---->A
         |   c   |
          -------
             ^
 trigger_____|
*/
```

这会生成一个D-Latch，而非复用器。



#### 进一步阅读

[always@ blocks](https://inst.eecs.berkeley.edu/~eecs151/fa20/files/verilog/always_at_blocks.pdf)

[IEEE Standard for Verilog](https://www.eg.bucknell.edu/~csci320/2016-fall/wp-content/uploads/2015/08/verilog-std-1364-2005.pdf)
