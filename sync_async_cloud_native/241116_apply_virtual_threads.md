# 스프링 부트 환경에서 가상 스레드 적용 (feat. 클라우드 네이티브 애플리케이션)

안녕하세요. yeTi입니다.
이번에는 스프링 부트(Sprint Boot) 기반 개발 환경에서 기존의 스레드 풀(Thread Pool) 방식을 사용하던 애플리케이션을 자바 21에서 도입된 가상 스레드(Virtual Threads) 방식으로 전환하는 과정을 알아보겠습니다. 

## 서론

스프링 부트는 임베디드 서버를 제공합니다.  

그 중 많이 사용하는 톰캣 서버는 스레드 풀을 활용하여 웹 요청을 처리하는 구조를 가집니다. 그러나 자바 21에서 도입된 가상 스레드는 더 높은 동시성으로 하드웨어 자원의 효율성을 제공하며, 특히 I/O 바운드 애플리케이션에서 큰 성능 향상을 기대할 수 있습니다. 이번 포스팅에서는 스레드 풀 방식에서 가상 스레드 방식으로 전환하는 과정을 살펴보고, 전환시 유의해야 할 점들을 공유해 보겠습니다.

스레드 풀 방식 및 가상 스레드 방식에 대한 설명은 이미 [앞선 포스팅](https://yeti.tistory.com/389)에서 다루었으니 참고해주세요.

## 스프링 부트에서 가상 스레드 적용 준비

스프링 부트 애플리케이션을 가상 스레드로 전환하기 전에 몇 가지 사전 준비가 필요합니다.

### 필요 환경

- **자바 21 이상**: 가상 스레드는 자바 21에서 정식 지원됩니다.
- **스프링 부트 3.2 이상**: 최신 버전에서는 가상 스레드 지원이 포함되어 있습니다.

### 종속성 업데이트

`pom.xml` 또는 `build.gradle` 파일에서 자바 버전과 스프링 부트 버전을 최신으로 업데이트합니다.

```xml
<properties>
    <java.version>21</java.version>
    <spring-boot.version>3.2.0</spring-boot.version>
</properties>
```

## 스레드 풀 방식에서 가상 스레드 방식으로 전환하기

이제 실제로 스레드 풀 방식에서 가상 스레드 방식으로 전환하는 과정을 살펴보겠습니다.

### 스레드 풀 설정 제거

기존의 스레드 풀 설정을 제거하거나 비활성화합니다. `application.yml` 또는 `application.properties` 파일에서 스레드 풀 관련 설정을 삭제합니다.

```yaml
# 기존 스레드 풀 설정 예시
server:
  tomcat:
    threads:
      max: 200
      min-spare: 10
```

### 가상 스레드 활성화

`application.yml` 파일에 가상 스레드를 활성화하는 설정을 추가합니다.

```yaml
spring:
  threads:
    virtual:
      enabled: true
```

### 톰캣 설정 변경

스프링 부트 3.2 이상에서는 톰캣이 자동으로 가상 스레드를 활용하도록 설정됩니다. 추가적인 설정이 필요하지 않을 수 있지만, 커스터마이즈가 필요하다면 설정을 조정할 수 있습니다.

```java
@Bean
public ConfigurableServletWebServerFactory webServerFactory() {
    TomcatServletWebServerFactory factory = new TomcatServletWebServerFactory();
    factory.setTomcatContextCustomizers(context -> {
        // 추가적인 톰캣 설정이 필요한 경우 여기서 설정
    });
    return factory;
}
```

### 비동기 작업 설정

스프링의 비동기 작업(`@Async` 등)도 가상 스레드를 활용하도록 설정합니다.

```java
@Configuration
@EnableAsync
public class AsyncConfig {

    @Bean
    public Executor taskExecutor() {
        return Executors.newVirtualThreadPerTaskExecutor();
    }
}
```

이처럼 스프링 부트 기반 애플리케이션에서 가상 스레드를 적용하는 것은 매우 간단합니다. 다만, 이렇게 가상 스레드를 적용한 것의 특성을 알아둘 필요가 있습니다.

## 가상 스레드 적용 특성

### 웹 요청 처리 특성

`application.yml` 파일에 가상 스레드를 활성화하는 설정을 추가한 것은 웹 요청 처리 구간에서 가상 스레드를 사용하겠다는 의미입니다.

```yaml
spring:
  threads:
    virtual:
      enabled: true
```

조금 더 자세하게 설명하면, Request per Thread 방식으로 웹 요청을 처리하는 방식에서 각각의 웹 요청은 캐리어 스레드를 직접적으로 사용하는 것이 아닌 JVM 에서 제공하는 가상 스레드를 할당받아 처리합니다.

이러한 방식은 가상 스레드가 지원하는 논블로킹 I/O 의 장점을 살려 DB 나 외부 API 와 연계하는 구간에서 가상 스레드가 컨텍스트 스위칭하여 웹 요청의 처리량을 높일 수 있습니다.

### 비동기 작업 특성

`@Async` 어노테이션이나 `@Scheduled` 어노테이션 등을 사용하는 비동기 작업 또한 가상 스레드를 사용할 수 있게 됩니다.

이 의미는 앞서 언급한 것과 마찬가지로 가상 스레드가 지원하는 논블로킹 I/O 의 장점을 살려 비동기 작업을 처리하는 구간에서 가상 스레드의 컨텍스트 스위칭으로 비동기 작업의 처리량을 높일 수 있습니다.

하지만 `@Async` 어노테이션이나 `@Scheduled` 어노테이션을 사용하는 경우에는 CPU 바운드 작업의 비중이 높은 경우도 있습니다. 이러한 경우에는 가상 스레드를 사용하는 것이 오히려 성능을 저하시킬 수 있습니다. 이는 가상 스레드가 지원하는 논블로킹 I/O 의 장점을 살리지 못하기 때문입니다.

### 데이터베이스 접근 특성

앞서 스프링 부트의 설정을 적용한다고 하더라도 기존 DB 연결을 위해 사용하는 DB 커넥션 풀은 스레드 풀 방식을 사용합니다.

이는 DB 연결 구간에는 블로킹 I/O 가 발생한다는 의미인데요. 그렇더라도 웹 요청 처리 구간에서는 가상 스레드를 사용하기 때문에 JVM 수준에서 DB 연결 구간의 블로킹 I/O 를 비동기 작업으로 처리해주기 때문에 웹 요청의 처리 측면에서는 처리량을 높일 수 있습니다.

## 전환 시 고려 사항

가상 스레드로 전환할 때는 몇 가지 유의해야 할 사항이 있습니다.

### 동기화 블록 및 ThreadLocal

가상 스레드는 동기화 블록이나 `ThreadLocal` 사용 시 성능 저하가 발생할 수 있습니다. 가능한 비동기 설정을 사용하고, `ThreadLocal` 사용을 최소화합니다.

### 라이브러리 호환성

사용 중인 라이브러리가 가상 스레드와 호환되는지 확인해야 합니다. 일부 라이브러리는 여전히 전통적인 스레드 모델에 의존할 수 있습니다.

## 결론

스프링 부트 기반 애플리케이션을 스레드 풀 방식에서 가상 스레드 방식으로 전환하는 과정은 간단합니다.

앞서 포스팅한 내용과 더불어 보면, 가상 스레드는 I/O 바운드 작업에 JVM 수준으로 논블로킹 I/O 를 지원하기 때문에 스레드 풀 방식보다 높은 동시성과 처리량을 달성할 수 있습니다. 그러나 전환 과정에서는 동기화 블록의 사용, 라이브러리 호환성을 꼼꼼한 점검하고 성능 테스트를 통해 전환의 효과를 검증하고, 필요한 최적화를 지속적으로 수행하는 것이 중요합니다.

프레임워크 수준에서 가상 스레드를 지원하기 때문에 기존 코드와의 호환성을 유지하면서 가상 스레드를 빠르게 적용할 수 있습니다. 이를 통해 빠른 실험으로 서비스의 효율을 높일 수 있는 시도를 해보는 것은 좋은 방법이라고 생각합니다.

## 참고 자료

- [Project Loom 공식 웹사이트](https://openjdk.java.net/projects/loom/)
- [Java Virtual Threads - Baeldung](https://www.baeldung.com/java-virtual-threads)
- [Spring Boot와 Virtual Threads 통합 가이드](https://spring.io/blog/2022/10/11/embracing-virtual-threads/)
- [HikariCP Issues 관련 토론](https://github.com/brettwooldridge/HikariCP/issues/2151)
- [R2DBC 공식 문서](https://docs.spring.io/spring-data/r2dbc/docs/current-SNAPSHOT/reference/html/)
- [비동기 데이터베이스 접근 - R2DBC vs JDBC](https://mariadb.com/resources/blog/benchmark-jdbc-connectors-and-java-21-virtual-threads/)

## 지난 기록

- [스레드 풀과 버추얼 스레드의 비교 (feat. 클라우드 네이티브 애플리케이션)](https://yeti.tistory.com/389)
- [클라우드 네이티브 애플리케이션에서 버추얼 스레드의 역할과 이점](https://yeti.tistory.com/388)
- [클라우드 네이티브 애플리케이션: 경량 애플리케이션의 중요성과 Context Switching 문제 해결 방안](https://yeti.tistory.com/385)
- [클라우드 네이티브 애플리케이션: 탄생 배경부터 현황까지](https://yeti.tistory.com/382)
- [동시성과 병렬성의 이해: Context Switching](https://yeti.tistory.com/381)
- [동시성과 병렬성의 이해: 동기, 비동기, 블로킹, 논블로킹의 개념](https://yeti.tistory.com/380)
- [동시성과 병렬성의 이해: 멀티스레딩 vs 멀티프로세싱](https://yeti.tistory.com/379)