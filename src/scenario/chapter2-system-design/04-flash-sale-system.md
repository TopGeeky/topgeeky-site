---
title: 04.秒杀系统设计——面试官挑不出刺的秒杀系统
description:缓存系统是目前几乎所有应用中广泛采用的技术。另外，它适用于技术栈的每一层。例如，在网络领域中，缓存用于 DNS 查找，Web 服务器缓存用于频繁的请求。
date: 2024-10-01
order: 3
tags:
  - Java
  - interview
  - system design
author: TopGeeky



---

# 

在电商、游戏、票务等热门领域，秒杀活动总能激发用户的热情，但也给技术团队带来了巨大的挑战。设计一个能够承受高并发、防止超卖、保证数据一致性的秒杀系统，绝非易事。今天，我们就来聊聊如何从头开始构建一个高效稳定的秒杀系统。

#### **一、秒杀系统的核心挑战**

1. **高并发**：秒杀活动开始瞬间，会有大量用户同时访问，系统需要快速响应每一个请求。
2. **超卖风险**：库存有限，如何在高并发下确保库存不会超卖，是秒杀系统的关键。
3. **数据一致性**：秒杀过程中，需要保证数据的一致性，避免出现库存和订单数据不一致的情况。
4. **用户体验**：在高并发下，如何保证用户请求的快速响应，提升用户体验，也是需要考虑的问题。

#### **二、秒杀系统的设计思路**

1. **前端优化**

2. - **静态资源缓存**：秒杀页面的静态资源（如图片、CSS、JS等）可以提前缓存到CDN，减少服务器的访问压力。
   - **用户请求限流**：通过前端页面控制用户点击秒杀按钮的频率，比如每秒只允许点击一次，防止恶意刷单。
   - **验证码机制**：对于高价值商品，可以加入验证码机制，进一步防止恶意刷单和机器人攻击。

3. **后端优化**

4. - **库存预热**：秒杀开始前，将库存数据预热到缓存中，减少数据库访问压力。
   - **异步处理**：对于秒杀成功的请求，可以采用异步方式处理后续操作，比如生成订单、发送短信通知等，提高系统响应速度。
   - **分布式锁**：使用分布式锁（如Redis分布式锁）来控制对库存的并发访问，防止超卖。
   - **消息队列**：使用消息队列（如Kafka、RabbitMQ等）来削峰填谷，将秒杀请求异步处理，减轻系统压力。

5. **数据库优化**

6. - **读写分离**：采用主从数据库架构，实现读写分离，提高数据库读写性能。
   - **分库分表**：针对秒杀商品，可以提前进行分库分表，减少单个数据库和表的压力。
   - **事务管理**：确保秒杀过程中的事务一致性，避免数据不一致的问题。

7. **安全防护**

8. - **防刷单**：通过用户行为分析、IP地址限制、设备指纹等技术手段，防止恶意刷单行为。
   - **限流策略**：在后端服务层、数据库层等关键位置设置限流策略，防止系统被恶意攻击导致崩溃。

9. **监控与报警**

10. - **实时监控**：通过监控工具（如Prometheus、Grafana等）实时监控系统的性能指标，如QPS、响应时间、错误率等。
    - **报警机制**：设置报警机制，当系统出现异常或性能指标达到阈值时，及时通知相关人员进行处理。

#### **三、秒杀系统的实现步骤**

1. **需求分析与设计**：明确秒杀系统的业务需求和技术要求，设计系统架构和数据库结构。
2. **技术选型**：根据系统需求选择合适的技术栈，如前端框架、后端框架、数据库、缓存、消息队列等。
3. **编码实现**：按照设计文档进行编码实现，注意代码的可读性和可维护性。
4. **测试与调优**：进行单元测试、集成测试、压力测试等，确保系统的稳定性和性能。根据测试结果进行调优，优化系统性能。
5. **上线与监控**：将系统部署到生产环境，并进行实时监控和报警配置。定期回顾系统性能和数据，持续优化系统。

#### **四、总结**

秒杀系统的设计是一个复杂而有趣的过程，需要综合考虑前端、后端、数据库、安全防护等多个方面。通过合理的架构设计、技术选型、编码实现和测试调优，我们可以构建一个高效稳定的秒杀系统，为用户提供良好的秒杀体验。同时，也需要保持对新技术和新方法的关注和学习，不断提升系统的性能和安全性。希望本文能够对你有所启发和帮助！



