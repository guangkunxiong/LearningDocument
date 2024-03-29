# 什么是凤凰架构

流水不腐，有老朽、有消亡、有重生、有更迭，才是正常生态的运作合理规律。

那么你来设想一下，如果你的系统中，每个部件都符合“Phoenix”的特性，哪怕其中的某些部件采用了极不靠谱的程序代码，哪怕存在严重的内存泄漏问题，最多只能服务三分钟就一定会崩溃。而即便这样，只要在整体架构设计中，有恰当的、自动化的错误熔断、服务淘汰和重建的机制，那在系统外部来观察，它在整体上仍然有可能表现出稳定和健壮的服务能力。铺垫到这里，我就可以给你解释清楚，到底什么是“The Fenix Project”了。

# 演进中的架构 Unix设计哲学



> Unix的分布式设计哲学
>
> 保持接口与实现的简单性，比系统的任何其他属性，包括准确性、一致性和完整性，都来得更加重要。

一条路是尽快提升单机的处理能力，以避免分布式的种种问题；另一条路是找到更完美的解决方案，来应对如何构筑分布式系统的问题。

在当时捉襟见肘的运算能力、网络带宽下，设计不得不做出的妥协和摩尔定律的黄金时期第一条路更好。

但是，到了三十多年以后的今天，随着微服务的逐渐成熟完善，成为大型软件的主流架构风格以后，这个美好的愿景终将还是会重新被开发者拾起。

# 单体系统时代

单体架构是落后的系统架构风格，最终会被微服务所取代，前提是巨大的单体。



# SOA

三种代表性的服务架构模式

烟囱式架构

信息烟囱也被叫做信息孤岛（Information Island），使用这种架构的系统呢，也被称为孤岛式信息系统或者烟囱式信息系统。这种信息系统，完全不会跟其他相关的信息系统之间进行互操作，或者是进行协调工作。

微内核架构

微内核架构也可以嵌入到其它架构模式之中，通过插件的方式，来提供逐步演化的功能和增量开发。所以，如果你准备实现一个能够支持二次开发的软件系统，微内核就是一种良好的架构模式。微内核架构也有它的局限和使用前提，它会假设系统中各个插件模块之间是互不认识的（不可预知系统会安装哪些模块），这些插件会访问内核中一些公共的资源，但不会发生直接交互。

事件驱动架构

子系统之间建立一套事件队列管道（Event Queues），来自系统外部的消息将以事件的形式发送到管道中，各个子系统可以从管道里获取自己感兴趣、可以处理的事件消息，也可以为事件新增或者是修改其中的附加信息，甚至还可以自己发布一些新的事件到管道队列中去。每一个消息的处理者都是独立的、高度解耦的，但它又能与其他处理者（如果存在该消息处理者的话）通过事件管道来进行互动。

# 微服务时代

微服务是一种通过多个小型服务的组合，来构建单个应用的架构风格，这些服务会围绕业务能力而非特定的技术标准来构建。各个服务可以采用不同的编程语言、不同的数据存储技术、运行在不同的进程之中。服务会采取轻量级的通讯机制和自动化的部署机制，来实现通讯与运维。

## 第一，围绕业务能力构建

这个核心技术特征，实际上再次强调了康威定律的重要性。它的意思是，有怎样的结构、规模和能力的团队，就会产生出对应结构、规模、能力的产品。这个结论不是某个团队、某个公司遇到的巧合，而是必然的演化结果。

## 第二，分散治理

表达的是“谁家孩子谁来管”。微服务对应的开发团队，有着直接对服务运行质量负责的责任，也应该有着不受外界干预，掌控服务各个方面的权力，可以选择与其他服务异构的技术来实现自己的服务。

微服务更加强调的是，在确实有必要进行技术异构的时候，一个开发团队应该能有选择“不统一”的权利。

## 第三，通过服务来实现独立自治的组件

服务是进程外组件，它是通过远程调用来提供功能的。

## 第四，产品化思维

产品化思维的意思就是，我们要避免把软件研发看作是要去完成某种功能，而要把它当做是一种持续改进、提升的过程。比如，我们不应该把运维看作就是运维团队的事，把开发看作就是开发团队的事。

## 第五，数据去中心化

微服务这种架构模式也明确地提倡，数据应该按领域来分散管理、更新、维护和存储。

