# Redisson Map

- 允许为每个键绑定一个 Lock/ ReadWriteLock/ Semaphore/ CountDownLatch 对象
- 支持本地缓存
- 支持驱逐机制
- 支持持久化
- 支持添加监听器
- 支持使用LRU/LFU策略对Map大小进行限制

## 1. 分类

开源版本的Redisson只提供了下面几个类型的Map。

- Map：常规Map

- MapCahe：在常规Map的基础上提供了驱逐支持（Reidsson的驱逐实现，非Redis的驱逐实现）

- LocalCachedMap：在常规Map的基础上提供了本地缓存支持

很遗憾，支持驱逐机制的Map不支持本地缓存，支持本地缓存的Map又不支持驱逐机制。

## 2. 驱逐

具备驱逐功能的Map实现，实现了 `org.redisson.api.RMapCache` 接口。

当前的 Redis 实现没有映射条目驱逐功能。因此，过期的条目会被 `org.redisson.eviction.EvictionScheduler` <mark>不时清理</mark>。它一次删除 100 个过期条目。任务启动时间自动调整，取决于上次删除的过期条目数量，变化范围为 5 秒到半小时。因此，如果 clean 任务每次删除 100 个条目，它将每 5 秒执行一次（最小执行延迟）。但如果当前过期条目数量低于前一个，则执行延迟将增加 1.5 倍，反之则减少。

## 3. 本地缓存

具备本地缓存功能的Map实现，实现了`org.redisson.api.RLocalCachedMap`接口。

缓存的策略由`LocalCachedMapOptions`在Map创建时指定，并且官方的建议是：针对于每一个RedissonClient使用的每一个Key相同的具备本地缓存功能的Map实例，应该使用相同的`LocalCachedMapOptions`对象。

提供的选项如下：

```java
      LocalCachedMapOptions options = LocalCachedMapOptions.defaults()

      // 定义缓存Miss时，是否缓存到本地，默认为false
      .storeCacheMiss(false);

      // 定义缓存存储模式
      // 提供选项：
      // LOCALCACHE - 仅将数据存储在本地缓存中，并且仅在数据更新/无效时使用Redis
      // LOCALCACHE_REDIS - 在Redis和本地缓存中存储数据.
      .storeMode(StoreMode.LOCALCACHE_REDIS)

      // 定义本地缓存的提供者
      // 提供选项：
      // REDISSON - Redisson自己的实现
      // CAFFEINE - 使用Caffeine的实现
      .cacheProvider(CacheProvider.REDISSON)

      // 定义缓存驱逐策略
      // 提供选项：
      // LFU - 最不经常使用法
      // LRU - 最近最久未使用算法
      // SOFT - 软引用，GC回收
      // WEAK - 弱引用，GC回收
      // NONE - 没有取值机制
     .evictionPolicy(EvictionPolicy.NONE)

      // 缓存大小
     .cacheSize(1000)

      // 定义了Redis连接失败后加载丢失的本地缓存更新策略
      //
      // 可用的策略如下:
      // CLEAR - 如果Map实例已断开连接一段时间，则清除本地缓存
      // LOAD  - 将失效条目哈希值存储在失效日志中 10 分钟。如果 LocalCachedMap 实例在 10 分钟内断开连接，则存储的无效条目哈希的缓存键将被删除，否则整个本地缓存将被清除。
      // NONE  - 默认. 没有重新连接处理
     .reconnectionStrategy(ReconnectionStrategy.NONE)

      // 定义本地缓存同步策略
      //
      // 可用的策略如下:
      // INVALIDATE - 默认. 映射条目更改时，所有 LocalCachedMap 实例的本地缓存条目无效。向所有实例广播映射条目哈希（16 字节）。
      // UPDATE     - 在映射条目更改时更新所有 LocalCachedMap 实例的本地缓存条目。向所有实例广播完整的映射条目状态（Key 和 Value 对象）。
      // NONE       - 出现变更时不同步
     .syncStrategy(SyncStrategy.INVALIDATE)

      // 本地缓存条目存活时长（毫秒）
     .timeToLive(10000)
      // 或指定时长单位
     .timeToLive(10, TimeUnit.SECONDS)

      // 缓存条目最大空闲时长（毫秒）
     .maxIdle(10000)
      // 或指定时长单位
     .maxIdle(10, TimeUnit.SECONDS);
```

## 3. 持久化

Redisson 允许将 Map 数据与 Redis 存储一起存储在外部存储中。

用例：

1.  Redisson Map 对象作为应用程序和外部存储之间的缓存。
2. 提高 Redisson Map 数据的持久性和被逐出条目的寿命。
3. 缓存数据库、Web 服务或任何其他数据源。

并且，针对于读/写提供了三种策略：

1. Read-through(同步)

2. Write-through (同步)

3. Write-behind (异步)



**对于Read-through的使用：**

1. 实现`org.redisson.api.map.MapLoader`接口

2. MapOptions配置loader为实现的MapLoader对象



**对于Write-through/Write-behind的使用：**

1. 实现`org.redisson.api.map.MapWriter`接口

2. MapOptions配置wrtier为实现的MapWriter对象

3. MapOptions配置writeMode为WriteMode.WRITE_THROUGH/WriteMode.WRITE_BEHIND

当writeMode为WRITE_BEHIND时，还能够配置后写延迟：

`.writeBehindDelay(5000)`

还能够配置后写批处理大小：

`.writeBehindBatchSize(1000)`



## 4. 监听器

Redisson 允许为实现 `RMapCache` 接口的 Map 对象绑定侦听器并跟踪 Map 数据上的后续事件：

Entry **created** - `org.redisson.api.map.event.EntryCreatedListener`  
Entry **expired** - `org.redisson.api.map.event.EntryExpiredListener`  
Entry **removed** - `org.redisson.api.map.event.EntryRemovedListener`  
Entry **updated** - `org.redisson.api.map.event.EntryUpdatedListener`



具体操作为：使用addLisenter方法为map添加上述四种类型的监听器实现。



## 5. LRU/LFU 有界Map

实现 `RMapCache` 接口的 Map 对象可以使用最近最少使用 (LRU) 或最少经常使用 (LFU) 顺序进行限制。有界映射允许在定义的限制内存储映射条目并按定义的顺序退出条目。

```java
RMapCache<String, SomeObject> map = redisson.getMapCache("anyMap");


// 尝试限制Map大小为10，使用LRU 驱逐算法
map.trySetMaxSize(10);
// ... 或者使用LFU 驱逐算法
map.trySetMaxSize(10, EvictionMode.LFU);

// 设置或改变Map大小限制，使用LRU 驱逐算法
map.setMaxSize(10);
// ... 或者使用LFU 驱逐算法
map.setMaxSize(10, EvictionMode.LFU);
```


