# 基础知识 

[TOC]




本文大部分内容参考   **周志明《深入理解 Java 虚拟机》**  ，想要深入学习的话请看原书。

## 一、Java 基础

### 重写和重载的区别

- 重载：存在于同一个类中，指一个方法与已经存在的方法名称上相同，但是参数类型、个数、顺序至少有一个不同。

  应该注意的是，返回值不同，其它都相同不算是重载。（会报错）

- 存在于继承体系中，指子类实现了一个与父类在方法声明上完全相同的一个方法。

  为了满足里式替换原则，重写有以下三个限制：

  - 子类方法的访问权限必须大于等于父类方法；
  - 子类方法的返回类型必须是父类方法返回类型或为其子类型。
  - 子类方法抛出的异常类型必须是父类抛出异常类型或为其子类型。

### 面向对象三大特性

- 封装：封装把⼀个对象的属性私有化，同时提供⼀些可以被外界访问的属性的⽅法

- 继承：使⽤已存在的类作为基础建⽴新类。

  - ⼦类拥有⽗类对象所有的属性和⽅法（包括私有属性和私有⽅法），但是⽗类中的私有属性和⽅法⼦类是⽆法访问，只是拥有。 

  - ⼦类可以拥有⾃⼰属性和⽅法，即⼦类可以对⽗类进⾏扩展。 

  - 子类可以重写父类的方法。

- 多态：表示一个对象具有多种的状态。具体表现为父类的引用指向子类的实例。

  在 Java 中有两种形式可以实现多态：继承（多个⼦类对同⼀⽅法的重写）和接⼝（实现接⼝并覆盖接⼝中同⼀⽅法）。

### String StringBuffer 和 StringBuilder

- String 被声明为 final ，内部的 char 数组也被声明为 final 。所以 String 不可变。好处：1. 可以缓存 hash 值。2. 字符串常量池（String Pool）保存着所有字符串字面量（literal strings），如果一个 String 对象已经被创建过了，那么就会从 String Pool 中取得引用。只有 String 是不可变的，才可能使用 String Pool。3. 线程安全。
- StringBuffer 和 StringBuilder 可变。StringBuffer 线程安全，内部使用了 Synchronized 同步。

### 抽象类和接口的区别

1. 抽象类和抽象方法都使用 abstract 关键字进行声明。如果一个类中包含抽象方法，那么这个类必须声明为抽象类。

   抽象类和普通类最大的区别是，抽象类不能被实例化，只能被继承。

2. 从使用上来看，一个类可以实现多个接口，但是不能继承多个抽象类。

3. 接口的字段只能是 static 和 final 类型的，而抽象类的字段没有这种限制。

4. 接口的成员只能是 public 的，而抽象类的成员可以有多种访问权限。

#### 使用选择

使用接口：

- 需要让不相关的类都实现一个方法，例如不相关的类都可以实现 Comparable 接口中的 compareTo() 方法；
- 需要使用多重继承。

使用抽象类：

- 需要在几个相关的类中共享代码。
- 需要能控制继承来的成员的访问权限，而不是都为 public。
- 需要继承非静态和非常量字段。

在很多情况下，接口优先于抽象类。因为接口没有抽象类严格的类层次结构要求，可以灵活地为一个类添加行为。并且从 Java 8 开始，接口也可以有默认的方法实现，使得修改接口的成本也变的很低。

### == 与 equals

- 对于基本类型，== 判断两个值是否相等，基本类型没有 equals() 方法。
- 对于引用类型，== 判断两个变量是否引用同一个对象，Object().equals() 也是判断引用的对象是否等价。
  - 但 String 类中重写了equals() 方法用于比较两个字符串的内容是否相等。

### 深拷贝和浅拷贝

- 浅拷贝：只复制栈内的对象引用，不复制堆内的对象空间。原始对象和副本引用其实是指向了一个对象。Object.clone()是浅拷贝
- 深拷贝：既复制栈内的引用，又在堆内重新开辟一块内存空间进行复制

### 反射



## 二、Java 集合

### Arraylist 与 LinkedList 区别

ArrayList 基于动态数组实现，LinkedList 基于双向链表实现。ArrayList 和 LinkedList 的区别可以归结为数组和链表的区别：

- 数组支持随机访问，但插入删除的代价很高，需要移动大量元素；
- 链表不支持随机访问，但插入删除只需要改变指针。

#### ArrayList 的扩容机制

**默认长度**：
Object[]数组的默认长度是10。

**扩容时机**：
添加元素时使用 ensureCapacityInternal() 方法来保证容量足够，如果不够时，需要使用 grow() 方法进行扩容。(如果是空数组，则最小需要容量就是默认容量10)

**扩容操作**：

1. 获取旧容量。
2. 计算新容量是旧容量的1.5倍（这里采用的是移位运算）
3. 如果 新容量 小于 所需最小容量，那么将 所需最小容量 赋值给 新容量值。


扩容操作需要调用 Arrays.copyOf() 把原数组整个复制到新数组中，这个操作代价很高，因此最好在创建 ArrayList 对象时就指定大概的容量大小，减少扩容操作的次数。