尽管在分布式中，我们要想处理好一致性的问题也很困难，很多时候都没法使用传统的事务处理来保证不出现一致性问题。但是两害相权取其轻，一致性问题这些必要的代价是值得付出的。

## 第六，轻量级通讯机制

这个弱管道（Dumb Pipes）机制，可以说几乎算是在直接指名道姓地反对 ESB、BPM 和 SOAP 等复杂的通讯机制。

如果服务需要上面的某一种功能或能力，那就应该在服务自己的 Endpoint（端点）上解决，而不是在通讯管道上一揽子处理。

## 第七，容错性设计

容错性设计，是指软件架构不再虚幻地追求服务永远稳定，而是接受服务总会出错的现实。这个技术特征要求，在微服务的设计中，有自动的机制能够对其依赖的服务进行快速故障检测，在持续出错的时候进行隔离，在服务恢复的时候重新联通。所以“断路器”这类设施，对实际生产环境的微服务来说，并不是可选的外围组件，而是一个必须的支撑点。如果没有容错性的设计，系统很容易就会因为一两个服务的崩溃带来的雪崩效应而被淹没。

## 第八，演进式设计

容错性设计承认服务会出错，而演进式设计则是承认服务会被报废淘汰。

## 第九，基础设施自动化

基础设施自动化，如 CI/CD 的长足发展，大大降低了构建、发布、运维工作的复杂性。

## 康威定律

第一定律 组织沟通方式会通过系统设计表达出来。
第二定律 时间再多一件事情也不可能做的完美，但总有时间做完一件事情。
第三定律 线型系统和线型组织架构间有潜在的异质同态特性。
第四定律 大的系统组织总是比小系统更倾向于分解

# 后微服务时代：跨越软件与硬件之间的界限

容器和容器编排，服务网格和sidecar

# 远程服务调用

## RPC

RPC 是一种语言级别的通讯协议，它允许运行于一台计算机上的程序以某种管道作为通讯媒介（即某种传输协议的网络），去调用另外一个地址空间（通常为网络上的另外一台计算机）。

朝着面向对象发展。这条线的缘由在于，在分布式系统中，开发者们不再满足于 RPC 带来的面向过程的编码方式，而是希望能够进行跨进程的面向对象编程。因此，这条线还有一个别名叫作分布式对象（Distributed Object），它的代表有 RMI、.NET Remoting。当然了，之前的 CORBA 和 DCOM 也可以归入这一类。

朝着性能发展，代表为 gRPC 和 Thrift。决定 RPC 性能主要就两个因素：序列化效率和信息密度。序列化效率很好理解，序列化输出结果的容量越小，速度越快，效率自然越高；信息密度则取决于协议中，有效荷载（Payload）所占总传输数据的比例大小，使用传输协议的层次越高，信息密度就越低，SOAP 使用 XML 拙劣的性能表现就是前车之鉴。gRPC 和 Thrift 都有自己优秀的专有序列化器，而在传输协议方面，gRPC 是基于 HTTP/2 的，支持多路复用和 Header 压缩，Thrift 则直接基于传输层的 TCP 协议来实现，省去了额外的应用层协议的开销。

朝着简化发展，代表为 JSON-RPC。要是说选出功能最强、速度最快的 RPC 可能会有争议，但要选出哪个功能弱的、速度慢的，JSON-RPC 肯定会是候选人之一。它牺牲了功能和效率，换来的是协议的简单。也就是说，JSON-RPC 的接口与格式的通用性很好，尤其适合用在 Web 浏览器这类一般不会有额外协议、客户端支持的应用场合。

## REST和RPC

REST 与 RPC 在思想上存在差异的核心，是抽象的目标不一样，也就是面向资源的编程思想与面向过程的编程思想之间的区别。

概念上的不同，是指 REST 并不是一种远程服务调用协议，甚至我们可以把定语也去掉，它就不是一种协议。

因为 REST 只能说是一种风格，而不是规范、协议，并且能完全达到 REST 所有指导原则的系统，也是很少见的。

## 理解 REST

