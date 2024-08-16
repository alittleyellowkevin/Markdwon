# SpringCloudAlibaba

## Nacos

### 注册中心

1. 导入坐标

```xml
<dependency>
     <groupId>com.alibaba.cloud</groupId>
     <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId></dependency>
```

2. 在bootstap文件中添加配置

```yaml
spring:
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
  application:
    name: gulimall-product

```

3. 在启动类上添加开启服务注册的注解

```java
@EnableDiscoveryClient
@SpringBootApplication
public class GulimallMemberApplication {
    public static void main(String[] args) {
        SpringApplication.run(GulimallMemberApplication.class, args);
    }
}
```

### 配置中心

1. 引入依赖

```xml
<dependency>
      <groupId>com.alibaba.cloud</groupId>
      <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
```

2. 在yml文件中加入配置中心地址

```yaml
spring:
  cloud:
    nacos:
      config:
        server-addr: localhost:8848
```

3. 观察运行时项目启动地址的配置

```java
NacosPropertySourceBuilder: Loading nacos data, dataId: 'gulimall-coupon.properties', group: 'DEFAULT_GROUP'
b.c.PropertySourceBootstrapConfiguration : Located property source: CompositePropertySource {name='NACOS', propertySources=[NacosPropertySource {name='gulimall-coupon.properties'}]}
```

4. 复制该文件并在nacos中添加

![image-20240813101339011](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240813101339011.png)

## OpenFeign

1. 导入坐标

```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
```

2. 开启feign功能

```java
@EnableFeignClients(basePackages = "com.atguigu.gulimall.member.feign")
@EnableDiscoveryClient
@SpringBootApplication
public class GulimallMemberApplication {
    public static void main(String[] args) {
        SpringApplication.run(GulimallMemberApplication.class, args);
    }
}
```

3. 声明远程调用接口

```java
@FeignClient("gulimall-coupon")
public interface CouponFeignService {
    @RequestMapping("/coupon/coupon/member/list")
    public R membercoupons();
}
```

4. 将远程接口注入

```java
@Autowired
    CouponFeignService couponFeignService;
    @RequestMapping("/coupons")
    public R test(){
        MemberEntity memberEntity = new MemberEntity();
        memberEntity.setNickname("张三");
        R member = couponFeignService.membercoupons();
        return R.ok().put("member", memberEntity).put("coupons", member.get("membercoupons"));
    }
```

## Gateway

