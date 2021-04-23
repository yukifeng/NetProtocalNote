# 网络协议笔记5-应用层3（HTTPS）
- https译为超文本传输安全协议，即HTTP协议增加了SSL协议或一个TLS协议（SSL协议的升级版本）来加密报文的
- https的默认端口号是443
- TLS协议可以用于其他协议，比如FTP->FTPS,SMTP->SMTPS

## TLS
- 译为传输层安全性协议，前身为SSL（安全套接层）
- TLS工作在应用层和传输层之间，作为桥梁
- 使用TLS：
	- 需要支付证书的费用
	- 需要加解密的消耗
	- 会降低访问速度

## HTTPS的通讯过程
- 分为3阶段

1. TCP的3次握手
2. TLS的连接
3. HTTP的请求和响应

### TLS1.2的连接过程（每次交互会有ACK确认响应回复）
1. 客户端发送给服务器： Client Hello
	- 包含TLS版本号
	- 支持的加密组件：指所使用的加密算法和密钥长度
	- 一个随机数
	
![4bGZL4](https://raw.githubusercontent.com/yukifeng/pictureBed/master/uPic/2021/04/13/4bGZL4.png)	
2. 服务器发送给客户端：Server Hello
	- 包含TLS版本号
	- 选择的加密组件：在第一步中客户端支持加密组件列表中选择的
	- 一个随机数
	
![ulepcv](https://raw.githubusercontent.com/yukifeng/pictureBed/master/uPic/2021/04/13/ulepcv.png)
3. 服务器发送给客户端：Certificate
	- 包含服务器的被CA签名过的公钥证书
![pDXpCU](https://raw.githubusercontent.com/yukifeng/pictureBed/master/uPic/2021/04/13/pDXpCU.png)
4. 服务器发送给客户端：Server Key Exchange
	- 包含实现ECDHE算法的其中一个参数（Server Params）
	- ECDHE是密钥交换算法，为了防止伪造，Server Params经过了服务器私钥签名
![TcEImJ](https://raw.githubusercontent.com/yukifeng/pictureBed/master/uPic/2021/04/13/TcEImJ.png)
5. 服务器发送给客户端：Server Hello Done
	- 告诉客户端协商部分已经结束
	- 此时，客户端与服务器通过明文共享了Client Random、Server Random、 Server params
	- 客户端拿到了服务器的公钥证书，接下来会验证公钥的真实性 
![n9Ii2R](https://raw.githubusercontent.com/yukifeng/pictureBed/master/uPic/2021/04/13/n9Ii2R.png)
6. 客户端发送给服务器：Client Key Exchange
	- 实现ECDHE算法的另一个参数（Client Params）
	- 此时双方拥有了ECDHE算法的2个参数，可以通过该算法计算出一个新的密钥串：Pre-master secret
	- 然后用Client Random、Server Random、Pre-master secret生成一个主密钥
	- 然后利用主密钥衍生出其他密钥：双方发送用的会话密钥
![IAVgr4](https://raw.githubusercontent.com/yukifeng/pictureBed/master/uPic/2021/04/13/IAVgr4.png)
7. 客户端发送给服务器：Change Cipher Spec
	- 告诉服务器之后的通信会采用计算出来的会话密钥进行加密
![6KSGOL](https://raw.githubusercontent.com/yukifeng/pictureBed/master/uPic/2021/04/13/6KSGOL.png)
8. 客户端发送给服务器：Finished
	- 包含此前全部报文整体校验值（摘要），加密后发送给服务器
	- 此次握手是否成功，以服务器能否正确解密该报文为判定标准
![vv5ahr](https://raw.githubusercontent.com/yukifeng/pictureBed/master/uPic/2021/04/13/vv5ahr.png)
9. 服务器发送给客户端：Change Cipher Spec
10. 服务器发送给客户端：Finished
- 到此，双方验证加解密无误，握手结束，后面传递加密的HTTP请求和响应
![UmzcBC](https://raw.githubusercontent.com/yukifeng/pictureBed/master/uPic/2021/04/13/UmzcBC.png)