- 资源 无论在电脑还是手机尽管它呈现出来的样子都不一样，但其中的信息是不变的，仍是同一个“资源”。
- 表征 “表征”这个概念是指信息与用户交互时的表示形式，这跟应用分层中我们常说的“表示层”（Presentation Layer）的语义其实是一致的。例如你要HTML格式，服务器就会返回给浏览器HTML格式
- 状态 在特定语境中才能产生的上下文信息就被称为“状态”。例如获取下一页这信息，就要知道当前也是哪里。这个由服务记住为有状态，浏览器记住为无状态。
- 转移 无论状态是由服务端还是客户端来提供的，下一页这个行为逻辑必然只能由服务端来提供。服务器通过某种方式，把“当前页”转变成“下一页”，这就被称为“表征状态转移”。

### 第一个，统一接口

HTTP 协议中已经提前约定好了一套“统一接口”，它包括：GET、HEAD、POST、PUT、DELETE、TRACE、OPTIONS 七种基本操作，任何一个支持 HTTP 协议的服务器都会遵守这套规定，对特定的 URI 采取这些操作，服务器就会触发相应的表征状态转移。

### 第二个，超文本驱动

### 第三个，自描述消息

资源的表征可能存在多种不同形态，因此在传输给浏览器的消息中应当有明确的信息，来告知客户端该消息的类型以及该如何处理这条消息。

### RESTful 风格的系统特征

#### 服务端与客户端分离

#### 无状态

REST 希望服务器能不负责维护状态，每一次从客户端发送的请求中，应该包括所有必要的上下文信息，会话信息也由客户端保存维护，服务器端依据客户端传递的状态信息来进行业务处理，并且驱动整个应用的状态变迁。

服务端无状态可以在分布式环境中获得很高价值的好处，但大型系统的上下文状态数量，完全可能膨胀到，客户端在每次发送请求时，根本无法全部囊括系统里所有必要的上下文信息。在服务端的内存、会话、数据库或者缓存等地方，持有一定的状态是一种现实情况，而且会是长期存在、被广泛使用的主流方案。

#### 可缓存

REST 希望软件系统能够像万维网一样，客户端和中间的通讯传递者（代理）可以将部分服务端的应答缓存起来。当然，应答中必须明确或者间接地表明本身是否可以进行缓存，以避免客户端在将来进行请求的时候得到过时的数据。

运作良好的缓存机制可以减少客户端、服务器之间的交互，甚至有些场景中可以完全避免交互，这就进一步提高了性能。

#### 分层系统

这里所指的并不是表示层、服务层、持久层这种意义上的分层，而是指客户端一般不需要知道是否直接连接到了最终的服务器，或者是连接到路径上的中间服务器。中间服务器可以通过负载均衡和共享缓存的机制，提高系统的可扩展性，这样也便于缓存、伸缩和安全策略的部署。

#### 统一接口

REST 希望开发者面向资源编程，希望软件系统设计的重点放在抽象系统该有哪些资源上，而不是抽象系统该有哪些行为（服务）上。

统一接口也是 REST 最容易陷入争论的地方，基于网络的软件系统，到底是面向资源更好，还是面向服务更合适，这件事情在很长的时间里恐怕都不会有个定论，也许永远都没有。但是，有一个已经基本清晰的结论是：面向资源编程的抽象程度通常更高。

抽象程度高有好处但也有坏处。坏处是往往距离人类的思维方式更远，而好处是往往通用程度会更好。

如果你想要在架构设计中合理恰当地利用统一接口，Fielding 给出了三个建议：第一，系统要能做到每次请求中都包含资源的 ID，所有操作均通过资源 ID 来进行；第二，每个资源都应该是自描述的消息；第三，通过超文本来驱动应用状态的转移。

#### 按需代码

按需代码被 Fielding 列为了一条可选原则，原因其实并非是它特别难以达到，更多是出于必要性和性价比的实际考虑。按需代码是指任何按照客户端（如浏览器）的请求，将可执行的软件程序从服务器发送到客户端的技术。它赋予了客户端无需事先知道，所有来自服务端的信息应该如何处理、如何运行的宽容度。

### REST 与 RPC 在思想上的差异

REST 的基本思想是面向资源来抽象问题，它与此前流行的面向过程的编程思想，在抽象主体上有本质的差别。

第一，降低了服务接口的学习成本。

第二，资源天然具有集合与层次结构。

以方法为中心抽象的接口，由于方法是动词，逻辑上决定了每个接口都是互相独立的；但以资源为中心抽象的接口，由于资源是名词，天然就可以产生集合与层次结构。

