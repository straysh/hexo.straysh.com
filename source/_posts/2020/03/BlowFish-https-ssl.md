---
title: BlowFish_https_ssl
date: 2020-03-10 19:00:20
tags: 
- Golang
- Linux
categories: 
- 博文
toc: true
---
# SSL到底用的哪个SSL证书端口
# 看看啥是“SSL证书端口”
常有人问“SSL到底使用的是哪个端口？”，或者“常用的SSL证书端口是哪些？”。答案是没有。`SSL/TLS`本身不使用任何端口，而`HTTPS`使用443端口。

`SSL/TLS`是基础设施，允许其他协议如`HTTPS`或`DNS`等基于`TLS`工作。这些协议各自使用特定的端口，但`SSL/TLS`并不需要。

# 两个不同的术语：SSL证书端口 vs HTTPS端口
通常我们把TLS证书叫作SSL证书。因此当我们提到"SSL证书443端口"一点也不奇怪。只需要记住，当有人提到SSL证书端口，通常是指HTTPS。

~~端口也指代软件用来与硬件通信的内存地址。在此处特指`I/O`地址。~~

在进入故宫前，会有栅栏把人群分成若干组，否则人群蜂拥向大门简直是灾难。这个比喻并100%准确，但是端口差不多就是这些栅栏的作用。端口来管控数据的收发。HTTP使用80端口通信。HTTPS使用443端口通信。这就是为啥老说SSL证书端口443了。



# HTTPS/SSL证书端口有多少个？
专家将网络链接划分为四层（若基于OSI则是七层）。[端口在传输层使用](/2020/02/27/网络-tcp-udp/#TCP-UDP工作在传输层)。
一共有`65535`个端口，从`0000h`到`FFFFh`。


<table><tr><th>PORT #</th><th>功能</th></tr><tr><td>80</td><td>HTTP</td></tr><tr><td>443</td><td>SSL</td></tr><tr><td>21</td><td>FTP</td></tr><tr><td>990</td><td>FTPs</td></tr><tr><td>22</td><td>SFTP/SSH</td></tr><tr><td>3306</td><td>MySQL</td></tr></table>

SSL(和后继者TLS)直接工作在TCP协议上。因此更高层的协议如HTTP可以无需修改的提供安全连接。在SSL层下的HTTP即HTTPS。

正确使用SSL/TLS后，攻击者只能从链路中看到IP地址和端口号，传输数据的估算大小，加密方式和压缩方式。黑客也可以终止连接，这样做的话客户端和服务端都将知道被第三方介入了。

通常，攻击者也能得到你请求的域名，但URL中其他部分并不能看到。HTTPS本身并不会暴露域名，但浏览器会首先使用域名去获取IP地址。

# 对协议的高层级描述
在建立TCP连接后，由客户端发起SSL握手。客户端会发送一些规范：
- 使用的SSL/TLS版本
- 期望使用何种加密算法（提供一个列表供选择）
- 期望使用何种压缩算法（提供一个列表供选择）

服务器会挑选出自己支持的SSL/TLS版本中最高的，从客户端提供的加密套件集合中挑选一套，并从中随机挑选一种压缩算法。

做完这些基础设置后，服务器向客户端发送它的证书`certiticate`。该证书要么被客户端信任，要么被浏览器信任的第三方证书机构信任。例如，若客户端信任GeoTrust，那么浏览器可以信任google.com，因为GeoTrust签发了Google的证书。

客户端验证了证书且确认服务器是其自称的服务器后，会生成一个随机秘钥`randomkey`，并使用接收的服务器公钥(或其他`PreMasterSecret`，取决于使用的密码套件)加密，发送到服务器。服务器使用私钥解密出秘钥`randomkey`。此时，双方可以使用该秘钥来安全的传输数据了。
![tls_ssl_encryption](/images/linux/tls_ssl_encryption.png)

---
名词解释：
1. SSL - 该协议会给客户端与服务器之间的通信进行加密，保障通信的安全
2. TLS - 是SSL的一个新版本，该协议与SSL 3.0之间的差异并不显著，但这些差异的存在，已使得TLS 1.0和SSL 3.0之间不能互操作

所以，目前当谈论SSL时实际指TLS。

参考资料：
