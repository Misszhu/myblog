# HTTP 和 HTTPS的区别

我之前写过一篇文章叫《白话HTTP》，当时写这篇的文章的起源就是有同事问我：**HTTP和HTTPS的区别**，我答不上来，就去查阅了一些资料，发现很多文章我都不是很理解。于是我决定先从HTTP开始了解，所以有了《白话HTTP》。

其实到现在，准备写这篇文章的这一刻，我仍然还没深入理解一个完整的HTTPS通信过程是怎么样的。我的目的是记录下我的学习笔记，帮助自己梳理出一个完整的 **HTTPS 请求过程**。

### 太长不看

HTTP 和 HTTP 的区别**主要**有以下几点：

- HTTP 是明文传输的，HTTPS 是加密传输的，HTTPS 比HTTP 更加安全
- 使用 HTTPS 需要到 CA （Certificate Athority 数字证书认证机构）申请证书，证书有收费的，也有免费的
- HTTP 的响应速度比 HTTPS 更快，因为 HTTP 使用 TCP 三次握手建立连接，客户端和服务器需要交换三个包，而 HTTPS 除了 TCP的三个包，还需要加上 SSL 握手的9个包，一共是12个包
- HTTPS 实际上是构建在 SSL/ TLS 之上的 HTTP协议，所以 HTTPS 比 HTTP 更耗费服务器资源

而一个简单的HTTPS 请求过程是这样的：

1. 客户端向服务器索要并验证公钥
2. 双方协商“对话密钥”
3. 双方采用“对话密钥”进行加密通信

文章标题的是《HTTP和HTTP的区别》，上面用三言两语已经能说清楚了，不过我想记录的还有这些内容▼：

- TCP三次握手
- HTTPS 请求过程
- 非对称加密 & 对称加密

## TCP三次握手

HTTP 是使用 TCP三次握手建立连接的。TCP 是传输层的协议，而且传输层是常见的互联网模型中的其中一层。好吧，其实我说的都是废话。现在我们只需要知道**在进行真正的HTTP通信之前，会先使用TCP三次握手确认双方身份**就行了。

分点描述TCP三次握手的过程是这样的（是的，我很喜欢分点说）：

1. 第一次握手：客户端向服务器发送 SYN 包，尝试连接服务器，客户端进入 SYN_SEND 状态；
2. 第二次握手：服务器接收客户端 SYN 包并确认，同时向客户端送一个 SYN + ACK 包，此时服务器进入 SYN_RECV 状态； 
3. 第三次握手：客户端接收到服务器的 SYN + ACK 包，向服务器发送 ACK 包。此包发送完毕，客户端和服务器双方进入 ESTABLISHED   状态。

双方进入**ESTABLISHED**状态，说明连接已经建立，接下来开始进行真正的通信。

上图体会一下:

![image](https://github.com/Misszhu/myblog/raw/master/HTTP/image/HTTP-HTTPS1.png)

如果用我们平时的对话模拟三次握手，阿婷看了对话过程简直想打人：

1. 喂喂喂，你听得到吗？
2. 听得到，你听得到我说？
3. 听到听到，我们开始说话吧！

![image](https://github.com/Misszhu/myblog/raw/master/HTTP/image/HTTP-HTTPS2.png)

是不是很啰嗦~~有事就不能直说吗，其实TCP这样做是有他的道理的，互联网是开放环境，通信双方都是未知身份，为了保证通信过程的安全，先确认身份再进行通信的步骤是非常有必要的。

## HTTPS请求过程

HTTPS请求的整体流程是这样的：

1. 客户端生成一个随机数（client random），给出协议的版本号、以及客户端支持的加密方法，尝试连接服务器；
2. 服务器确认双方使用的加密方法，，把公钥（通过证书的形式）和一个服务器生成的随机数（server random）发给客户端；
3. 客户端拿到公钥（证书）后，确认证书有效，再生成一个的随机数（Premaster secret），并使用公钥加密这个随机数，发送给服务器；
4. 服务器使用自己的私钥解密，以获取客户端发来的随机数（Premaster secret）；
5. 到这里，客户端和服务器都同时拥有了三个随机数，双方会在接下来的通信中，使用这是三个随机数，生成对话密钥（session key），用来加密接下来的整个通信过程。

到第4步为止（包括第4步），整个握手阶段全部结束。接下来，客户端与服务器进入加密通信，就完全是使用普通的HTTP协议，只不过用"对话密钥"加密内容。

再结合一张流程图感受一下整个过程：

![image](https://github.com/Misszhu/myblog/raw/master/HTTP/image/HTTP-HTTPS3.png)

## 非对称加密和对称加密

上面第3步客户端用公钥加密随机数，服务端用私钥解密随机数，属于非对称加密。再说得简单一点就是：加密和解密使用不同密钥的算法是非对称加密。

而第5步，客户端和服务器双方都使用约定生成的“对话密钥”加密和解密通信过程，属于对称加密。再翻译一下：加密和解密使用相同密钥的算法是对称加密。

非对称加密执行稍慢，但安全性高；对称加密执行快，安全性低。

第2步的公钥，是一把还没上锁的锁头，服务器把锁头（公钥）客户端，客户端用锁头把他自己生成的随机数锁好，发送给服务器。解开这把锁头的钥匙（私钥）只有服务器有。





