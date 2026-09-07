[图解redis](https://xiaolincoding.com/redis/)
[redis常见面试题](https://xiaolincoding.com/redis/base/redis_interview.html)
## 认识redis

### 什么是redis

- 基于内存的数据库，因此读写速度非常快
	
- 提供**多种数据类型**支持不同的业务场景

### redis和Memcached有什么区别

- 共同点：
		  
	- 都是基于内存的数据库
	- 都有过期策略
	- 两者性能都非常高
	  
- 不同点：
		
	- Redis支持的数据类型更加丰富
	- 支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载使用，而Memcached没有持久功能，数据全在内存中
	- Redis原生支持集群模式，Memcached没有原生的集群模式，需要依赖客户端来实现集群中分片
	- Redis还支持订阅模型，Lua脚本，事务等功能

### 为什么用redis作为MySQL的缓存

- 高性能
		
	- 主要是数据存在内存中，读取缓存即可
	
- 高并发
		
	- QPS

## Redis数据结构

### Redis数据类型以及使用场景分别是什么

- String：
- List
- Hash
- Set
- Zset
- BitMap(2.2版本更新)
- HyperLogLog(2.8更新)
- GEO(3.2更新)
- Stream(5.0版本更新)

### 五种常见的redis数据类型是怎么实现的

![](https://telegraph-image-43w.pages.dev/file/AgACAgUAAyEGAATTNkFKAAOtable6Edn0p72WMnFItZ0j1D7EssAAlwTaxvZnMhVb2yk32QyRGYBAAMCAAN3AAM6BA.png)
- String：SDS（简单动态字符串）
- List：
- Hash
- Set
- Zset
- BitMap(2.2版本更新)
- HyperLogLog(2.8更新)
- GEO(3.2更新)
- Stream(5.0版本更新)

## Redis线程模型

### Redis是单线程吗

- Redis单线程指的是：
	
- **并不是单线程**
		
	- 

### Redis单线程模式是怎样的

### Redis采用单线程为什么还这么快

### Redis6.0之前为什么使用单线程

### Redis6.0之后为什么引入了多线程

## Redis持久化

### Redis如何实现数据不丢失

### AOF日志是如何实现的

### RDB快照是如何实现的呢

### 为什么会有混合持久化

## Redis集群

### Redis如何实现服务高可用

### 集群脑裂导致数据丢失怎么办

## Redis过期删除与内存淘汰

### Redis使用的过期删除策略是什么

### Redis做持久化时，对过期键会如何处理

### Redis主从模式中，对过期键会如何处理

### Redis内存满了，会发生什么

### Redis内存淘汰策略有哪些

### LRU算法和LFU算法有什么区别

## Redis缓存设计

### 如何避免缓存雪崩、击穿、穿透

### 如何设计一个缓存策略，可以动态缓存热点数据

### 常见的缓存更新策略有哪些

### 如何保证缓存和数据库数据的一致性

## Redis实战

### Redis如何实现延迟队列

### Redis的大Key如何处理

### Redis管道有什么用

### Redis事务支持回滚吗

### 如何用Redis实现分布式锁