## **[一：秒杀应该考虑哪些问题](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)**

### [1.1：超卖问题](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)

分析秒杀的业务场景,最重要的有一点就是超卖问题，假如备货只有100个，但是最终超卖了200，一般来讲秒杀系统的价格都比较低，如果超卖将严重影响公司的财产利益，因此首当其冲的就是解决商品的超卖问题。

### [1.2：高并发](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)

秒杀具有时间短、并发量大的特点，秒杀持续时间只有几分钟，而一般公司都为了制造轰动效应，会以极低的价格来吸引用户，因此参与抢购的用户会非常的多。短时间内会有大量请求涌进来，后端如何防止并发过高造成缓存击穿或者失效，击垮数据库都是需要考虑的问题。

### [1.3：接口防刷](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)

现在的秒杀大多都会出来针对秒杀对应的软件，这类软件会模拟不断向后台服务器发起请求，一秒几百次都是很常见的，如何防止这类软件的重复无效请求，防止不断发起的请求也是需要我们针对性考虑的

### [1.4：秒杀url](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)

对于普通用户来讲，看到的只是一个比较简单的秒杀页面，在未达到规定时间，秒杀按钮是灰色的，一旦到达规定时间，灰色按钮变成可点击状态。这部分是针对小白用户的，如果是稍微有点电脑功底的用户，会通过F12看浏览器的network看到秒杀的url，通过特定软件去请求也可以实现秒杀。或者提前知道秒杀url的人，一请求就直接实现秒杀了。这个问题我们需要考虑解决

### [1.5：数据库设计](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)

秒杀有把我们服务器击垮的风险，如果让它与我们的其他业务使用在同一个数据库中，耦合在一起，就很有可能牵连和影响其他的业务。如何防止这类问题发生，就算秒杀发生了宕机、服务器卡死问题，也应该让他尽量不影响线上正常进行的业务

### [1.6:大量请求问题](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)

按照1.2的考虑，就算使用缓存还是不足以应对短时间的高并发的流量的冲击。如何承载这样巨大的访问量，同时提供稳定低时延的服务保证，是需要面对的一大挑战。我们来算一笔账，假如使用的是redis缓存，单台redis服务器可承受的QPS大概是4W左右，如果一个秒杀吸引的用户量足够多的话，单QPS可能达到几十万，单体redis还是不足以支撑如此巨大的请求量。缓存会被击穿，直接渗透到DB,从而击垮mysql.后台会将会大量报错

> 基于 Spring Cloud Alibaba + Gateway + Nacos + RocketMQ + Vue & Element 实现的后台管理系统 + 用户小程序，支持 RBAC 动态权限、多租户、数据权限、工作流、三方登录、支付、短信、商城等功能
>
> - 项目地址：https://github.com/YunaiV/yudao-cloud
> - 视频教程：https://doc.iocoder.cn/video/

## **[二：秒杀系统的设计和技术方案](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)**

### [2.1：秒杀系统数据库设计](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)

针对1.5提出的秒杀数据库的问题，因此应该单独设计一个秒杀数据库，防止因为秒杀活动的高并发访问拖垮整个网站。这里只需要两张表，一张是秒杀订单表，一张是秒杀货品表

