# Feign

## Feign的使用

1.导入依赖

```xml
<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
            <version>2.2.7.RELEASE</version>
</dependency>
```

2. 编写Feign接口

```java
@FeignClient("leadnews-article")
public interface IArticleClient {
    @PostMapping("/api/v1/article/save")
    public ResponseResult saveArticle(@RequestBody ArticleDto dto);

}
```

3. 实现Feign接口

```java
@RestController
public class ArticleClient implements IArticleClient {
    @Autowired
    private ApArticleService apArticleService;
    @PostMapping("/api/v1/article/save")
    @Override
    public ResponseResult saveArticle(@RequestBody ArticleDto dto) {
        return apArticleService.saveArticle(dto);
    }
}
```

4. 在需要使用该接口的启动类上扫描包

```java
@SpringBootApplication
@EnableDiscoveryClient
@EnableFeignClients(basePackages ="com.heima.apis.article")
@MapperScan("com.heima.wemedia.mapper")
public class WemediaApplication {
    public static void main(String[] args) {
      SpringApplication.run(WemediaApplication.class,args);
    }
}
```

## Feign服务降级

1. 编写降级逻辑

```java
@Component
public class IArticleClientFallback implements IArticleClient {
    @Override
    public ResponseResult saveArticle(ArticleDto dto)  {
        return ResponseResult.errorResult(AppHttpCodeEnum.SERVER_ERROR,"获取数据失败");
    }
}
```

2. 将接口指向降级代码

```java
@FeignClient(value = "leadnews-article", fallback = IArticleClientFallback.class)
public interface IArticleClient {
    @PostMapping("/api/v1/article/save")
    public ResponseResult saveArticle(@RequestBody ArticleDto dto);
}
```

3. 配置包扫描降级逻辑

```java
@Configuration
@ComponentScan("com.heima.apis.article.fallback")
public class InitConfig {
}
```

3. 客户端开启降级

```yaml
feign:
  # 开启feign对hystrix熔断降级的支持
  hystrix:
    enabled: true
  # 修改调用超时时间
  client:
    config:
      default:
        connectTimeout: 2000
        readTimeout: 2000
```



