title: "HTTPS之痛：为什么还不能全站HTTPS？"
date: 2016-01-19 09:18:19
tags:
- HTTPS
categories:
- 方案

---

原文：<http://webappsec-test.info/~bhill2/DifferentTakeOnOE.html>

<!--more-->

-----------------------------

全文包含如下部分：
1. 是证书（certificates）的原因吗？
2. 有了证书之后，可以开启HTTPS了吗？
3. 什么是混合内容（mixed content），为什么我们要知道它？
4. 修复混合内容载入问题
5. 修复HTTPS依赖死锁的问题
6. 总结

# 为什么我们还不能做到全站HTTPS？

我们所有人都不喜欢被偷窥而喜欢通过加密保护自己，但是为什么HTTPS的覆盖度还是没有那么广？

## 是因为“证书”的问题吗？

第一个最常被用来作为对HTTPS推广和普及的阻碍是获取、配置和维护一个合法的HTTPS证书的成本问题。你首先必须要找到一个证书授权的服务机构，然后需要提供你自己的身份认证，为所需的证书付款，然后才能把这个证书部署到自己的网站上去。除此之外，你还可能需要一个独立IPV4的IP地址去支持那些没有SNI（Server Name Indication）的老客户，并且要不停为这个证书续费以免过期。

大多数针对加密的建议都是基于这一个理由（证书），“NSA（联邦监察局）正在记录所有关于你的上网活动，因此为什么我们就算没有一个合法的证书也要把我们的网站加密起来”。这类建议主要是用来提高政府机构大量监视时候的成本，而没有注意到更多的对证书的需求是用来保护不受网络上攻击者对目标用户的攻击和流量窃取。