![图片](https://mmbiz.qpic.cn/mmbiz_png/JdLkEI9sZffztUzzMlwADkJHaiaYnInqIowepyr1MzuPhvtZNj3tlDwyw0FicKrPJqCkSl7pytApxB4xQotzfl4w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![图片](https://mmbiz.qpic.cn/mmbiz_png/JdLkEI9sZffztUzzMlwADkJHaiaYnInqInn03Nib247YGf6r0L5icjCMVxV2IqvOftUBwleeaHh4iaL7d2FICuQ6Xw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

其实应该还有几张表，商品表：可以关联goods_id查到具体的商品信息，商品图像、名称、平时价格、秒杀价格等，还有用户表：根据用户user_id可以查询到用户昵称、用户手机号，收货地址等其他额外信息，这个具体就不给出实例了。

### [2.2：秒杀url的设计](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)

为了避免有程序访问经验的人通过下单页面url直接访问后台接口来秒杀货品，我们需要将秒杀的url实现动态化，即使是开发整个系统的人都无法在秒杀开始前知道秒杀的url。具体的做法就是通过md5加密一串随机字符作为秒杀的url，然后前端访问后台获取具体的url，后台校验通过之后才可以继续秒杀。

### [2.3：秒杀页面静态化](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)

将商品的描述、参数、成交记录、图像、评价等全部写入到一个静态页面，用户请求不需要通过访问后端服务器，不需要经过数据库，直接在前台客户端生成，这样可以最大可能的减少服务器的压力。具体的方法可以使用freemarker模板技术，建立网页模板，填充数据，然后渲染网页

### [2.4:单体redis升级为集群redis](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)

秒杀是一个读多写少的场景，使用redis做缓存再合适不过。不过考虑到缓存击穿问题，我们应该构建redis集群，采用哨兵模式，可以提升redis的性能和可用性。

### [2.5:使用nginx](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)

nginx是一个高性能web服务器，它的并发能力可以达到几万，而tomcat只有几百。通过nginx映射客户端请求，再分发到后台tomcat服务器集群中可以大大提升并发能力。

### [2.6:精简sql](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)

典型的一个场景是在进行扣减库存的时候，传统的做法是先查询库存，再去update。这样的话需要两个sql，而实际上一个sql我们就可以完成的。可以用这样的做法：`update miaosha_goods set stock =stock-1 where goos_id ={#goods_id} and version = #{version} and sock>0;`这样的话，就可以保证库存不会超卖并且一次更新库存,还有注意一点这里使用了版本号的乐观锁，相比较悲观锁，它的性能较好。

### [2.7：redis预减库存](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)

很多请求进来，都需要后台查询库存,这是一个频繁读的场景。可以使用redis来预减库存，在秒杀开始前可以在redis设值，比如redis.set(goodsId,100),这里预放的库存为100可以设值为常量),每次下单成功之后,`Integer stock = (Integer)redis.get(goosId);` 然后判断sock的值，如果小于常量值就减去1;不过注意当取消的时候,需要增加库存，增加库存的时候也得注意不能大于之间设定的总库存数(查询库存和扣减库存需要原子操作，此时可以借助lua脚本)下次下单再获取库存的时候,直接从redis里面查就可以了。

### [2.8:接口限流](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)

秒杀最终的本质是数据库的更新，但是有很多大量无效的请求，我们最终要做的就是如何把这些无效的请求过滤掉，防止渗透到数据库。限流的话，需要入手的方面很多：

#### [2.8.1：前端限流](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)

首先第一步就是通过前端限流，用户在秒杀按钮点击以后发起请求，那么在接下来的5秒是无法点击(通过设置按钮为disable)。这一小举措开发起来成本很小，但是很有效。

#### [2.8.2：同一个用户xx秒内重复请求直接拒绝](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)

具体多少秒需要根据实际业务和秒杀的人数而定，一般限定为10秒。具体的做法就是通过redis的键过期策略，首先对每个请求都从`String value = redis.get(userId);`如果获取到这个

value为空或者为null，表示它是有效的请求，然后放行这个请求。如果不为空表示它是重复性请求，直接丢掉这个请求。如果有效,采用`redis.setexpire(userId,value,10).value`可以是任意值，一般放业务属性比较好,这个是设置以userId为key，10秒的过期时间(10秒后,key对应的值自动为null)

#### [2.8.3：令牌桶算法限流](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)

接口限流的策略有很多，我们这里采用令牌桶算法。令牌桶算法的基本思路是每个请求尝试获取一个令牌，后端只处理持有令牌的请求，生产令牌的速度和效率我们都可以自己限定，guava提供了RateLimter的api供我们使用。以下做一个简单的例子,注意需要引入guava

```
public class TestRateLimiter {

    public static void main(String[] args) {
        //1秒产生1个令牌
        final RateLimiter rateLimiter = RateLimiter.create(1);
        for (int i = 0; i < 10; i++) {
            //该方法会阻塞线程，直到令牌桶中能取到令牌为止才继续向下执行。
            double waitTime= rateLimiter.acquire();
            System.out.println("任务执行" + i + "等待时间" + waitTime);
        }
        System.out.println("执行结束");
    }
}
```