### HashMap的底层实现

#### 存储结构

内部包含了一个 Entry 类型的数组 table。Entry 存储着键值对。它包含了四个字段：hashCode、key、value、next，从 next 字段我们可以看出 Entry 是一个链表。即数组中的每个位置被当成一个桶，一个桶存放一个链表。HashMap 使用拉链法来解决冲突，同一个链表中存放哈希值和散列桶取模运算结果相同的 Entry。

从 JDK 1.8 开始，一个桶存储的链表长度大于等于 8 时会将链表转换为红黑树。

#### 拉链法

- 计算键值对所在的桶；
- 在链表上顺序查找，时间复杂度显然和链表的长度成正比。

#### put操作

1. 调用key的hashCode方法计算哈希值，将哈希值对数组长度取模确定桶下标。这里用了与操作进行优化（因为数组长度总是2的整数次幂，可以通过位运算加快取模速度 ）
2. 如果当前的桶数组为null，则调用resize()方法进行初始化。
3. 如果没有发生哈希碰撞，则直接放到对应的桶中
4. 如果发生哈希碰撞，且key已经存在，就替换掉value。若key不存在，挂到树上或链表（1.7是头插法）上。
5. 链表长度超过8转换成红黑树。（红黑树是一颗自平衡的二叉树，插入数据时，有可能导致红黑树不平衡，这时需要翻转红黑树使它达到平衡状态。而8这个数字就是链表和数字在插入效率和查询效率上的折中处理，就如同HashMap的负载因子是0.75，而不是0.6或是0.8，是一个道理。）
6. 数据put完成后，如果HashMap的总数超过threshold就要resize

#### resize扩容

hashmap中有一个参数叫做负载因子，代表当hashmap容量使用率达到一定比率时触发扩容。
当map中包含的Entry的数量大于等于threshold = loadFactor（0.75） * capacity的时候，且新建的Entry刚好落在一个非空的桶上，此刻触发扩容机制，将其容量扩大为2倍。

扩容操作同样需要把 oldTable 的所有键值对重新插入 newTable 中，因此这一步是很费时的。

1. 重新计算桶下标

   在进行扩容时，需要把键值对重新计算桶下标，从而放到对应的桶上。在前面提到，HashMap 使用 hash%capacity 来确定桶下标。HashMap capacity 为 2 的 n 次方这一特点能够极大降低重新计算桶下标操作的复杂度。

   假设原数组长度 capacity 为 16，扩容之后 new capacity 为 32：

   ```
   capacity     : 00010000
   new capacity : 00100000
   ```

   对于一个 Key，它的哈希值 hash 在第 5 位：

   - 为 0，那么 hash%00010000 = hash%00100000，桶位置和原来一致；
   - 为 1，hash%00010000 = hash%00100000 + 16，桶位置是原位置 + 16。

#### HashMap 和 Hashtable 的区别

- Hashtable 使用 synchronized 来进行同步。
- HashMap 可以插入键为 null 的 Entry。
- HashMap 的迭代器是 fail-fast 迭代器。
- HashMap 不能保证随着时间的推移 Map 中的元素次序是不变的。

### ConcurrentHashMap

#### 1.7

ConcurrentHashMap 采用了分段锁（Segment），每个分段锁维护着几个桶（HashEntry），多个线程可以同时访问不同分段锁上的桶.Segment 继承自 ReentrantLock。

##### size 操作

每个 Segment 维护了一个 count 变量来统计该 Segment 中的键值对个数。

在执行 size 操作时，需要遍历所有 Segment 然后把 count 累计起来。

ConcurrentHashMap 在执行 size 操作时先尝试不加锁，如果连续两次不加锁操作得到的结果一致，那么可以认为这个结果是正确的。

如果尝试的次数超过 3 次，就需要对每个 Segment 加锁。

#### 1.8 

JDK 1.7 使用分段锁机制来实现并发更新操作，核心类为 Segment，它继承自重入锁 ReentrantLock，并发度与 Segment 数量相等。

JDK 1.8 使用了 CAS 操作来支持更高的并发度，在 CAS 操作失败时使用内置锁 synchronized 

并且 JDK 1.8 的实现也在链表过长时会转换为红黑树。

1. 使用volatile。Node中的val和next都被volatile关键字修饰。也就是说，我们改动val的值或者next的值对于其他线程是可见的。
2. put时，如果数组位置的Node为null，则通过自旋+CAS初始化节点，拿到节点后使用CAS写入，在 CAS 操作失败时使用内置锁 synchronized 。

##### 为什么使用红黑树

红黑树不追求AVL那样要求完全平衡，它只要求部分达到平衡，来换取增删节点时候旋转次数的降低，任何不平衡都会在三次旋转之内解决，而AVL是严格平衡树，因此在增加或者删除节点的时候，根据不同情况，旋转的次数比红黑树要多。性能和维护。

- 节点是黑色或者红色。
- 根节点是黑色。
- 叶节点是黑色。
- 红色节点的子节点是黑色。
- 一个节点到该节点的子孙节点的所有路径上包含相同数目的黑节点。