笔者很尊重要求所有网站加密的本意，但是同时也相信这种建议实际上没有解决问题。首先，因为OE（Own Encryption）缺少认证，它不能保证与其他依赖的资源之间有合法和安全的协议（译者注：加密学里最基本的协议，建议参阅公钥私钥等篇章），因为用户和资源的供应者之间需要注意网络上的攻击者没有在中间提供恶意的认证，所以实际上通过网站的私有证书加密不能解决任何关于网络安全的问题。其次，我不认为证书是HTTPS所需要面对的最大的问题。[Let's Encrypt](https://letsencrypt.org/)公司已经意识到HTTPS所面对的证书的问题是不能自动化安装和部署，因此提供了针对现在主流的服务架构的自动化证书部署方案，用来解决证书部署这类比较棘手的问题。除此之外，HTTPS还有许多的困难需要解决。

## 如果我们现在有了HTTPS证书，那我们可以启用全站HTTPS了吗？

好吧，可能是可行的，但是也有可能不行。如果你的网站的所有的HTML资源都是来自同一个HOST下的子资源，而且你使用了相对路径，那这是可行的，否则你的网站将会因为启用了HTTPS而崩溃。

原因是在你网页里可能使用了混合内容（mixed content）。

## 什么是混合内容，为什么我需要解决混合内容的问题？

混合内容是一个名词，用来描述一个文档从HTTPS和HTTP同时加载内容的情景。

比如下面这种情景：你是OriginA的运营者，绿色的圆圈表示同时支持HTTP和HTTPS资源，红色圆圈表示HTTP资源，虚线表示这些资源之间的拉取是通过HTTP，实线表示这些资源之间是通过HTTPS联系。假定所有的链接都是绝对路径。一个红色的X表示该资源拉取将失败。

![](/images/https1.PNG)

现在假设你拿到一个证书，在OriginA上开启了HTTPS，那么现在的资源关系图将变为：

![](/images/https2.PNG)

如果你没有更新HTML文档里的绝对路径，那么OriginA将依然会通过HTTP载入所有所需的资源。大部分现代的浏览器将阻止通过HTTP去载入开启了HTTPS的网站，或者给用户负面的警告信息。由于在OriginA里的JS和JPG资源被阻止，所以OriginA的网站将会出现崩溃的情况。另外，即使网站里存在JS资源，但是由于是通过HTTP链接，浏览器依然拒绝去加载。

## 为什么浏览器要去阻止混合内容载入？为什么不让站点运营者决定是否载入？

大多数的网络安全模型里，源（Origin）是通过自身的信息（schema+host+port）被认证的。一个通过HTTPS加载的文档可以把用户导航到一个在GET里拥有敏感信息的非安全站点，它可以POST或者postMessage()到一个非安全的Schema或者源（Origin）里。同时一个HTTPS资源也能接受来自HTTP文档的GET、POST或者onMessage()。因此，如果我们没有正式的信息流安全控制的前提下，为什么浏览器要阻止混合内容？如果我们可以跨HTTP和HTTPS POST，为什么不可以XHR？

实际上浏览器所使用的一个正式的安全模型是由Bell和LaPadula提出的一个“[Tranquility](https://en.wikipedia.org/wiki/Bell%E2%80%93LaPadula_model)”属性。简单来说，Tranquility强制浏览器在一个安全的文档内交互的时候，不允许变成一个不安全的文档。

在所有可能的、复杂的和潜在的网络安全里，浏览器最终找到一个可以半依赖的安全指示器：URL和Schema锁定。如果你在浏览器内输入了“https”，或者被导航到一个文档包含一个小锁的图标，浏览器就会保证里面的内容是受到保护的。

如果你的HTTPS文档通过HTTP加载script或者images，tranquility所做的安全保证将会立即被破坏。究竟如何被破坏或者破坏到什么程度可能基于不同的情景有很大差异，但是浏览器不知道这种程度的差异，因此就只需要简单的阻止。

这对用户来讲是一件好事，但是对于网站运营者来讲，在加载混合内容的时候，就需要考虑大量的依赖问题，因为一个https文档不能显示或者加载http依赖，所以实际上依赖问题是阻碍100%HTTPS所面临的最大的成本。

### 修复混合内容（mixed content）

当你开始着手修复mixed content的时候，迁移到HTTPS的成本开始真正出现。除了服务器配置和证书部署之外，移除混合内容是成本最高的部分，而且不容易做到自动化，因为你需要理解所有可能涉及到内容，然后修改测试保证每一个都可以正常工作。

对于一个复杂的站点来说，不是简单的对所有的资源通过mod_rewrite开启HTTPS，你可能遭遇http schema问题在很多地方，包括：静态内容，动态内容，数据库，第三方等。

一个新的[规范](http://www.w3.org/TR/upgrade-insecure-requests/)正在开发中（已经被firefox和chrome）所支持，用来解决自动升级http到https的问题。

**Before**

![](/images/https3.PNG)

**After upgrade-insecure-requests**

![](/images/https4.PNG)

upgrade-insecure-requests 可以有效帮助OriginA实现自己的全站HTTPS，因为混合内容已经被自动修复，远程依赖也自动修改为HTTPS，但是我们依然有一个坏掉的资源链接（OriginB），因为OriginB不支持HTTPS。

正是由于多个站点之间的大量的互相依赖的问题，导致可能这些站点之间都不能开启HTTPS，比如如下。

### 互相依赖的资源关系，导致所有关联方都不能开启HTTPS

![](/images/https5.PNG)

可能使情况更坏的是，得到站点依赖并不是一件容易的事情。一旦你开启HTTPS，可能一些错误直接会曝露到用户面前，而你对这些错误基本上没有办法可以预先防止。（`default-src https`安全策略指令可能会在链接错误时告诉你，但是在这个错误发生之前，几乎没有容易的办法去测试得到。）

## 解决问题

我们所缺少的是一个在http和https之间的中间态。这个中间态可以有下列属性：

- 允许安全的源使用一种不改变安全tranquility的方式加载自身所提供资源
- 不强制资源去提供不成熟的未经验证的安全tranquility
- 低成本和低风险部署；理想状况下零内容级别的改变，仅仅需要服务器配置更新
- 允许探测那些因为违反了安全tranquility而影响用户体验的错误

**引入中间态之前**

![](/images/https6.PNG)


**引入中间态之后**

![](/images/https7.PNG)

首先，OriginB打开HTTPS中间态模式，意味着它的资源依然不能使用HTTPS schema，但是具有TLS能力，同时拥有了一个合法的证书。这是零成本的，因为它并没有对用户或者浏览器保证自己的安全态或者tranquility。

现在OriginB处于https中间态，OriginA同时也打开了HTTPS，OriginA有一个JS文件依赖OriginB，如果一个浏览器能够识别HTTPS中间态，那么就会尝试发起一个TLS链接到OriginB，用HTTP来获取OriginB的JS资源。如果这种乐观的HTTPS中间态的尝试失败了，那么浏览器就会把内容标记为混合状态，同时触发阻止资源的进一步拉取，因此我们并没有降低OriginA的安全特性。如果HTTPS中间态的尝试成功了，那么标准的对OriginA的安全模型并没有被破坏，同时，tranquility也被满足，没有混合内容的警告，资源获取阻塞同时会被触发。

因此，现在OriginA可以升级，OriginC也可以升级，OriginB依然有一个OriginD的HTTP依赖，虽然OriginD不能提供https，但是通过https中间态，它并不会影响OriginA和OriginB的HTTPS升级。

现在这里的这个中间态的最关键的点就是：OriginA对OriginB的JS资源的依赖，它需要满足所有的TLS所需要的属性，但是HTTP中间态透明切换是依赖于具体的中间态的具体实现。

## 如何引入HTTPS中间态？

首先，通过在服务器上开启https同时引入一个过滤器，该过滤器可以返回一个text/html文档或者直接返回404，可以解开资源依赖带来的死锁，同时也不用在文档里引入混合内容。实际上这是一个令人惊奇的完美的方案，除了一些极端的场景，比如CORS。再者，通过这种方案，一下子就可以满足我们希望的理想的4条属性中的3条。

至于第4条不能满足的属性 - “有能力探测自身所依赖资源的状态，以便于随时可以反转到真正的https。”如果浏览器已经支持了“Content-Security-Policy-Report-Only”中的“upgrade-insecure-requests”特性，则：

1. 试图去升级到HTTPS
2. 如果升级到HTTPS失败，则：
    1. 回落到http fetch
    2. 触发一个失败了的报告

是否有可能不需要做任何其他更多的事情的同时，通过一些基本的upgrade-insecure-requests设置就可以满足全站https的要求？

![](/images/https8.PNG)    

**`<iframe>`问题**

如果只通过过滤HTML资源加载HTTPS是非足够的，因为HTML有可能依赖其他的HTML，比如通过`iframe`：

![](/images/https9.PNG)

如果要解决一个iframe的依赖树问题，也是通过“Optimistic Secure Tranquility”的方案。首先，乐观的通过TLS载入HTML文档，并且通过一个安全的framed上下文环境开启Tranquility。为什么这种方案可行呢？原因是没有能够通过TLS升级加载的文档已经早早的加载失败，因此我们在这里开启HTTPS并不会引入新的关于混合内容的问题。

**https-transitional with ALPN and HTTP Alt-Svc**

ALPN允许一个客户端与一个服务端通过TLS进入一个额外的应用层协议。

假设我们有这样一个新型的ALPN协议类型：https-transitional。然后服务器端看到的客户端的请求是：把我通过http协议链接各种所需的资源和配置，但是通过TLS，而不是HTTPS的直接连接。根据HTTP ALT-Svc草案，一个完整的TLS连接必须要完成，而且服务器端必须要有一个证书，该证书必须匹配原初的HTTP host。而transitional机制与Alt-Svc又不同：

一个资源如果通过“https-transitional”载入的，将会在user-agent里有下列属性：

- 不会被标记为混合内容
- 不会限制混合内容的获取
- 除非当前文档的任何父级frame限制了混合内容，则该文档会限制混合内容，并自动应用了`upgrade-insecure-request`

upgrade-insecure-request将会被修改为：

- 当一个次级资源通过http升级加载的时候，将会连接到服务器端的https endpoint中去，同时增加了https-transitional ALPN协议，这个协议是优先于http/spdy/h2协议的
- 如果服务端可以理解https-transitional ALPN协议，将会通过TLS加载http内容
- 如果不理解https-transitional协议，将会自动切入https

对混合内容的抑制将也会被修改：如果混合内容被阻挡的时候，user agent将不会自动试图去升级http到https，因为没有显式的保证http和https两个scheme里的内容是等同的。而https-transitional scheme则会有这种保证，因此一个user agent将会总是首先尝试被阻挡的内容为http-transitional。

当一个文档被作为transitional模式载入的时候，user agent将会试图将所有被阻挡的资源作为混合内容，如果同时`upgrade-insecure-requests`被设置过了，则将会在失败时静默回滚到http加载。这种机制必须在console中报告错误，应该提供一种机制可以让站点获取到类似于content security policy的reports。

![](/images/https10.PNG)

上图就应用了这些规则：OriginA通过frame加载OriginB的HTML时，将不会遇到任何的warning，因为OriginB支持transitional模式。但是，如果OriginB通过frame加载来自于OriginD的混合资源时，由于OriginD不支持transitional的自动升级，这次的获取将会被静默阻止，原因是为了维护tranquility。这种部分的加载失败还是比啥也不做好，原因是如果不做这种transitional的尝试，OriginA对OriginD的混合内容的依赖将会直接把错误曝露到用户面前。

### 性能影响

对于https资源来说，对比于upgrade-insecure-request，不会有任何性能影响。

唯一一个可能影响的地方在于Alt-Svc header，因为user agent将会尝试把所有的次级资源通过TLS升级加载，但是并不是所有的都可能成功的，这会引入一些延迟。User agent可能会通过一些优化比如记住TLS是否可以应用到某一个host上，通过通过一些并发机制同时加载可升级的和不可升级的，这些优化可能需要一些策略调整才能达到最佳。

## Locking Down

在某些情况下，站点的设计者们想完全地抛弃http，一个从https上下文加载的文档将从来不用回档到纯文本模式或者非授权的连接。但是一个https transitional乐观升级方案并不能阻挡来自于攻击者的故意从https到http的降级，如何避免这种攻击？

我们首先可以采用一些设计模式HTTP Strict Transport Security，用来解决HTTPS站点不再采用HTTP连接的场景，除此之外，还有Alt-Svc Header：

1. 设置Alt-Svc的freshness为“infinite”，将会保证站点只使用https和https transitional模式
2. 给transitional模式下的文档进入tranquility的可能，保证混合内容被限制。
3. 逐步抛弃http

- end - 