上面代码的思路就是通过RateLimiter来限定我们的令牌桶每秒产生1个令牌(生产的效率比较低)，循环10次去执行任务。acquire会阻塞当前线程直到获取到令牌，也就是如果任务没有获取到令牌，会一直等待。那么请求就会卡在我们限定的时间内才可以继续往下走，这个方法返回的是线程具体等待的时间。执行如下;

![图片](https://mmbiz.qpic.cn/mmbiz_png/JdLkEI9sZffztUzzMlwADkJHaiaYnInqILacupTyOcIHR2Qa6ycfqbSVxk4sLyB85DGNq1rGRoYBtNK2zebibljw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

可以看到任务执行的过程中，第1个是无需等待的，因为已经在开始的第1秒生产出了令牌。接下来的任务请求就必须等到令牌桶产生了令牌才可以继续往下执行。

如果没有获取到就会阻塞(有一个停顿的过程)。不过这个方式不太好，因为用户如果在客户端请求，如果较多的话,直接后台在生产token就会卡顿(用户体验较差)，它是不会抛弃任务的，我们需要一个更优秀的策略:如果超过某个时间没有获取到，直接拒绝该任务。接下来再来个案例：

```
public class TestRateLimiter2 {

    public static void main(String[] args) {
        final RateLimiter rateLimiter = RateLimiter.create(1);

        for (int i = 0; i < 10; i++) {
            long timeOut = (long) 0.5;
            boolean isValid = rateLimiter.tryAcquire(timeOut, TimeUnit.SECONDS);
            System.out.println("任务" + i + "执行是否有效:" + isValid);
            if (!isValid) {
                continue;
            }
            System.out.println("任务" + i + "在执行");
        }
        System.out.println("结束");
    }
}
```

其中用到了tryAcquire方法，这个方法的主要作用是设定一个超时的时间，如果在指定的时间内预估(注意是预估并不会真实的等待)，如果能拿到令牌就返回true，如果拿不到就返回false.然后我们让无效的直接跳过，这里设定每秒生产1个令牌，让每个任务尝试在

0.5秒获取令牌，如果获取不到,就直接跳过这个任务(放在秒杀环境里就是直接抛弃这个请求)；程序实际运行如下：

![图片](https://keaganoss.oss-cn-shanghai.aliyuncs.com/scenario/202411292145123.png)

只有第1个获取到了令牌，顺利执行了，下面的基本都直接抛弃了，因为0.5秒内，令牌桶(1秒1个)来不及生产就肯定获取不到返回false了。

这个限流策略的效率有多高呢？假如我们的并发请求是400万瞬间的请求,将令牌产生的效率设为每秒20个，每次尝试获取令牌的时间是0.05秒，那么最终测试下来的结果是，每次只会放行4个左右的请求,大量的请求会被拒绝,这就是令牌桶算法的优秀之处。

### [2.9:异步下单](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)

为了提升下单的效率，并且防止下单服务的失败。需要将下单这一操作进行异步处理。最常采用的办法是使用队列，队列最显著的三个优点：异步、削峰、解耦。这里可以采用rabbitmq，在后台经过了限流、库存校验之后，流入到这一步骤的就是有效请求。然后发送到队列里，队列接受消息，异步下单。下完单，入库没有问题可以用短信通知用户秒杀成功。假如失败的话,可以采用补偿机制，重试。

### [3.0：服务降级](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)

假如在秒杀过程中出现了某个服务器宕机，或者服务不可用，应该做好后备工作。之前的博客里有介绍通过Hystrix进行服务熔断和降级，可以开发一个备用服务，假如服务器真的宕机了，直接给用户一个友好的提示返回，而不是直接卡死，服务器错误等生硬的反馈。

## **[三：总结](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)**

秒杀流程图：

![图片](https://keaganoss.oss-cn-shanghai.aliyuncs.com/scenario/202411292145164.webp)

这就是我设计出来的秒杀流程图,当然不同的秒杀体量针对的技术选型都不一样，这个流程可以支撑起几十万的流量，如果是成千万破亿那就得重新设计了。

比如数据库的分库分表、队列改成用kafka、redis增加集群数量等手段。通过本次设计主要是要表明的是我们如何应对高并发的处理，并开始尝试解决它，在工作中多思考、多动手能提升我们的能力水平，加油！如果本篇博客有任何错误，请麻烦指出来，不胜感激