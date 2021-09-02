zk的ap和cp是从不同的角度分析的。

> 从一个读写请求分析，保证了可用性（不用阻塞等待全部follwer同步完成），保证不了数据的一致性，所以是ap。

但是从zk架构分析，

> zk在leader选举期间，会暂停对外提供服务（为啥会暂停，因为zk依赖leader来保证数据一致性)，所以丢失了可用性，保证了一致性。

再细点话，这个c不是强一致性，而是最终一致性。即上面的写案例，数据最终会同步到一致，只是时间问题。

综上，zk广义上来说是cp，狭义上是ap。