第三，REST 绑定于 HTTP 协议。

# 本地事务的原子性和持久性



# 本地事务如何实现隔离性

隔离性保证了每个事务各自读、写的数据互相独立，不会彼此影响。只从定义上，我们就能感觉到隔离性肯定与并发密切相关。如果没有并发，所有事务全都是串行的，那就不需要任何隔离，或者说这样的访问具备了天然的隔离性。

写锁（Write Lock，也叫做排他锁 eXclusive Lock，简写为 X-Lock）：只有持有写锁的事务才能对数据进行写入操作，数据加持着写锁时，其他事务不能写入数据，也不能施加读锁。

读锁（Read Lock，也叫做共享锁 Shared Lock，简写为 S-Lock）：多个事务可以对同一个数据添加多个读锁，数据被加上读锁后就不能再被加上写锁，所以其他事务不能对该数据进行写入，但仍然可以读取。对于持有读锁的事务，如果该数据只有一个事务加了读锁，那可以直接将其升级为写锁，然后写入数据。

范围锁（Range Lock）：对于某个范围直接加排他锁，在这个范围内的数据不能被读取，也不能被写入。如下语句是典型的加范围锁的例子：

## 本地事务的四种隔离级别

### 可串行化

串行化访问提供了强度最高的隔离性，ANSI/ISO SQL-92 中定义的最高等级的隔离级别便是可串行化（Serializable）。

可串行化比较符合普通程序员对数据竞争加锁的理解，如果不考虑性能优化的话，对事务所有读、写的数据全都加上读锁、写锁和范围锁即可（这种可串行化的实现方案称为 Two-Phase Lock）。

### 可重复读

可串行化的下一个隔离级别是可重复读（Repeatable Read）。可重复读的意思就是对事务所涉及到的数据加读锁和写锁，并且一直持续到事务结束，但不再加范围锁。

可重复读比可串行化弱化的地方在于幻读问题（Phantom Reads），它是指在事务执行的过程中，两个完全相同的范围查询得到了不同的结果集。

### 读已提交

可重复读的下一个隔离级别是读已提交（Read Committed）。读已提交对事务涉及到的数据加的写锁，会一直持续到事务结束，但加的读锁在查询操作完成后就马上会释放。

读已提交比可重复读弱化的地方在于不可重复读问题（Non-Repeatable Reads），它是指在事务执行过程中，对同一行数据的两次查询得到了不同的结果。

### 读未提交

读已提交的下一个级别是读未提交（Read Uncommitted）。读未提交对事务涉及到的数据只加写锁，这会一直持续到事务结束，但完全不加读锁。

读未提交比读已提交弱化的地方在于脏读问题（Dirty Reads），它是指在事务执行的过程中，一个事务读取到了另一个事务未提交的数据。

## MVCC 的基础原理

MVCC 是一种读取优化策略，它的“无锁”是特指读取时不需要加锁。MVCC 的基本思路是对数据库的任何修改都不会直接覆盖之前的数据，而是产生一个新版副本与老版本共存，以此达到读取时可以完全不加锁的目的。

# 认证

- 认证（Authentication）：系统如何正确分辨出操作用户的真实身份？
- 授权（ Authorization）：系统如何控制一个用户该看到哪些数据、能操作哪些功能？
- 凭证（Credentials）：系统如何保证它与用户之间的承诺是双方当时真实意图的体现，是准确、完整且不可抵赖的？
- 保密（Confidentiality）：系统如何保证敏感数据无法被包括系统管理员在内的内外部人员所窃取、滥用？
- 传输（Transport Security）：系统如何保证通过网络传输的信息无法被第三方窃听、篡改和冒充？
- 验证（Verification）：系统如何确保提交到每项服务中的数据是合乎规则的，不会对系统稳定性、数据一致性、正确性产生风险？



# 授权

## OAuth2.0第三方授权

授权码模式，以微信第三方登录为例

