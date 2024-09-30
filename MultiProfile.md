# Multi Profile에서 특정 환경에서만 Config 또는 Bean 등록

Spring Boot에서 Multi Profile을 활용하면 다양한 환경에서 애플리케이션을 유연하게 구성할 수 있습니다. 
이를 이해하고 활용하기 위해 알아야 할 주요 개념들은 다음과 같습니다:

## 1. Spring Profile 개념
Spring Profile은 애플리케이션이 실행되는 환경에 따라 다른 설정 파일 또는 Bean을 사용하도록 합니다. 
예를 들어, 개발 환경(`dev`), 테스트 환경(`test`), 운영 환경(`prod`)에 맞춘 구성을 설정할 수 있습니다.

### @Profile 어노테이션
특정 Profile에서만 Bean을 등록하려면 `@Profile` 어노테이션을 사용합니다. 
`@Configuration` 클래스나 특정 Bean에 적용하여, 조건에 따라 로딩되도록 설정합니다.

```java
@Configuration
@Profile("dev")
public class DevConfig {
    @Bean
    public MyService myService() {
        return new DevMyService();
    }
}

@Configuration
@Profile("prod")
public class ProdConfig {
    @Bean
    public MyService myService() {
        return new ProdMyService();
    }
}
```
위 예시에서는 애플리케이션이 "dev" 프로파일로 실행되면 `DevConfig`가 로드되고, "prod" 프로파일로 실행되면 `ProdConfig`가 로드됩니다.

## 2. 프로파일 적용 방법
특정 프로파일을 적용하는 방법에는 여러 가지가 있습니다.

### application.properties 또는 application.yml에서 지정
`application.properties` 또는 `application.yml`에서 `spring.profiles.active`를 통해 활성화할 프로파일을 지정할 수 있습니다.

```properties
spring.profiles.active=dev
```

### JVM 옵션을 사용
애플리케이션을 실행할 때 JVM 옵션으로 프로파일을 지정할 수도 있습니다.

```bash
java -jar -Dspring.profiles.active=prod myapp.jar
```

## 3. 프로파일별 설정 파일
여러 환경에서 동일한 설정 파일을 사용하는 대신, 환경별로 별도의 설정 파일을 사용할 수 있습니다. 예를 들어, `application-dev.yml`과 `application-prod.yml` 파일을 만들어 각각의 환경에서 사용할 수 있습니다.

```yaml
# application-dev.yml
 spring:
  datasource:
    url: jdbc:mysql://localhost:3306/dev_db

# application-prod.yml
spring:
  datasource:
    url: jdbc:mysql://prod.db.server:3306/prod_db
```
특정 환경에서 유효한 설정이 자동으로 로드됩니다.

## 4. 다중 프로파일 사용
여러 프로파일을 동시에 활성화할 수 있습니다. 예를 들어, "dev"와 "mysql" 프로파일을 함께 활성화하려면 다음과 같이 설정합니다.

```properties
spring.profiles.active=dev,mysql
```
이 경우, 두 프로파일에 해당하는 Bean 또는 설정 파일이 모두 적용됩니다.

## 5. Profile을 활용한 Conditional Bean 등록
`@Profile`을 사용하면 특정 프로파일에서만 Bean을 로드하거나 활성화할 수 있습니다. 이를 통해 환경에 따라 다른 구현체나 설정을 사용할 수 있습니다.

### 예시: 개발 환경에서는 로컬 캐시, 운영 환경에서는 Redis 캐시 사용
```java
    @Bean
    @Profile("dev")
    public CacheManager localCacheManager() {
        return new ConcurrentMapCacheManager();
    }
    
    @Bean
    @Profile("prod")
    public CacheManager redisCacheManager() {
        return new RedisCacheManager(redisConnectionFactory());
    }
```

## 6. @Conditional 어노테이션과 함께 사용
`@Profile` 외에도 `@Conditional` 어노테이션을 사용하면 더욱 복잡한 조건에 따라 Bean을 로드할 수 있습니다. 예를 들어, 특정 파일이 존재할 때만 Bean을 등록하거나, 환경 변수를 기준으로 Bean을 활성화할 수 있습니다.

## 7. Profiles 대체 방법
`@Profile`을 사용하지 않고, 환경 변수를 직접 조건문에서 사용하는 방식으로 특정 설정이나 Bean을 관리할 수도 있습니다. 그러나 Spring에서 제공하는 `@Profile`은 코드 관리가 더 용이하고 직관적입니다.
