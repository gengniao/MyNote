<h1 align="center">Ribbon 客户端负载均衡</h1>

## 1.Ribbon 的基本使用

### Maven 依赖

> Spring-cloud-starter-eureka 包含 Spring-cloud-starter-ribbon

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-eureka</artifactId>
</dependency>
```

### @LoadBalanced 注解
<pre><code>import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
import org.springframework.context.annotation.Bean;
import org.springframework.web.client.RestTemplate;

@SpringBootApplication
@EnableEurekaClient
public class ConsumerApplication {

	public static void main(String[] args) {
		SpringApplication.run(ConsumerApplication.class, args);
	}


	<font color="#008b8b">/**
	 * 注意这里的方法名称最好和 @Autowired 中的变量名一致
	 * @LoadBalanced 使 RestTemplate 具备负载均衡的能力
	 */</font>
	@Bean
	<font color="#ff69b4">@LoadBalanced</font>
	public RestTemplate restTemplate() {
		return new RestTemplate();
	}
}
</code></pre>

### 负载均衡策略(默认为轮询)