1. 第三方应用将资源所有者（用户）导向授权服务器的授权页面，并向授权服务器提供 ClientID 及用户同意授权后的回调 URI，这是第一次客户端页面转向。第三方应用请求微信授权URL，带着应用的身份id和回调URL。
2. 授权服务器根据 ClientID 确认第三方应用的身份，用户在授权服务器中决定是否同意向该身份的应用进行授权。注意，用户认证的过程未定义在此步骤中，在此之前就应该已经完成。微信确定第三方应用的身份，用户扫码确认登录。
3. 如果用户同意授权，授权服务器将转向第三方应用在第 1 步调用中提供的回调 URI，并附带上一个授权码和获取令牌的地址作为参数，这是第二次客户端页面转向。用户确认登录，微信扫码界面跳转回调URL，附带授权码和获取令牌地址。授权码的目的是不让客户知道密钥。
4. 第三方应用通过回调地址收到授权码，然后将授权码与自己的 ClientSecret 一起作为参数，通过服务器向授权服务器提供的获取令牌的服务地址发起请求，换取令牌。该服务器的地址应该与注册时提供的域名处于同一个域中。第三方服务器请求微信授权服务器，带着授权码和自己的密钥，换取令牌，要求此时域名和在微信注册密钥的域名相同。
5. 授权服务器核对授权码和 ClientSecret，确认无误后，向第三方应用授予令牌。令牌可以是一个或者两个，其中必定要有的是访问令牌（Access Token），可选的是刷新令牌（Refresh Token）。访问令牌用于到资源服务器获取资源，有效期较短，刷新令牌用于在访问令牌失效后重新获取，有效期较长。微信授权服务器确认后返回访问令牌和刷新令牌。
6. 资源服务器根据访问令牌所允许的权限，向第三方应用提供资源

## 基于角色的访问控制RBAC

RBAC 模型在业界有很多种说法，其中，最具系统性且得到了普遍认可的说法，是美国乔治梅森（George Mason）大学信息安全技术实验室提出的 RBAC96 模型。

为了避免对每一个用户设定权限，RBAC 将权限从用户身上剥离，改为绑定到“角色”（Role）上，“权限控制”这项工作，就可以具体化成针对“角色拥有操作哪些资源的许可”这个逻辑表达式的值是否为真的求解过程。

谁（User）拥有什么权限（Authority）去操作（Operation）哪些资源（Resource）。

用户 ---隶属--- 角色 ---拥有--- 许可--- 操作--- 资源

- 所有的访问控制模型，实质上都是在解决同一个问题：谁（User）拥有什么权限（Authority）去操作（Operation）哪些资源（Resource）。
- 为避免对每一个用户设定权限，RBAC 提出了角色和许可等概念，角色为的是解耦用户与权限之间的多对多关系，而许可为的是解耦操作与资源之间的多对多关系。
- 建立访问控制模型的基本目的就是为了管理垂直权限和水平权限。垂直权限即功能权限，水平权限则是数据权限，它很难抽象与通用。

# 令牌/凭证

## Cookie-Session

可是，HTTP 协议的无状态特性，又有悖于我们最常见的网络应用场景，典型的就是认证授权，毕竟系统总得要获知用户身份才能提供合适的服务。因此，我们也希望 HTTP 能有一种手段，让服务器至少有办法区分出发送请求的用户是谁。

所以，为了实现这个目的，RFC 6265规范就定义了 HTTP 的状态管理机制，在 HTTP 协议中增加了 Set-Cookie 指令。这个指令的含义是以键值对的方式向客户端发送一组信息，在此后一段时间内的每次 HTTP 请求中，这组信息会附带着名为 Cookie 的 Header 重新发回给服务端，以便服务器区分来自不同客户端的请求。

根据每次请求传到服务端的 Cookie，服务器就能分辨出请求来自于哪一个用户。由于 Cookie 是放在请求头上的，属于额外的传输负担，不应该携带过多的内容，而且放在 Cookie 中传输也并不安全，容易被中间人窃取或篡改，所以在实际情况中，通常是不会像这个例子一样，设置“id=icyfenix”这样的明文信息的。

一般来说，系统会把状态信息保存在服务端，而在 Cookie 里只传输一个无字面意义的、不重复的字符串，通常习惯上是以 sessionid 或者 jsessionid 为名。然后，服务器拿这个字符串为 Key，在内存中开辟一块空间，以 Key/Entity 的结构，来存储每一个在线用户的上下文状态，再辅以一些超时自动清理之类的管理措施。

这种服务端的状态管理机制，就是今天我们非常熟悉的 Session。Cookie-Session 也就是最传统的，但在今天依然广泛应用于大量系统中的、由服务端与客户端联动来完成的状态管理机制。

