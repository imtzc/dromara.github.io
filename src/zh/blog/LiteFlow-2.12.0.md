---
title: 决策路由特性来袭，LiteFlow大版本2.12.0发布，Make your code amaing！
author: 铂赛东
date: 2024-04-16
cover: /assets/img/blog/LiteFlow-2.12.0-0.png
head:
  - - meta
    - name: 博客
---

![](/assets/img/blog/LiteFlow-2.12.0-0.png)

**LiteFlow是一个开源编排式规则引擎，能够让你的系统逻辑任意编排，可选用脚本书写逻辑，支持多达7种脚本语言，支持丰富的第三方存储的支持，所有的逻辑和规则均可热变更。设计系统和重构系统的神器。**

LiteFlow是Gitee的高star项目，截止到发此文章的时候，Gitee star接近6k大关，Github则拥有2.6Kstar。

同时LiteFlow也是国内优秀的社区驱动型开源项目，开源3年多，目前已经被各大一线公司应用在核心系统上，据不完全统计，国内将近千余家公司都在使用。特性以及支持度都非常好。社区人数超过5000人。测试用例将近1800个，质量有保障。

如果你是第一次知道这个项目，可以去官网或相关的主页进行了解：

> 项目官网:
> 
> https://liteflow.cc
> 
> gitee托管仓库：
> 
> https://gitee.com/dromara/liteFlow
> 
> github托管仓库：
> 
> https://github.com/dromara/liteflow

以下文章LiteFlow简称为LF。

## 前言

距离上次版本已经过去3个月了，这次我们带来了v2.12.0的超大版本更新。这个版本本来准备3月底发布的，中间跳过两次票，一切只为了版本的更加稳定。让各位开发者久等了。

今年能明显感觉使用LF的小伙伴越来越多了，社区的人数也是节节攀升，每天的咨询不断。相信有不少人都在等这个版本，这个版本带来了19个issue的更新，其中包含着非常多的大特性更新，同时也有稳定性的更新和社区反映的一些bug修复。

这三个月中我和LF团队的小伙伴都在为这个版本而努力，新版本官网也做了一些改变。变更了首页的动态效果，现在变得更加简洁了。现在的背景的动图更加贴合LF的组件这个意图，思路来自于七巧板。感谢森阳的倾心设计。

![](/assets/img/blog/LiteFlow-2.12.0-1.png)

![](/assets/img/blog/LiteFlow-2.12.0-2.png)

同时v2.12.0更换了slogan，这个版本的slogan为：Make your code amazing。我们希望使用了LF能帮助开发者把他们的代码变得更加灵巧且令人惊叹。

接下来我们来了解下v2.12.0有哪些重头戏吧！

## 决策路由

决策路由是v2.12.0的版本特性。LF带来了规则的全新用法，即执行时不指定具体规则ID，通过对决策路由EL的判断，来动态该执行哪些规则，并且匹配以及执行的过程中都是全并行态的。为此LF引入了`<route>`标签：

```
<chain name="chain1">
    <route>
        AND(r1, r2, r3)
    </route>
    <body>
        THEN(a, b, c);
    </body>
</chain>

<chain name="chain2">
    <route>
        AND(OR(r4, r5), NOT(r3))
    </route>
    <body>
        SWITCH(x).TO(d, e, f);
    </body>
</chain>
```

在决策体中的EL只能用与或非表达式，组件只能用布尔表达式。具体如何使用大家请参考官网文档中的`决策路由`。

## 布尔组件

v2.12.0并不是一个兼容版本，最主要的原因就是新增了布尔组件的概念。

我们在新版本中去除了`NodeIfComponent`，`NodeWhileComponent`，`NodeBreakComponent` 三个组件形态，统一变成了布尔组件`NodeBooleanComponent`。换句话说，布尔组件能用在IF，WHILE，BREAK三种表达式中。

```
@LiteflowComponent("x")
public class ECmp extends NodeBooleanComponent {
    @Override
    public boolean processBoolean() throws Exception {
        // do your biz
        return true;
    }
}
```

大家如果升级新版本，需要替换以上三种组件为布尔组件，替换下超类就可以了，这个过程不会耽误大家太多时间。为此我们准备了`2.12.0升级指南`，里面有非常详细的升级指南，这份指南可以在官网找到。

## 允许启动不检查节点存在性

社区里有很多小伙伴反映过现在强制性启动检查节点存在性让他们的协作开发受到了一些阻碍。为此LF新版提供了一个全局参数：

```
parse-mode=PARSE_ONE_ON_FIRST_EXEC
```

这个`parse-mode`取代了以前的`parse-on-start` 。这个参数有三种值：