红黑规则确保了红黑树的关键特性：从根到叶子的最长的可能路径不多于最短的可能路径的两倍长。结果是这个树大致上是平衡的。

## 三、Spring

### IOC

IoC（Inverse of Control:控制反转）是一种**设计思想**，就是 **将原本在程序中手动创建对象的控制权，交由Spring框架来管理。** IoC 在其他语言中也有应用，并非 Spirng 特有。 **IoC 容器是 Spring 用来实现 IoC 的载体， IoC 容器实际上就是个Map（key，value）,Map 中存放的是各种对象。**

将对象之间的相互依赖关系交给 IoC 容器来管理，并由 IoC 容器完成对象的注入。这样可以很大程度上简化应用的开发，把应用从复杂的依赖关系中解放出来。 **IoC 容器就像是一个工厂一样，当我们需要创建一个对象的时候，只需要配置好配置文件/注解即可，完全不用考虑对象是如何被创建出来的。** 在实际项目中一个 Service 类可能有几百甚至上千个类作为它的底层，假如我们需要实例化这个 Service，你可能要每次都要搞清这个 Service 所有底层类的构造函数，这可能会把人逼疯。如果利用 IoC 的话，你只需要配置好，然后在需要的地方引用就行了，这大大增加了项目的可维护性且降低了开发难度。

Spring 时代我们一般通过 XML 文件来配置 Bean，后来开发人员觉得 XML 文件来配置不太好，于是 SpringBoot 注解配置就慢慢开始流行起来。

﻿