不过，Cookie-Session 在单节点的单体服务环境中确实是最合适的方案，但当服务器需要具备水平扩展服务能力，要部署集群时就有点儿麻烦了。因为 Session 存储在服务器的内存中，那么当服务器水平拓展成多节点时，我们在设计时就必须在以下三种方案中选择其一：

- 要么就牺牲集群的一致性（Consistency），让均衡器采用亲和式的负载均衡算法。比如根据用户 IP 或者 Session 来分配节点，每一个特定用户发出的所有请求，都一直被分配到其中某一个节点来提供服务，每个节点都不重复地保存着一部分用户的状态，如果这个节点崩溃了，里面的用户状态便完全丢失。
- 要么就牺牲集群的可用性（Availability），让各个节点之间采用复制式的 Session，每一个节点中的 Session 变动，都会发送到组播地址的其他服务器上，这样即使某个节点崩溃了，也不会中断某个用户的服务。但 Session 之间组播复制的同步代价比较高昂，节点越多时，同步成本就越高。
- 要么就牺牲集群的分区容错性（Partition Tolerance），让普通的服务节点中不再保留状态，将上下文集中放在一个所有服务节点都能访问到的数据节点中进行存储。此时的矛盾是数据节点就成为了单点，一旦数据节点损坏或出现网络分区，整个集群都不能再提供服务。

## JWT：解决认证授权问题的无状态方案

JWT 令牌，跟 Cookie-Session 并不是完全对等的解决方案，它只用来处理认证授权问题，是 Cookie-Session 在认证授权问题上的替代品，充其量能携带少量非敏感的信息。而我们不能说，JWT 要比 Cookie-Session 更加先进，它也更不可能全面取代 Cookie-Session 机制。

当服务器存在多个，客户端只有一个时，那就把状态信息存储在客户端，每次随着请求发回服务器中去。可是奇怪了，前面我才说过，这样做的缺点是无法携带大量信息，而且有泄露和篡改的安全风险。

### JWT 令牌的三部分结构

JWT 令牌是以 JSON 结构（毕竟名字就叫 JSON Web Token）存储的，其结构总体上可以划分为三个部分，每个部分用点号“ . ”分隔开。

**令牌的第一部分是令牌头（Header）**

```
{
  "alg": "HS256",
  "typ": "JWT"
}
```

它描述了令牌的类型（统一为 typ:JWT）以及令牌签名的算法，示例中 HS256 为 HMAC SHA256 算法的缩写。

HMAC”的前缀，这是指散列消息认证码（Hash-based Message Authentication Code，HMAC）。可以简单将它理解为一种带有密钥的哈希摘要算法，实现形式上通常是把密钥以加盐方式混入，与内容一起做哈希摘要。HMAC 哈希与普通哈希算法的差别是，普通的哈希算法通过 Hash 函数结果易变性，保证了原有内容未被篡改，而 HMAC 不仅保证了内容未被篡改过，还保证了该哈希确实是由密钥的持有人所生成的。

**令牌的第二部分是负载（Payload）**，这是令牌真正需要向服务端传递的信息。针对认证问题，负载至少应该包含能够告知服务端“这个用户是谁”的信息；针对授权问题，令牌至少应该包含能够告知服务端“这个用户拥有什么角色 / 权限”的信息。JWT 的负载部分是可以完全自定义的，我们可以根据具体要解决的问题，设计自己所需要的信息，只是总容量不能太大，毕竟它受 HTTP Header 大小的限制。

```
{
  "username": "icyfenix",
  "authorities": [
    "ROLE_USER",
    "ROLE_ADMIN"
  ],
  "scope": [
    "ALL"
  ],
  "exp": 1584948947,
  "jti": "9d77586a-3f4f-4cbb-9924-fe2f77dfa33d",
  "client_id": "bookstore_frontend"
}
```

JWT 在 RFC 7519 标准中，推荐（非强制约束）了七项声明名称（Claim Name）

- iss（Issuer）：签发人。
- exp（Expiration Time）：令牌过期时间。
- sub（Subject）：主题。
- aud （Audience）：令牌受众。
- nbf （Not Before）：令牌生效时间。
- iat （Issued At）：令牌签发时间。
- jti （JWT ID）：令牌编号。

参考https://www.iana.org/assignments/jwt/jwt.xhtml