| 设置值 | 含义 |
| --- | --- |
| PARSE\_ALL\_ON\_START | 启动时解析所有的规则，不配置默认就是这个值 |
| PARSE\_ALL\_ON\_FIRST\_EXEC | 启动时不解析规则，但是第一次执行任意规则时，解析所有的 |
| PARSE\_ONE\_ON\_FIRST\_EXEC | 启动时不解析规则，但是第一次执行相关规则时，只解析对应的规则 |

关于这部分的详细解释，请看官网文档中的`元数据管理 -> 启动不检查规则`。

## 重写了python脚本引擎

LF提供了python脚本支持，但是一直以来这个解析引擎就是存在一些问题的，可能是社区里面的人用python引擎的人不多吧，所以这个问题一直没有被暴露出来。既然有人用，既然出现了问题。那么我们就有必要修复这个问题。

为此，我们推翻了之前python解析引擎的实现方式，重新写了一版，解决了python脚本不能正常返回的情况。

## 声明式组件的大一统

其实这个特性早在2.11.4中就已经实现了，严格意义上不算新版本的特性。但是由于作了兼容，所以在2.11.X的文档上并没有加以说明。

之前LF分类声明式和方法级别声明式，并且类声明式在声明上比方法级别声明式多了一个`@LiteflowCmpDefine` 标注，曾经有一个小伙伴表示这个有点多余，而且有点割裂感。

所以LF在新的声明式中可以不要这个标注。基本上类级别声明式和方法级别声明式就统一了，你在一个类中定义一个组件，那就是类级别声明式，定义多个组件就是方法级别声明式。当然以前那种定义方式仍然有效。LF进行了兼容。

这是类级别声明式：

```
@LiteflowComponent("a")
public class ACmp{
  
    @LiteflowMethod(LiteFlowMethodEnum.PROCESS, nodeType = NodeTypeEnum.COMMON)
    public void processAcmp(NodeComponent bindCmp) {
        System.out.println("ACmp executed!");
    }
}
```

这是方法级别声明式：

```
@LiteflowComponent
public class CmpConfig {

    //普通组件的定义
    @LiteflowMethod(value = LiteFlowMethodEnum.PROCESS, nodeId = "a", nodeName = "A组件")
    public void processA(NodeComponent bindCmp) {
        ...
    }

    //SWITCH组件的定义
    @LiteflowMethod(value = LiteFlowMethodEnum.PROCESS_SWITCH, nodeId = "b", nodeName = "B组件", nodeType = NodeTypeEnum.SWITCH)
    public String processB(NodeComponent bindCmp) {
        ...
    }
    
    //布尔组件的定义
    @LiteflowMethod(value = LiteFlowMethodEnum.PROCESS_BOOLEAN, nodeId = "c", nodeName = "C组件", nodeType = NodeTypeEnum.BOOLEAN)
    public boolean processC(NodeComponent bindCmp) {
        ...
    }
}
```

## 上下文提供别名获取方式

上下文的获取，相信大家都知道是通过`this.getContextBean`或者是`this.getFirstContextBean`来获取。但是这些获取方式都是通过class来进行获取的，如果传入两个相同class的上下文，就会有问题了。

LF在新版本推出了根据别名获取的方式，想要定义一个上下文的别名，很简单，只需要通过`@ContextBean`注解进行标注：

```
@ContextBean("anyName")
public class YourContext {
    ...
}
```

获取的时候，LF提供了一个新的获取方式：

```
@Component("a")
public class ACmp extends NodeComponent {

    @Override
    public void process() {
        TestContext context = this.getContextBean("anyName");
        ...
    }
}
```

这个特性能解决相同类作为上下文的场景。

## EL层面提供retry关键字

LF之前提供重试特性，但是重试特性是通过`@LiteflowRetry`标注来提供的。这个标注在类上，并没有办法在规则EL中得以体现。在新版本中，我们提供了基于EL层面的`retry`关键字，并且把重试这种特性作用于了任意表达式上，而不再是只作用于组件上。

```
<chain id="chain1">
    THEN(a, b.retry(3));
</chain>

<chain id="chain2">
    THEN(a, b).retry(3);
</chain>

<chain id="chain3">
    FOR(c).DO(a).retry(3);
</chain>

<chain id="chain4">
    exp = SWITCH(x).to(m,n,p);
    IF(f, exp.retry(3), b);
</chain>
```

关于`retry`关键字的用法详细请参考官网文档中的`高级特性 -> EL中的重试`。

## 新增验证/卸载脚本特性

在新版本中，我们提供了一个验证脚本和卸载脚本的api。具体用法如下：

```
boolean isValid = ScriptValidator.validate(script);
```

卸载脚本的用法如下：

```
FlowBus.unloadScriptNode(String nodeId);
```

## 提供查看一个规则下的所有Node

新版中，我们还提供了一个api，用来查看chain下的所有node：