![img](https://images.xiaozhuanlan.com/photo/2019/57da0deca924d0e73dbb56501d2ec4be.png)

﻿

### AOP

AOP(Aspect-Oriented Programming:面向切面编程)能够将那些与业务无关，**却为业务模块所共同调用的逻辑或责任（例如事务处理、日志管理、权限控制等）封装起来**，便于**减少系统的重复代码**，**降低模块间的耦合度**，并**有利于未来的可拓展性和可维护性**。

**Spring AOP就是基于动态代理的**，如果要代理的对象，实现了某个接口，那么Spring AOP会使用**JDK Proxy**，去创建代理对象，而对于没有实现接口的对象，就无法使用 JDK Proxy 去进行代理了，这时候Spring AOP会使用**Cglib** ，这时候Spring AOP会使用 **Cglib** 生成一个被代理对象的子类来作为代理，如下图所示：
当然你也可以使用 AspectJ ,Spring AOP 已经集成了AspectJ ，AspectJ 应该算的上是 Java 生态系统中最完整的 AOP 框架了。

使用 AOP 之后我们可以把一些通用功能抽象出来，在需要用到的地方直接使用即可，这样大大简化了代码量。我们需要增加新功能时也方便，这样也提高了系统扩展性。日志功能、事务管理等等场景都用到了 AOP 。

https://blog.csdn.net/Challenger_/article/details/107247638?spm=1001.2014.3001.5501

#### AOP失效

*在同一个类中的方法，调用 aop注解（@Cacheable 注解@Transactional 注解也是aop 注解） 的方法，会使aop 注解失效 
*spring AOP 使用Java动态代理和 cglib 代理 来创建AOP代理，没有接口的类 使用cglib 代理。
当方法被代理时，其实是 动态生成了一个代理对象，代理对象去执行 invoke方法，在调用 被代理对象的方法的时候执行了一些其他的动作。

所以当在被代理对象的方法中调用被代理对象的其他方法时。其实是没有用代理调用，是用了被代理对象本身调用的。

buyTrainTicket(Ticket ticket)方法时，spring 的动态代理已经帮我们动态生成了一个代理的对象，暂且我就叫他 $TicketService1。

所以调用buyTrainTicket(Ticket ticket) 方法实际上是代理对象$TicketService1调用的。$TicketService1.buyTrainTicket(Ticket ticket)

但是在buyTrainTicket 方法内调用同一个类的另外一个注解方法sendMessage()时，实际上是this.sendMessage() 这个this 指的是TicketService 对象，并不是$TicketService1 代理对象，没有走代理。所以 注解失效。

所以在同一个类的方法中调用其他注解方法，应该使用代理对象 调用。应该获取当前代理对象，*通过代理对象去调用方法*。
最好的解决方案就是避免在对象内部调用

https://blog.csdn.net/u012373815/article/details/77345655

#### Spring AOP 和 AspectJ AOP 有什么区别？

Spring AOP 属于运行时增强，而 AspectJ 是编译时增强。Spring AOP 基于代理(Proxying)，而 AspectJ 基于字节码操作(Bytecode Manipulation)。

Spring AOP 已经集成了 AspectJ ，AspectJ 应该算的上是 Java 生态系统中最完整的 AOP 框架了。AspectJ 相比于 Spring AOP 功能更加强大，但是 Spring AOP 相对来说更简单，

如果我们的切面比较少，那么两者性能差异不大。但是，当切面太多的话，最好选择 AspectJ ，它比Spring AOP 快很多。

### 注解

一、**注解的基本概念和原理及其简单实用**

注解（Annotation）提供了一种安全的类似注释的机制，为我们在代码中添加信息提供了一种形式化得方法，使我们可以在稍后某个时刻方便的使用这些数据（通过解析注解来使用这些数据），用来将任何的信息或者元数据与程序元素（类、方法、成员变量等）进行关联。其实就是更加直观更加明了的说明，这些说明信息与程序业务逻辑没有关系，并且是供指定的工具或框架使用的。Annotation像一种修饰符一样，应用于包、类型、构造方法、方法、成员变量、参数及本地变量的申明语句中。

Annotation其实是一种接口。通过[Java](http://lib.csdn.net/base/17)的反射机制相关的API来访问Annotation信息。相关类（框架或工具中的类）根据这些信息来决定如何使用该程序元素或改变它们的行为。Java语言解释器在工作时会忽略这些Annotation，因此在JVM中这些Annotation是“不起作用”的，只能通过配套的工具才能对这些Annotation类型的信息进行访问和处理。

Annotation和interface的异同：

1、 annotition的类型使用关键字@interface而不是interface。它继承了java.lang.annotition.Annotition接口，并非申明了一个interface。



2、 Annotation类型、方法定义是独特的、受限制的。Annotation类型的方法必须申明为无参数、无异常抛出的。这些方法定义了Annotation的成员：方法名称为了成员名，而方法返回值称为了成员的类型。而方法返回值必须为primitive类型、Class类型、枚举类型、Annotation类型或者由前面类型之一作为元素的一位数组。方法的后面可以使用default和一个默认数值来申明成员的默认值，null不能作为成员的默认值，这与我们在非Annotation类型中定义方法有很大不同。Annotation类型和他的方法不能使用Annotation类型的参数，成员不能是generic。只有返回值类型是Class的方法可以在Annotation类型中使用generic，因为此方法能够用类转换将各种类型转换为Class。



3、 Annotation类型又与接口有着近似之处。它们可以定义常量、静态成员类型（比如枚举类型定义）。Annotation类型也可以如接口一般被实现或者继承。



***元注解@Target,@Retention,@Documented,@Inherited

***
\* @Target 表示该注解用于什么地方，
\* @Retention 表示在什么级别保存该注解信息。
*
\* @Documented 将此注解包含在 javadoc 中
*
\* @Inherited 允许子类继承父类中的注解
注解本身不做任何事情，只是像xml文件一样起到配置作用。注解代表的是某种业务意义，注解背后处理器的工作原理如上源码实现：首先解析所有属性，判断属性上是否存在指定注解，如果存在则根据搜索规则取得bean，然后利用反射原理注入。如果标注在字段上面，也可以通过字段的反射技术取得注解，根据搜索规则取得bean，然后利用反射技术注入

### Bean 的生命周期

### Spring解决循环依赖

先去缓存里找Bean，没有则实例化当前的Bean放到Map，如果有需要依赖当前Bean的，就能从Map取到。

## 四、Git



## 五、Linux

- netstat -tln | grep 8080 查看端口8080的使用情况
- ps aux|grep java 查看java进程
- ps aux 查看所有进程
- netstat 查看网络状态

```objectivec
LISTEN：侦听来自远方的TCP端口的连接请求

SYN-SENT：再发送连接请求后等待匹配的连接请求

SYN-RECEIVED：再收到和发送一个连接请求后等待对方对连接请求的确认

ESTABLISHED：代表一个打开的连接

FIN-WAIT-1：等待远程TCP连接中断请求，或先前的连接中断请求的确认

FIN-WAIT-2：从远程TCP等待连接中断请求

CLOSE-WAIT：等待从本地用户发来的连接中断请求

CLOSING：等待远程TCP对连接中断的确认

LAST-ACK：等待原来的发向远程TCP的连接中断请求的确认

TIME-WAIT：等待足够的时间以确保远程TCP接收到连接中断请求的确认

CLOSED：没有任何连接状态
  
```

```ruby
统计80端口的连接数据

netstat -nat | grep -i "80" | wc -l

统计httpd协议连接数

ps -ef | grep httpd | wc -l

统计已连接的，状态为establish的

netstat -na | greo ESTABLISH | wc -l

查出那个IP连接最多，并将其封掉

netstat -na | grep ESTABLISH | awk {print $5} | awk -F:{print $1}| sort | uniq -c | sort -r +On

查看apache当前并发访问数

netstat -na | grep ESTABLIS | wc -l

查看有多少个进程数

ps -aux | grep httpd | wc -l

查看Apache的并发请求数及其TCP的连接状态

netstat -nt | awk ‘{++S[$NF]}END{for(a in S) print a,S[a]}‘

SYN_RECV表示正在等待处理的请求数；ESTABLISHED表示正常数据传输状态；TIME_WAIT表示处理完毕，等待超时结束的请求数。
　　状态：描述

　　CLOSED：无连接是活动 的或正在进行

　　LISTEN：服务器在等待进入呼叫

　　SYN_RECV：一个连接请求已经到达，等待确认

　　SYN_SENT：应用已经开始，打开一个连接

　　ESTABLISHED：正常数据传输状态

　　FIN_WAIT1：应用说它已经完成

　　FIN_WAIT2：另一边已同意释放

　　ITMED_WAIT：等待所有分组死掉

　　CLOSING：两边同时尝试关闭

　　TIME_WAIT：另一边已初始化一个释放

　　LAST_ACK：等待所有分组死掉
```

- 查看内存使用情况top

PID：进程的ID
 USER：进程所有者
 PR：进程的优先级别，越小越优先被执行
 NInice：值
 VIRT：进程占用的虚拟内存
 RES：进程占用的物理内存
 SHR：进程使用的共享内存
 S：进程的状态。S表示休眠，R表示正在运行，Z表示僵死状态，N表示该进程优先值为负数
 %CPU：进程占用CPU的使用率
 %MEM：进程使用的物理内存和总内存的百分比
 TIME+：该进程启动后占用的总的CPU时间，即占用CPU使用时间的累加值。
 COMMAND：进程启动命令名称

- 查看是否有僵尸进程



```undefined
ps -aux | grep defunct
```

- 得到进程的ID及其父进程ID:



```bash
ps -ef | grep 'defunct\|PID' | more
```

> ![img](https:////upload-images.jianshu.io/upload_images/9570401-2170c5ffebd3211f.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)
>
> 如图

###### PPID为父进程

- 杀掉进程



```bash
### 清理僵尸进程
#### 父进程
ps  -ef | grep 'defunct' | grep -v grep | awk '{print $3}' | xargs kill -9
#### 子进程
ps  -ef | grep 'defunct' | grep -v grep | awk '{print $2}' | xargs kill -9
```

### 日志

Linux查看日志的命令有多种: tail、cat、tac、head、echo等，本文只介绍几种常用的方法。

## 1、tail

这个是我最常用的一种查看方式

```js
命令格式: tail[必要参数][选择参数][文件]
-f 循环读取
-q 不显示处理信息
-v 显示详细的处理信息
-c<数目> 显示的字节数
-n<行数> 显示行数
-q, --quiet, --silent 从不输出给出文件名的首部
-s, --sleep-interval=S 与-f合用,表示在每次反复的间隔休眠S秒
```

用法如下：

```js
tail  -n  10   test.log   查询日志尾部最后10行的日志;
tail  -n +10   test.log   查询10行之后的所有日志;
tail  -fn 1000   test.log   循环实时查看最后1000行记录(最常用的)
```

一般还会配合着grep用，例如 :

```js
 tail -fn 1000 test.log | grep '关键字'
```

如果一次性查询的数据量太大,可以进行翻页查看，例如:

```js
tail -n 4700  aa.log |more -1000 可以进行多屏显示(ctrl + f 或者 空格键可以快捷键)
```

## 2、head

跟tail是相反的head是看前多少行日志

```js
head -n  10  test.log   查询日志文件中的头10行日志;
head -n -10  test.log   查询日志文件除了最后10行的其他所有日志;
```

head其他参数参考tail

## 3、cat

cat 是由第一行到最后一行连续显示在屏幕上

一次显示整个文件 :

```js
 $ cat filename
```

从键盘创建一个文件 :

```js
$cat > filename
```

将几个文件合并为一个文件：

```js
$cat file1 file2 > file 只能创建新文件,不能编辑已有文件.
```

将一个日志文件的内容追加到另外一个 :

```js
$cat -n textfile1 > textfile2
```

清空一个日志文件:

```js
$cat : >textfile2
```

注意：`>` 意思是创建，`>>`是追加。千万不要弄混了。

```
cat`其他参数参考`tail
```

## 4、more

`more`命令是一个基于`vi`编辑器文本过滤器，它以全屏幕的方式按页显示文本文件的内容，支持vi中的关键字定位操作。`more`名单中内置了若干快捷键，常用的有H（获得帮助信息），`Enter`（向下翻滚一行），空格（向下滚动一屏），`Q`（退出命令）。`more`命令从前向后读取文件，因此在启动时就加载整个文件。

该命令一次显示一屏文本，满屏后停下来，并且在屏幕的底部出现一个提示信息，给出至今己显示的该文件的百分比：`–More–（XX%）`

`more`的语法：`more` 文件名

`Enter` 向下`n`行，需要定义，默认为1行

`Ctrl f` 向下滚动一屏

空格键 向下滚动一屏

`Ctrl b` 返回上一屏

`=` 输出当前行的行号

`:f` 输出文件名和当前行的行号

`v` 调用vi编辑器

`!`命令 调用`Shell`，并执行命令

```
q`退出`more
```

## 5、sed

这个命令可以查找日志文件特定的一段 , 根据时间的一个范围查询，可以按照行号和时间范围查询

**按照行号**

```js
sed -n '5,10p' filename 这样你就可以只查看文件的第5行到第10行。
```

**按照时间段**

```js
 sed -n '/2014-12-17 16:17:20/,/2014-12-17 16:17:36/p'  test.log
```

## 6、less

less命令在查询日志时，一般流程是这样的

```js
less log.log

shift + G 命令到文件尾部  然后输入 ？加上你要搜索的关键字例如 ？1213

按 n 向上查找关键字

shift+n  反向查找关键字
less与more类似，使用less可以随意浏览文件，而more仅能向前移动，不能向后移动，而且 less 在查看之前不会加载整个文件。
less log2013.log 查看文件
ps -ef | less   ps查看进程信息并通过less分页显示
history | less   查看命令历史使用记录并通过less分页显示
less log2013.log log2014.log   浏览多个文件
```

常用命令参数：

```js
less与more类似，使用less可以随意浏览文件，而more仅能向前移动，不能向后移动，而且 less 在查看之前不会加载整个文件。
less log2013.log 查看文件
ps -ef | less   ps查看进程信息并通过less分页显示
history | less   查看命令历史使用记录并通过less分页显示
less log2013.log log2014.log   浏览多个文件
常用命令参数：
-b <缓冲区大小> 设置缓冲区的大小
-g 只标志最后搜索的关键词
-i 忽略搜索时的大小写
-m 显示类似more命令的百分比
-N 显示每行的行号
-o <文件名> 将less 输出的内容在指定文件中保存起来
-Q 不使用警告音
-s 显示连续空行为一行
/字符串：向下搜索"字符串"的功能
?字符串：向上搜索"字符串"的功能
n：重复前一个搜索（与 / 或 ? 有关）
N：反向重复前一个搜索（与 / 或 ? 有关）
b 向后翻一页
h 显示帮助界面
q 退出less 命令
```

## 一般本人查日志配合应用的其他命令

```js
history // 所有的历史记录

history | grep XXX  // 历史记录中包含某些指令的记录

history | more // 分页查看记录

history -c // 清空所有的历史记录

!! 重复执行上一个命令

查询出来记录后选中 :　!323
```

## linux日志文件说明

```js
/var/log/message 系统启动后的信息和错误日志，是Red Hat Linux中最常用的日志之一
/var/log/secure 与安全相关的日志信息
/var/log/maillog 与邮件相关的日志信息
/var/log/cron 与定时任务相关的日志信息
/var/log/spooler 与UUCP和news设备相关的日志信息
/var/log/boot.log 守护进程启动和停止相关的日志消息
/var/log/wtmp 该日志文件永久记录每个用户登录、注销及系统的启动、停机的事件
```

## 七、设计模式

### 单例模式

确保一个类只有一个实例，并提供该实例的全局访问点。

#### 懒汉式

#### 线程不安全

以下实现中，私有静态变量 uniqueInstance 被延迟实例化，这样做的好处是，如果没有用到该类，那么就不会实例化 uniqueInstance，从而节约资源。

这个实现在多线程环境下是不安全的，如果多个线程能够同时进入 if (uniqueInstance == null) ，并且此时 uniqueInstance 为 null，那么会有多个线程执行 uniqueInstance = new Singleton(); 语句，这将导致实例化多次 uniqueInstance。

```java
public class Singleton {

  private static Singleton uniqueInstance;

  private Singleton() {
  }

  public static Singleton getUniqueInstance() {
    if (uniqueInstance == null) {
      uniqueInstance = new Singleton();
    }
    return uniqueInstance;
  }
}
```

#### 饿汉式-线程安全

线程不安全问题主要是由于 uniqueInstance 被实例化多次，采取直接实例化 uniqueInstance 的方式就不会产生线程不安全问题。

但是直接实例化的方式也丢失了延迟实例化带来的节约资源的好处。

```java
private static Singleton uniqueInstance = new Singleton();
```

#### 懒汉式-线程安全

只需要对 getUniqueInstance() 方法加锁，那么在一个时间点只能有一个线程能够进入该方法，从而避免了实例化多次 uniqueInstance。

但是当一个线程进入该方法之后，其它试图进入该方法的线程都必须等待，即使 uniqueInstance 已经被实例化了。这会让线程阻塞时间过长，因此该方法有性能问题，不推荐使用。

```java
public static synchronized Singleton getUniqueInstance() {
  if (uniqueInstance == null) {
    uniqueInstance = new Singleton();
  }
  return uniqueInstance;
}

```

#### 双重校验锁-线程安全

uniqueInstance 只需要被实例化一次，之后就可以直接使用了。加锁操作只需要对实例化那部分的代码进行，只有当 uniqueInstance 没有被实例化时，才需要进行加锁。

双重校验锁先判断 uniqueInstance 是否已经被实例化，如果没有被实例化，那么才对实例化语句进行加锁。

```java
public class Singleton {

  private volatile static Singleton uniqueInstance;

  private Singleton() {
  }

  public static Singleton getUniqueInstance() {
    if (uniqueInstance == null) {
      synchronized (Singleton.class) {
        if (uniqueInstance == null) {
          uniqueInstance = new Singleton();
        }
      }
    }
    return uniqueInstance;
  }
}
```

考虑下面的实现，也就是只使用了一个 if 语句。在 uniqueInstance == null 的情况下，如果两个线程都执行了 if 语句，那么两个线程都会进入 if 语句块内。**虽然在 if 语句块内有加锁操作，但是两个线程都会执行 uniqueInstance = new Singleton(); 这条语句，只是先后的问题**，那么就会进行两次实例化。因此必须使用双重校验锁，也就是需要使用两个 if 语句：第一个 if 语句用来避免 uniqueInstance 已经被实例化之后的加锁操作，而第二个 if 语句进行了加锁，所以只能有一个线程进入，就不会出现 uniqueInstance == null 时两个线程同时进行实例化操作。

```java
if (uniqueInstance == null) {
  synchronized (Singleton.class) {
    uniqueInstance = new Singleton();
  }
}
```

uniqueInstance 采用 volatile 关键字修饰也是很有必要的， uniqueInstance = new Singleton(); 这段代码其实是分为三步执行：

1. 为 uniqueInstance 分配内存空间
2. 初始化 uniqueInstance
3. 将 uniqueInstance 指向分配的内存地址

但是由于 JVM 具有指令重排的特性，执行顺序有可能变成 1>3>2。指令重排在单线程环境下不会出现问题，但是在多线程环境下会导致一个线程获得还没有初始化的实例。例如，线程 T1 执行了 1 和 3，此时 T2 调用 getUniqueInstance() 后发现 uniqueInstance 不为空，因此返回 uniqueInstance，但此时 uniqueInstance 还未被初始化。

使用 volatile 可以禁止 JVM 的指令重排，保证在多线程环境下也能正常运行。

### spring中用了那些设计模式

- **工厂设计模式** : Spring使用工厂模式通过 BeanFactory、ApplicationContext 创建 bean 对象。

  工厂模式的好处：

- **代理设计模式** : Spring AOP 功能的实现。

- **单例设计模式** : Spring 中的 Bean 默认都是单例的。

- **模板方法模式** : Spring 中 jdbcTemplate、hibernateTemplate 等以 Template 结尾的对数据库操作的类，它们就使用到了模板模式。

  模版模式的好处

- **包装器设计模式** : 我们的项目需要连接多个数据库，而且不同的客户在每次访问中根据需要会去访问不同的数据库。这种模式让我们可以根据客户的需求能够动态切换不同的数据源。

- **观察者模式:** Spring 事件驱动模型就是观察者模式很经典的一个应用。

- **适配器模式** :Spring AOP 的增强或通知(Advice)使用到了适配器模式、spring MVC 中也是用到了适配器模式适配Controller。

- **构造器模式**：为了灵活构造复杂对象，该对象会有多个成员变量，在外部调用的时候，不需要或者不方便一次性创建出所有的成员变量，在这种情况下，使用多个构造方法去构建对象，很难维护，这时候Builder设计模式解决这个问题，进行buid()方法中创建对象，并且将builder传入，该builder中，维护了传入对象的成员变量。

### rpc中用了那些设计模式

责任链模式




责任链模式在Dubbo中发挥的作用举足轻重，就像是Dubbo框架的骨架。Dubbo的调用链组织是用责任链模式串连起来的。责任链中的每个节点实现Filter接口，然后由ProtocolFilterWrapper，将所有Filter串连起来。Dubbo的许多功能都是通过Filter扩展实现的，比如监控、日志、缓存、安全、telnet以及RPC本身都是。如果把Dubbo比作一列火车，责任链就像是火车的各车厢，每个车厢的功能不同。如果需要加入新的功能，增加车厢就可以了，非常容易扩展。




观察者模式




Dubbo中使用观察者模式最典型的例子是RegistryService。消费者在初始化的时候回调用subscribe方法，注册一个观察者，如果观察者引用的服务地址列表发生改变，就会通过NotifyListener通知消费者。此外，Dubbo的InvokerListener、ExporterListener 也实现了观察者模式，只要实现该接口，并注册，就可以接收到consumer端调用refer和provider端调用export的通知。Dubbo的注册/订阅模型和观察者模式就是天生一对。




修饰器模式




Dubbo中还大量用到了修饰器模式。比如ProtocolFilterWrapper类是对Protocol类的修饰。在export和refer方法中，配合责任链模式，把Filter组装成责任链，实现对Protocol功能的修饰。其他还有ProtocolListenerWrapper、 ListenerInvokerWrapper、InvokerWrapper等。个人感觉，修饰器模式是一把双刃剑，一方面用它可以方便地扩展类的功能，而且对用户无感，但另一方面，过多地使用修饰器模式不利于理解，因为一个类可能经过层层修饰，最终的行为已经和原始行为偏离较大。




工厂方法模式




CacheFactory的实现采用的是工厂方法模式。CacheFactory接口定义getCache方法，然后定义一个AbstractCacheFactory抽象类实现CacheFactory，并将实际创建cache的createCache方法分离出来，并设置为抽象方法。这样具体cache的创建工作就留给具体的子类去完成。




抽象工厂模式




ProxyFactory及其子类是Dubbo中使用抽象工厂模式的典型例子。ProxyFactory提供两个方法，分别用来生产Proxy和Invoker（这两个方法签名看起来有些矛盾，因为getProxy方法需要传入一个Invoker对象，而getInvoker方法需要传入一个Proxy对象，看起来会形成循环依赖，但其实两个方式使用的场景不一样）。AbstractProxyFactory实现了ProxyFactory接口，作为具体实现类的抽象父类。然后定义了JdkProxyFactory和JavassistProxyFactory两个具体类，分别用来生产基于jdk代理机制和基于javassist代理机制的Proxy和Invoker。




适配器模式




为了让用户根据自己的需求选择日志组件，Dubbo自定义了自己的Logger接口，并为常见的日志组件（包括jcl, jdk, log4j, slf4j）提供相应的适配器。并且利用简单工厂模式提供一个LoggerFactory，客户可以创建抽象的Dubbo自定义Logger，而无需关心实际使用的日志组件类型。在LoggerFactory初始化时，客户通过设置系统变量的方式选择自己所用的日志组件，这样提供了很大的灵活性。




代理模式




Dubbo consumer使用Proxy类创建远程服务的本地代理，本地代理实现和远程服务一样的接口，并且屏蔽了网络通信的细节，使得用户在使用本地代理的时候，感觉和使用本地服务一样。



### java容器中的设计模式

**
迭代器模式
**Collection 继承了 Iterable 接口，其中的 iterator() 方法能够产生一个 Iterator 对象，通过这个对象就可以迭代遍历 Collection 中的元素。



从 JDK 1.5 之后可以使用 foreach 方法来遍历实现了 Iterable 接口的聚合对象。



```
List<String> list = new ArrayList<>();
list.add("a");
list.add("b");
for (String item : list) {
    System.out.println(item);
}
```

**
适配器模式
**

java.util.Arrays#asList() 可以把数组类型转换为 List 类型。



```
@SafeVarargs
public static <T> List<T> asList(T... a)
```

应该注意的是 asList() 的参数为泛型的变长参数，不能使用基本类型数组作为参数，只能使用相应的包装类型数组。



```
Integer[] arr = {1, 2, 3};
List list = Arrays.asList(arr);
```

也可以使用以下方式调用 asList()：



```
List list = Arrays.asList(1, 2, 3);
```



### 策略模式

策略模式主要是对方法的封装，把一系列方法封装到一系列的策略类中，客户端可以根据不同情况选择使用适宜的策略类，不同的策略类可以自由切换。

**策略模式**是一种行为设计模式， 它能让你定义一系列算法， 并将每种算法分别放入独立的类中， 以使算法的对象能够相互替换。
**
 实现方式
**

1. 从上下文类中找出修改频率较高的算法 （也可能是用于在运行时选择某个算法变体的复杂条件运算符）。

   

2. 
   声明该算法所有变体的通用策略接口。

   

3. 
   将算法逐一抽取到各自的类中， 它们都必须实现策略接口。

   

4. 
   在上下文类中添加一个成员变量用于保存对于策略对象的引用。 然后提供设置器以修改该成员变量。 上下文仅可通过策略接口同策略对象进行交互， 如有需要还可定义一个接口来让策略访问其数据。

   

5. 
   客户端必须将上下文类与相应策略进行关联， 使上下文可以预期的方式完成其主要工作。



### 观察者模式

当对象间存在一对多关系时，则使用观察者模式（Observer Pattern）。比如，当一个对象被修改时，则会自动通知依赖它的对象。观察者模式属于行为型模式。

**介绍**

**意图：**定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被自动更新。

**主要解决：**一个对象状态改变给其他对象通知的问题，而且要考虑到易用和低耦合，保证高度的协作。

**何时使用：**一个对象（目标对象）的状态发生改变，所有的依赖对象（观察者对象）都将得到通知，进行广播通知。

**如何解决：**使用面向对象技术，可以将这种依赖关系弱化。

**关键代码：**在抽象类里有一个 ArrayList 存放观察者们。

**应用实例：** 1、拍卖的时候，拍卖师观察最高标价，然后通知给其他竞价者竞价。 2、西游记里面悟空请求菩萨降服红孩儿，菩萨洒了一地水招来一个老乌龟，这个乌龟就是观察者，他观察菩萨洒水这个动作。

**优点：** 1、观察者和被观察者是抽象耦合的。 2、建立一套触发机制。

**缺点：** 1、如果一个被观察者对象有很多的直接和间接的观察者的话，将所有的观察者都通知到会花费很多时间。 2、如果在观察者和观察目标之间有循环依赖的话，观察目标会触发它们之间进行循环调用，可能导致系统崩溃。 3、观察者模式没有相应的机制让观察者知道所观察的目标对象是怎么发生变化的，而仅仅只是知道观察目标发生了变化。

**使用场景：**

- 一个抽象模型有两个方面，其中一个方面依赖于另一个方面。将这些方面封装在独立的对象中使它们可以各自独立地改变和复用。
- 一个对象的改变将导致其他一个或多个对象也发生改变，而不知道具体有多少对象将发生改变，可以降低对象之间的耦合度。
- 一个对象必须通知其他对象，而并不知道这些对象是谁。
- 需要在系统中创建一个触发链，A对象的行为将影响B对象，B对象的行为将影响C对象……，可以使用观察者模式创建一种链式触发机制。

**注意事项：** 1、JAVA 中已经有了对观察者模式的支持类。 2、避免循环引用。 3、如果顺序执行，某一观察者错误会导致系统卡壳，一般采用异步方式。