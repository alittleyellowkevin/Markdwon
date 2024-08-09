# Redis

## Redis简介

Redis是一个基于内存的key-value结构数据库

## Redis的数据类型

- 字符串（String）：普通字符串，是Redis中最简单的数据类型
- 哈希（hash）：也叫散列，类似于Java中的HashMap结构
- 列表（list）：按照插入顺序排序，可以有重复元素，类似于java中LinkList
- 集合（set）：无序集合，没有重复元素，类似于Java中的HashSet
- 有序集合（sorted set / zset）：集合中每个元素关联一个分数，根据分数升序排序，没有重复元素

## Redis的常用命令

### 字符串操作命令

- set key value : 设置指定key的值
- get key : 获取制定key 的值
- setex key seconds value：设置制定key的值，并设置key的过期时间为seconds秒
- setnx key value：只有在key不存在时设置key的值

### 哈希操作命令

- hest key field value：将哈希表key中的字段field的值设置为value
- hget key field：获取存储在哈希表中指定字段的值
- hdel key field：删除存储在哈希表中的指定字段
- hkeys key：获取哈希表中所有字段
- hvals key：获取哈希表中所有值

### 列表操作命令

- lpush key value1 [value2]：将一个或多个值插入列表头部
- lrange key start stop：获取列表指定范围内的元素
- rpop key：移除并获取列表最后一个元素
- llen key：获取列表长度

### 集合操作命令

- sadd key member1 [member2]：向集合添加一个或多个成员
- smembers key：返回集合中所有成员
- scard key：获取集合的成员数
- sinter key1 [key2]：返回给定所有集合的交集
- sunion key1 [key2]：返回所有给定集合的并集
- Srem key member1 [member2]：删除集合中一个或多个成员

### 有序集合操作命令

有序集合是string类型元素的集合，不允许有重复成员，每个元素都会关联一个double类型的分数

- zdd key score1 member1 [score2 member2] : 向有序数组添加一个或多个成员
- zrange key start stop [withscores]：通过索引区间返回有序集合中指定成员
- zincrby key increment member：有序集合中对指定成员的分数加上增量increment
- zrem key member [member]：移除有序集合中的一个或多个成员

### 通用命令

- keys pattern：查找所有符合给定模式的key
- exists key：检查给定key是否存在
- type key：返回key所存储的值的类型
- del key：该命令用于在key存在时删除key