```
List <Node> nodeList = FlowBus.getNodesByChainId("chain1");
```

## 对某一个规则/脚本进行启停操作

在数据中，其实LF早就支持了规则和脚本层面的enable，但是在其他存储插件中，我们之前并未支持。

新版中我们也进行了支持，由于很多存储插件诸如zk，etcd，redis，apollo这种规则/脚本都是KV结构，我们在key的结构上作了一定的修改。

规则key的完整形态为：`规则ID[:是否启用]`。

脚本key的完整形态为：`脚本组件ID:脚本类型[:脚本名称:脚本语言:是否启用]`。

其中方括号内的为可选值。

具体这部分的详细解释请参考官方文档中的`规则以及配置源`这一章。

## 完整的更新列表

```
特性 #I96A33 为LF增加决策表特性

https://gitee.com/dromara/liteFlow/issues/I96A33

特性 #I9DQDU 允许对不存在的组件增加全局参数

https://gitee.com/dromara/liteFlow/issues/I9DQDU

特性 #I9050W 为每一个上下文提供一个名字，使用时可以根据名字来获取

https://gitee.com/dromara/liteFlow/issues/I9050W

特性 #I8PL2M EL语句里可以设置重试次数，类似于EL中的超时时间

https://gitee.com/dromara/liteFlow/issues/I8PL2M

特性 #I61D1N 规则层面增加一个enable的选项，可以禁用规则

https://gitee.com/dromara/liteFlow/issues/I61D1N

特性 #I8YNCB 查看某一个chainId下的所有node

https://gitee.com/dromara/liteFlow/issues/I8YNCB

特性 #I8HDIA 新增一个验证脚本的方法

https://gitee.com/dromara/liteFlow/issues/I8HDIA

增强 #I8SMQB BREAK、IF、WHILE组件统一变成布尔组件

https://gitee.com/dromara/liteFlow/issues/I8SMQB

增强 #I8MW6Q 没有脚本（node）加载后没有提供卸载的方法，可能造成脚本一直占用内存

https://gitee.com/dromara/liteFlow/issues/I8MW6Q

增强 #I905AD 优化注解的获取速度，优化性能

https://gitee.com/dromara/liteFlow/issues/I905AD

增强 #I95XTD python脚本无法写return脚本

https://gitee.com/dromara/liteFlow/issues/I95XTD

增强 #I90IRR 设置超时maxWaitSenconds之后，超时的组件仍旧执行会报出NPE的问题

https://gitee.com/dromara/liteFlow/issues/I90IRR

增强 #I9F2HP 链路继承中的占位符换成双括弧

https://gitee.com/dromara/liteFlow/issues/I9F2HP

增强 #I98L0S 现版本需要依赖jackson2.16，缺少toPrettyString方法

https://gitee.com/dromara/liteFlow/issues/I98L0S

增强 #I97Y7Y 2.11.4.2版本中ComponentScanner初始化失败，建议重写BeanPostProcessor#postProcessBeforeInitialization

https://gitee.com/dromara/liteFlow/issues/I97Y7Y

增强 #I91AUT 对于诸如isContinueOnError或isAccess这样的方法，期望能够提供set形式的调用

https://gitee.com/dromara/liteFlow/issues/I91AUT

修复 #I932V4 组件降级，在寻找降级组件时，仍应该去查找下有没有对应的组件

https://gitee.com/dromara/liteFlow/issues/I932V4

修复 #I8YDGE 在迭代循环组件中，无法获取子流程传递的请求参数

https://gitee.com/dromara/liteFlow/issues/I8YDGE

修复 #I8Y0X4 并行循环中有可能导致的ConditionStack的并发问题

https://gitee.com/dromara/liteFlow/issues/I8Y0X4
```

## 吸纳团队成员

目前LF成员一共有11个人，我们也希望吸纳更多的开发者加入到开源团队之中。如果想加入开源团队，"投名状"为3个PR，成功通过作者的审核并被合入主干才能有资格。如果是学生朋友，则完成作者指定分派的课题并成功合入主干即为通过。

## LF CLUB

LF CLUB是由作者创办的高级付费社区。

LF CLUB能帮助到所有LiteFlow框架的使用者，以及想使用LiteFlow的潜在开发者。

除了给会员提供最及时最详细的咨询服务，目前在连载的白话文解析LF系列已经更新11篇，这个系列会是50篇以上的系列。并且全部都是独家内容。不会在其他平台上进行发布。

除此之外，还会推出其他高并发解析，Java spring使用高级技巧等系列以及LF相关进程，新特性的介绍等等。

基本上每2到3天会更新一次。如果有兴趣的小伙伴，可以扫以下二维码看介绍。

![](/assets/img/blog/LiteFlow-2.12.0-3.png)