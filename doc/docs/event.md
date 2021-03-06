# 事件机制

## 目录

- 为什么要事件机制
- Jboot 事件机制的使用

## 为什么要事件机制？

答案是：为了解耦，保持自身的独立和简洁，从而降低系统复杂度。

举个例子：
**用户注册**功能几乎是互联网系统必备的功能，用户注册的功能非常简单，用户提交注册信息，系统验证用户合法性后保存信息到数据库。

但是，由于运营等需求，可能在某个时间点需要增加某个功能，例如：
- 在元旦的时候，注册用户添加2个积分。
- 新的需求用户注册成功后，需要发送一封邮件。
- 在情人节给注册邀请人10积分。
- 公司老板今天的生日、注册自动升级为xxx会员。
- ... 等等等等

同时，在稍微复杂的系统里，一个系统往往可能会有多个注册入口，比如网页注册、APP通过api注册、微信用户自动登录时注册、第三方用户登录时进行注册...

那么，此时，注册功能每添加一个新的需求，就需要修改很多地方的代码...出现很多的代码冗余。

如何解决呢？

此时就用到了事件机制了。在用户注册的时候，系统只需要发送一个新用户注册的事件，让事件的监听器去获取这个事件后，执行自己的业务逻辑。

每个需求，就是一个事件监听器。当有新的需求出现的时候，我们只需要写一个新的事件监听器就可以了，其他任何地方的所有代码都不需要改动。


## Jboot 事件机制的使用

事件机制由两部分组成，一个是发送者，一个是接收者。发送者只是一个抽象的概念，在 Jboot 中并没有这个对象的存在。

当我们需要发送事件的时候，只需要调用如下代码就可以了。

```java
Jboot.sendEvent("eventName",  data)
```

事件监听器如何监听到这个事件呢，只需要2步：
1. 编写一个类（任意名称）实现 `JbootEventListener` 接口
2. 通过 `@EventConfig` 注解配置上这个编写的类

例如：

```java
@EventConfig(action = {"eventName","event1","event2"})
public class MyEventListener implements JbootEventListener {
    
    public  void onEvent(JbootEvent event){
        Object data = event.getData();
        System.out.println("get data:"+data);
    }
}
```

此时，当有 `eventName` , `event1` , `event2` 任何一个事件发送的时候，以上的 `MyEventListener.onEvent` 都会被触发执行。

**备注**

事件机制和 MQ（消息队列）很相似，主要区别是在于事件机制只是在 Jvm 应用内执行，解决的业务之间的耦合问题。

MQ（消息队列）解决的是分布式下，多个系统的事件（或者消息）发送和监听，MQ 需要第三方的 **中间件** ，例如：Redis、rabbitMQ、activeMQ...等。


