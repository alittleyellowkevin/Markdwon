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

## Spring Data Redis

### 使用方式

1. 导入坐标

```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency> 
```

2. 配置redis数据源

```yaml
spring:
  redis:
    host: localhost
    port: 6379
```

3. 编写配置类，创建redisTemplate对象

```java
@Configuration
@Slf4j
public class RedisConfiguration {
    @Bean
    public RedisTemplate redisTemplte(RedisConnectionFactory redisConnectionFactory){
        log.info("开始创建redis模板类...");
        RedisTemplate redisTemplate = new RedisTemplate();
        //设置redis的工厂连接对象
        redisTemplate.setConnectionFactory(redisConnectionFactory);
        //设置来redis key的序列化器
        redisTemplate.setValueSerializer(new StringRedisSerializer());
        return redisTemplate;
    }
}
```

## redis实现延迟任务

- 实现思路

![](https://gulinall-hkw.oss-cn-shenzhen.aliyuncs.com/0f3ea1f9-c0f5-406c-ac39-a924e8d391e0.png)

1. 自媒体端将任务添加

```java
@Override
    @Async
    public void addNewsToTask(Integer id, Date publishTime) {

        log.info("添加任务到延迟服务中----begin");

        Task task = new Task();
        task.setExecuteTime(publishTime.getTime());
        task.setTaskType(TaskTypeEnum.NEWS_SCAN_TIME.getTaskType());
        task.setPriority(TaskTypeEnum.NEWS_SCAN_TIME.getPriority());
        WmNews wmNews = new WmNews();
        wmNews.setId(id);
        task.setParameters(ProtostuffUtil.serialize(wmNews));
        scheduleClient.addTask(task);
        log.info("添加任务到延迟服务中----end");

    }
```

2. 定时任务类中实现任务的添加

```java
 @Override
    public long addTask(Task task) {
        //1.添加任务到数据库中

        boolean success = addTaskToDb(task);

        if (success) {
            //2.添加任务到redis
            addTaskToCache(task);
        }


        return task.getTaskId();
    }

    @Autowired
    private CacheService cacheService;

    /**
     * 把任务添加到redis中
     *
     * @param task
     */
    private void addTaskToCache(Task task) {

        String key = task.getTaskType() + "_" + task.getPriority();

        //获取5分钟之后的时间  毫秒值
        Calendar calendar = Calendar.getInstance();
        calendar.add(Calendar.MINUTE, 5);
        long nextScheduleTime = calendar.getTimeInMillis();

        //2.1 如果任务的执行时间小于等于当前时间，存入list
        if (task.getExecuteTime() <= System.currentTimeMillis()) {
            cacheService.lLeftPush(ScheduleConstants.TOPIC + key, JSON.toJSONString(task));
        } else if (task.getExecuteTime() <= nextScheduleTime) {
            //2.2 如果任务的执行时间大于当前时间 && 小于等于预设时间（未来5分钟） 存入zset中
            cacheService.zAdd(ScheduleConstants.FUTURE + key, JSON.toJSONString(task), task.getExecuteTime());
        }


    }
```

3. 实现定时同步，即清除redis中未完成任务，从mysql中取得任务

```java
@Scheduled(cron = "0 */5 * * * ?")
    @PostConstruct
    public void reloadData() {
        clearCache();
        log.info("数据库数据同步到缓存");
        Calendar calendar = Calendar.getInstance();
        calendar.add(Calendar.MINUTE, 5);

        //查看小于未来5分钟的所有任务
        List<Taskinfo> allTasks = taskinfoMapper.selectList(Wrappers.<Taskinfo>lambdaQuery().lt(Taskinfo::getExecuteTime,calendar.getTime()));
        if(allTasks != null && allTasks.size() > 0){
            for (Taskinfo taskinfo : allTasks) {
                Task task = new Task();
                BeanUtils.copyProperties(taskinfo,task);
                task.setExecuteTime(taskinfo.getExecuteTime().getTime());
                addTaskToCache(task);
            }
        }
    }
```

4. 将zset中的任务定时刷新到list中

```java
@Scheduled(cron = "0 */1 * * * ?")
    public void refresh(){

        String token = cacheService.tryLock("FUTURE_TASK_SYNC", 1000 * 30);
        if(StringUtils.isNotBlank(token)){
            log.info("未来数据定时刷新---定时任务");

            //获取所有未来数据的集合key
            Set<String> futureKeys = cacheService.scan(ScheduleConstants.FUTURE + "*");
            for (String futureKey : futureKeys) {//future_100_50

                //获取当前数据的key  topic
                String topicKey = ScheduleConstants.TOPIC+futureKey.split(ScheduleConstants.FUTURE)[1];

                //按照key和分值查询符合条件的数据
                Set<String> tasks = cacheService.zRangeByScore(futureKey, 0, System.currentTimeMillis());

                //同步数据
                if(!tasks.isEmpty()){
                    cacheService.refreshWithPipeline(futureKey,topicKey,tasks);
                    log.info("成功的将"+futureKey+"刷新到了"+topicKey);
                }
            }
        }
    }
```

5. 自媒体端消费队列

```java
@Autowired
    private WmNewsAutoScanServiceImpl wmNewsAutoScanService;

    /**
     * 消费延迟队列数据
     */
    @Scheduled(fixedRate = 1000)
    @Override
    @SneakyThrows
    public void scanNewsByTask() {

        log.info("文章审核---消费任务执行---begin---");

        ResponseResult responseResult = scheduleClient.poll(TaskTypeEnum.NEWS_SCAN_TIME.getTaskType(), TaskTypeEnum.NEWS_SCAN_TIME.getPriority());
        if(responseResult.getCode().equals(200) && responseResult.getData() != null){
            String json_str = JSON.toJSONString(responseResult.getData());
            Task task = JSON.parseObject(json_str, Task.class);
            byte[] parameters = task.getParameters();
            WmNews wmNews = ProtostuffUtil.deserialize(parameters, WmNews.class);
            System.out.println(wmNews.getId()+"-----------");
            wmNewsAutoScanService.autoScanWmNews(wmNews.getId());
        }
        log.info("文章审核---消费任务执行---end---");
    }
```