**令牌的第三部分是签名（Signature）**。签名的意思是：使用在对象头中公开的特定签名算法，通过特定的密钥（Secret，由服务器进行保密，不能公开）对前面两部分内容进行加密计算，产生签名值。

```
HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload) , secret)
```

签名的意义在于，它可以确保负载中的信息是可信的、没有被篡改的，也没有在传输过程中丢失任何信息。因为被签名的内容哪怕是发生了一个字节的变动，也会导致整个签名发生显著变化。

此外，由于签名这件事情，只能由认证授权服务器完成（只有它知道 Secret），任何人都无法在篡改后重新计算出合法的签名值，所以服务端才能够完全信任客户端传上来的 JWT 中的负载信息。JWT 默认的签名算法 HMAC SHA256 是一种带密钥的哈希摘要算法，加密与验证过程都只能由中心化的授权服务来提供，所以这种方式**一般只适合于授权服务与应用服务处于同一个进程中的单体应用**。

### JWT 令牌的缺陷

由于 JWT 本身可以携带少量信息，这十分有利于 RESTful API 的设计，比较容易地做成无状态服务。

- 令牌难以主动失效   JWT 令牌一旦签发，理论上就和认证服务器没有什么瓜葛了，在到期之前就会始终有效，除非我们在服务器部署额外的逻辑去处理失效问题，而这对某些管理功能的实现是很不利的。比如说，一种十分常见的需求是：要求一个用户只能在一台设备上登录，在 B 设备登录后，之前已经登录过的 A 设备就应该自动退出。 如果我们采用 JWT，就必须设计一个“黑名单”的额外逻辑，把要主动失效的令牌集中存储起来，而无论这个黑名单是实现在 Session、Redis 还是数据库当中，都会让服务退化成有状态服务，这就降低了 JWT 本身的价值。但在使用 JWT 时，设置黑名单依然是很常见的做法，需要维护的黑名单一般是很小的状态量，因此在许多场景中还是有存在价值的
- 相对更容易遭受重放攻击  Cookie-Session 也是有重放攻击问题的，只是因为 Session 中的数据控制在服务端手上，应对重放攻击会相对主动一些。
- 只能携带相当有限的数据  HTTP 协议并没有强制约束 Header 的最大长度，但是，各种服务器、浏览器都会有自己的约束，比如 Tomcat 就要求 Header 最大不超过 8KB，而在 Nginx 中则默认为 4KB。
- 必须考虑令牌在客户端如何存储   如果在授权之后，操作完关掉浏览器就结束了，那把令牌放到内存里面，压根不考虑持久化，其实才是最理想的方案。但并不是谁都能忍受一个网站关闭之后，下次就一定强制要重新登录的。这样的话，你想想客户端该把令牌存放到哪里呢？Cookie？localStorage？还是 Indexed DB？它们都有泄露的可能，而令牌一旦泄露，别人就可以冒充用户的身份做任何事情。
- 无状态也不总是好的

# 保密

**摘要代替明文**

**先加盐值再做哈希是应对弱密码的常用方法**

**将盐值变为动态值能有效防止冒认**

**加入动态令牌防止重放攻击**

**启用 HTTPS 来应对因嗅探而导致的信息泄露问题**

**进一步提升保密强度的不同手段**

**存不存在某种绝对安全的保密方式？**

信息论之父香农就严格证明了一次性密码（One Time Password）的绝对安全性。但是使用一次性密码必须有个前提，就是我们已经提前安全地把密码或密码列表传达给了对方。比如说，你给朋友人肉送去一本存储了完全随机密码的密码本，然后每次使用其中一条密码来进行加密通讯，用完一条丢弃一条。这样理论上可以做到绝对的安全，但显然这种绝对安全对于互联网来说没有任何的可行性。

## 客户端加密的意义

为了保证信息不被黑客窃取而去做客户端加密，其实没有太大意义，对绝大多数的信息系统来说，启用 HTTPS 可以说是唯一的实际可行的方案。但是！为了保证密码不在服务端被滥用，而在客户端就开始加密的做法，还是很有意义的。

应该把明文密码这种东西当成是最烫手的山芋来看待，越早消灭掉越好。毕竟把一个潜在的炸弹从客户端运到服务端，对绝大多数系统来说都没有必要。

# 分布式共识

