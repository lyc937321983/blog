---
title: 谈谈如何解决 RabbitMQ 消息丢失与消息积压
date: 2026-09-01 13:48:29
tags: Java RabbitMQ 消息丢失
comment: 'valine'
categories: 
- Java
---

# 谈谈如何解决 RabbitMQ 消息丢失与消息积压

# 前言

目前企业中最常用到的消息队列就是 RabbitMQ（主要是因为它在中小企业普及更早，经受的考验也更久，带来了一大批“回头客”），所以掌握 RabbitMQ 的相关技能就显得是比较重要了。我们在使用 RabbitMQ 的过程中比较常见的问题就是消息丢失、消息积压等等，所以此类问题也就成为了面试官们老生常谈的问题了... 今天就和大家分享一下我在工作过程中解决 RabbitMQ 消息丢失与消息积压的一点经验心得 

# RabbitMQ 的消息丢失问题

RabbitMQ 避免消息丢失的方法主要是利用消息确认机制和手动签收机制，所以在解决消息丢失问题之前，我们有必要把这两个概念搞清楚。

## 消息确认机制（针对生产者）

我们可以通过在 .yml 文件中增加如下配置，开始消息确认机制 

```java
spring:
  rabbitmq:
    publisher-returns: true #消息失败确认
    publisher-confirm-type: CORRELATED  #消息成功确认
    template:
      mandatory: true #手动签收机制
```

这样，当我们实现 ConfirmCallback、ReturnCallback 这两个接口的方法后，就可以有针对性地进行消息确认的日志记录与后续的进一步操作（如：消息发送补偿等），从而达到接近100%投递的目的。

```java
import com.rabbitmq.client.ConfirmCallback;
import com.rabbitmq.client.Return;
import com.rabbitmq.client.ReturnCallback;

import java.io.IOException;

/**
 * @description: RabbitMQSender
 * @author: 庄霸.liziye
 * @create: 2022-08-03 09:39
 **/
public class RabbitMQSender implements ConfirmCallback, ReturnCallback {

    /**
     * 消息发送
     * @param msg
     */
    public void sendMsg(String msg){
        
    }

    /**
     * 成功接受后的回调
     * @param l
     * @param b
     * @throws IOException
     */
    @Override
    public void handle(long l, boolean b) throws IOException {
        //具体处理
    }

    /**
     * 失败后的回调
     * @param aReturn
     */
    @Override
    public void handle(Return aReturn) {
        //具体处理
    }
}
```

> 注意：生产者消息确认机制会降低 RabbitMQ 性能，个人建议非必要不使用此机制。我们也可以通过人工识别业务状态判断消费者是否处理了业务逻辑，如果没有处理相关消息，我们可以通过人工进行补偿。这两种方案各有利弊，前者会降低性能，后者会提升运维成本，所以我们需要根据具体情况选择最合适的解决方案。

## 消息确认机制（针对消费者）

RabbitMQ的消息是自动签收的，你可以理解为快递签收了，那么这个快递的状态就从发送变为已签收，唯一的区别是快递公司会对物流轨迹有记录，而MQ签收后就从队列中删除了。企业级开发中，RabbitMQ我们基本都开启手动签收方式，这样可以有效避免消息的丢失。

开启手动签收的方式也很简单，只需要在 .yml 文件中新增一条配置即可 

```java
spring:
  rabbitmq:
    listener:
      direct:
        acknowledge-mode: manual #手动签收机制
```

在代码中使用手动签收也很简单，只需要一行代码，如下图所示（该截图来自于目前我参与的项目） 

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/582efacf4f0d499fa01382d377b31af0~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

> 我目前参与的项目中，手动签收方式几乎都是处理完业务逻辑之后再手动签收，个人认为这种用法是不科学的（在处理完业务逻辑后再手动签收，否则不签收，就好比客人进店了你得买东西，否则不让走 ）。
>
> 在分布式的架构中，RabbitMQ 用来解耦和转发是非常常见的，如果是支付业务，往往在回调通知中通过 RabbitMQ 转发到其他服务，如果其他服务处理不成功，那么手动签收也不执行，这个消息又会入队发给其他消费者，这样就可能在流量洪峰阶段因为偶然的业务处理失败造成堵塞，这样就得不偿失了。个人认为我们可以使用 try-catch-finally 代码块包裹住消息处理部分的代码，将手动签收（即 .basicAck）部分放在 finally 模块中，这样可以保证 RabbitMQ 的职责单一、运行流畅。

## 消息丢失

我们了解了消息确认机制和手动签收机制，接下来我们就可以开始处理消失丢失问题了。消息丢失的原因主要有以下三种：

- 消息发出后，中途网络故障，RabbitMQ 服务器没收到
- 消息发出后，RabbitMQ 服务器收到了，但是还没持久化，服务器就宕机了
- 消息发出后，RabbitMQ 服务器收到了，但是消费方还未做业务逻辑处理，服务却挂掉了

针对上面的问题原因，我们可以使用以下方案来解决问题：

- 生产方在发送消息时，要用 try-catch代码块包裹发送消息的代码，在 catch 中捕获异常，并将 RabbitMQ 发送的关键内容记录到日志中（并存储消息发送状态），若发送失败，由定时任务定期扫描重新发送并更新状态。
- 消息的生产者必须要加入确认回调机制，通过回调方法确认成功发送并签收的消息；如果消息发送失败进入失败回调方法，就修改数据库消息的状态，等待定时任务重发。
- 消费方要开启手动签收 ACK 机制，消费成功才将消息移除，失败或因异常情况而尚未处理，就重新入队。

# RabbitMQ 的消息积压问题

讲完了消息丢失问题的原因及其解决办法，我们接下来再看看如何处理消息积压的问题。导致出现消息积压的原因一般是两种：

- 消费者的服务挂掉，导致一直无法消费消息。
- 消费方的服务节点太少，导致消费能力不足，从而出现积压。

针对此类问题的解决办法就很直接了

- 既然消费能力不足，那就扩展更多消费节点，提升消费能力。这是最直接的方式，也是消息积压最常用的解决方案。
- 建立专门的队列消费服务，将消息批量取出并持久化（存入数据库、存入本地文件等等），之后再慢慢消费。这种方式主要针对于考虑到服务器成本压力的企业，先通过一个独立服务把要消费的消息存起来，之后再慢慢处理这些消息。