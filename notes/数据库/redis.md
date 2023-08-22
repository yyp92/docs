# Redis

## Redis应用场景

- **计数器**：因为Redis操作是原子性的，通过原子递增或递减来做高并发用户的数据计数，比如点赞数、收藏数、分享数、商品抢购时的库存量、商品文章总数、评论数量等
- **排行榜**：Redis支持集合和有序集合的数据结构，且运行在内存中，因此可以存储一些类似于排行榜的数据，比如最近、最热、点击率最高、活跃度最高、评论最多等等的文章、商品、用户等；
- **哈希表**：用户粉丝列表、用户点赞列表、用户收藏列表、用户关注列表等；
- **自动排序**：存储时间戳，随着时间的变化，按照用户关注用户的最新动态列表等自动排序；
- **会话缓存**：使用Redis进行会话缓存，将web session存放在Redis中。
- **全页缓存FPC**：可以将服务端渲染结果的缓存在Redis中；
- **记录用户操作信息**：用户是否点赞、用户是否收藏、用户是否分享等；

## Redis常用数据类型

### 字符串Strings

字符串在redis的结构如下：

```js
// 用于存储一些key、token等数据
SecretId "AKIDpZ9Wp1pyhFdqrioDF5dmDkMoQ7oVF2shUOE" 
// 可以存储云开发经常用到的openID
openId "oUL-m5FuRmuVmxvbYOGuXbuEDsn8"  
// 表示商品库存为1017，执行原子操作命令会被解释为十进制有符号（正负）整数
storage 1017   
```

### 散列哈希表Hashes

数据：

```js
{
  "title": "为什么狗会如此亲近人类?",
  "id": 9717547,
  "url": "https://daily.zhihu.com/story/9717547",
  "image": "https://pic4.zhimg.com/v2-60f220ee6c5bf035d0eaf2dd4736342b.jpg",
  "body":  "<p>让狗从凶猛的野兽变成忠实的爱宠...</p>"
}
```

写法：

```bash
hmset post-9717547 title "为什么狗会如此亲近人类?" id 9717547 url "https://daily.zhihu.com/story/9717547" image "https://pic4.zhimg.com/v2-60f220ee6c5bf035d0eaf2dd4736342b.jpg" body "<p>让狗从凶猛的野兽变成忠实的爱宠...</p>"
```

而当我们要获取哈希表的值以及要对哈希表里的数据进行增删改查时，相应的操作命令如下（只是列举了部分，更多内容请查看技术文档）：

```js
// HGETALL以列表形式返回哈希表的字段及字段值
hgetall post-9717547

// HMGET命令返回哈希表中一个或多个给定字段的值，比如获取2个key title和id的值；HGET是只返回一个
hmget post-9717547 title id
hget post-9717547 body

// HMSET同时将多个键值对设置到哈希表中，比如我们同时设置两个键值对，HSET是只设置一个；如果key相同就会覆盖
hmset post-9717547 author "111"  city "深圳"
hset post-9717547 position "杂役"
```

### 列表Lists

Redis的列表类型可以用来存储多个**有序的字符串**，列表里的值是**可以重复**的，有点类似于JavaScript的数组。

```js
// rpush在列表的尾部（右边）添加一个或多个值，类似于数组方法里的push；lpush在列表的头部（左边）添加一个或多个值,类似于数组方法里的unshift
rpush code "Python" "JavaScript" "Java" "C++" "Golang" "Dart" "C" "C#"

// rpop移除并返回列表最后一个元素，类似于数组方法里的pop；lpop移除并返回列表第一个元素，类似于数组方法里的shift
rpop code

// llen返回列表的长度，有点类似于数组的属性length
llen code

// lindex通过索引获取列表中的元素，有点类似于数组的array[n]获取数组第n+1位的元素
lindex code 3

// lrange返回列表中指定区间内的元素，有点类似于数组方法里的slice
lrange code 2 5

// linsert key before|after pivot value,在列表的元素前或者后插入元素。当指定元素不存在于列表中时，不执行任何操作，如下方式是把SQL插入到Dart前，数组的slice方法可以在指定位置插入元素
linsert code before "Dart" "SQL"

// lset通过索引来设置元素的值，有点类似于数组的array[n]=""
lset code 4 "Go"
```

### 集合Sets

Redis的集合是字符串类型的无序集合，集合里的元素是**无序且唯一**的，不能出现重复的数据。

```js
// sadd 将一个或多个成员元素加入到集合中，已经存在于集合的成员元素将被忽略
sadd cloudbase "云函数" "云数据库" "云存储" "HTTP访问服务" "云应用" "云调用"

// smembers返回集合中的所有成员
smembers cloudbase

// scard返回集合中元素的数量
scard cloudbase

// srandmember返回集合中一个或多个随机数,spop移除集合中的指定的一个或多个随机元素，移除后会返回移除的元素
srandmember cloudbase 2
spop cloudbase

// sismember判断元素是否在集合中，在则返回1，不在返回0
sismember cloudbase "云调用"
```

Redis处理跨集合的命令如求并集`sunion`，存储并集`sunionstore`，交集`sinter`、存储交集`sinterstore`,差集`sdiff`、存储差集`sdiffstore`，跨集合移动元素`smove`，等等这里就不一一举例了。

### 有序集合Sorted sets

Redis的有序集合和集合一样也字符串类型元素且元素不重复的集合，不同的是，有序集合多了一个排序属性score（分数），也就是每个存储元素由两个值构成，一个是元素值，一个是排序值。

```js
// zadd命令用于将一个或多个元素及分数值加入到有序集中。如果元素已经存在，会更新这个元素的分数值，并通过重新插入这个元素，来保证该元素在正确的位置上。
zadd read:rank 9932 post-323 3211 post-123 1234 post-77 987 post-33 532 post-21

// zrange把元素按分数递增来排序，0为第一位，-1为最后一位，0,-1会把所有元素都排序;而1,3则是取排序的第2、4位;zrevrange则是递减
zrange read:rank 0 -1 withscores
zrange read:rank 1 3 withscores
zrevrange read:rank 1 3 withscores


// zcount显示分数score在 min 和 max 之间的元素的数量
zcount read:rank 1000 3000

// zrank返回有序集合指定元素的排名（排名以0为底），按分数值递增(从小到大)顺序排列；zrevrank是从大到小
zrank read:rank post-323
zrevrank read:rank post-987
```
