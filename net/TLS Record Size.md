# TLS Record Size

## 原理

TLS 协议由两层协议组成：TLS 握手协议和 TLS 记录协议（TLS Record），TLS Record 协议在 TLS 握手协议之下。下面是 SSL/TLS 的分层结构：



![img](https://pic3.zhimg.com/v2-79a09985ed2ea138577f6609251fb4a6_b.jpg)



所有的 TLS 上层数据（包含 TLS 握手协议消息以及更上层的应用协议数据）都由 TLS Record 来封装和传输。下面是 TLS Record 协议的消息封装流程：



![img](https://pic2.zhimg.com/v2-372d82090398c309db82ac99140df9c1_b.jpg)



一个消息会在 TLS Record 协议层被分段，然后对每个分段分别压缩（已废弃）和加密，最后加上 TLS Record 协议头再通过网络层发送出去，在 Wireshark 中可以看到：



![img](https://pic2.zhimg.com/v2-d9e9ff37ed909c1ba9af4839c53eff7d_b.jpg)



可以看出 TLS/SSL 数据加解密是基于分段的，接收方收到一个完整的分段就可以解密，如果分段过大，那接收方必须要等到接收完整个分段的数据才能解密，当网络不好出现丢包、拥塞、重传等等情况下会严重影响时延，如果分段过小，头部负载比重就会较大，影响吞吐率。所以，怎么分段就比较关键，也就是 TLS Record Size 大小多少合适。这就涉及到两个重要的指标：**时延和吞吐率**。

1. 如果 TLS Record Size 设置较大
   假如超过了 MTU，一个分段会被 TCP 层再次分段，拆分成多个包发送，那么时延也相应地增大，但由于记录协议头占比很小，吞吐率也增大。
2. 如果 TLS Record Size 设置较小
   一个分段在 TCP 层没有被再次分段，由一个包发送出去，时延会比较小，但吞吐率也会下降。

所以，对时延敏感的域名，TLS Record Size 建议设置为略小于 MTU 的大小（粗算：MTU - TLS Record Header Length - TCP Header Length - IP Header Length）。对吞吐率敏感的域名，TLS Record Size 建议设置为 16k（上限）。如果同时兼顾时延和吞吐率的话，建议设置为 8k （经验值）。

但是，TCP 有拥塞控制机制，在 TCP 连接刚开始的慢启动阶段，拥塞窗口会比较小，这时较大的 TLS Record Size 会导致 TLS Record 片段被 TCP 分成多个包来发送，从而导致时延较大，可能就会影响网页的首屏时间。在整个 TCP 数据传输过程中，可能启动拥塞避免算法，也可能遇到数据丢失和重传的情况，然后又重新进入慢启动阶段。

因此，更好的做法是动态调整 TLS Record Size 的大小，而不是使用固定的值。在 TCP 连接初期使用较小的值，等传输速度上来之后再使用更大的值。