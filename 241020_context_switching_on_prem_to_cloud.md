# 동시성과 병렬성의 이해: Context Switching

안녕하세요. yeTi입니다.

현대 소프트웨어 개발은 빠르게 변화하는 환경 속에서 지속적으로 적응하고 발전하고 있습니다. 특히, **온프레미스(On-Premise)** 환경에서 **클라우드(Cloud)** 환경으로의 전환은 서비스의 구동 방식과 성능 최적화에 큰 영향을 미쳤습니다. 이러한 변화는 **동시성(Concurrency)**과 **병렬성(Parallelism)**에 대한 새로운 요구사항을 만들어냈으며, 이에 대한 효과적인 대응 방안으로 **버추얼 쓰레드(Virtual Threads)**, **코루틴(Coroutines)**, **WebFlux**와 같은 최신 동시성 기법들이 등장하게 되었습니다. 이번 포스팅에서는 **Context Switching**의 개념과 온프레미스에서 클라우드로의 전환이 동시성 기법에 어떤 영향을 미쳤는지, 그리고 이러한 변화가 새로운 기술들의 등장을 어떻게 촉진했는지에 대해 살펴보겠습니다.

## 온프레미스 환경에서의 Context Switching

### Context Switching이란?

**Context Switching**은 운영체제가 여러 프로세스나 스레드 간에 CPU의 제어권을 전환하는 과정을 의미합니다. 이 과정에서 현재 실행 중인 작업의 상태를 저장하고, 다음 실행할 작업의 상태를 복원합니다. Context Switching은 동시성을 구현하는 핵심 메커니즘 중 하나이지만, 빈번한 전환은 시스템의 성능 저하를 초래할 수 있습니다.

### 온프레미스 환경에서의 한계

온프레미스 환경에서는 물리적인 서버 자원이 제한적이기 때문에, 다수의 스레드를 관리하는 데 따른 Context Switching의 오버헤드가 성능 저하의 주요 원인이 되곤 했습니다. 특히, **멀티스레딩(Multithreading)**을 통해 동시성을 구현할 때, 스레드 간의 Context Switching이 빈번하게 발생하면 CPU 자원의 비효율적인 사용과 함께 응답 속도가 느려지는 문제가 발생합니다.

### 멀티스레딩의 한계: 스레드 수와 CPU 코어의 관계

멀티스레딩의 효율성은 애플리케이션에 할당된 스레드 수와 실제 하드웨어의 CPU 코어 수에 직접적으로 영향을 받습니다. 온프레미스 환경에서는 물리적인 서버가 많은 CPU 코어를 보유하고 있기 때문에, 다수의 스레드를 생성하여 각 코어에서 병렬로 처리할 수 있습니다. 이는 멀티스레딩이 효과적으로 동시성을 구현할 수 있는 기반이 됩니다.

그러나 클라우드 환경에서는 다음과 같은 이유로 멀티스레딩의 한계가 더욱 두드러집니다:

1. **제한된 CPU 코어**: 클라우드 환경의 컨테이너나 가상 머신은 온프레미스 서버에 비해 할당된 CPU 코어 수가 제한되는 경우가 많습니다. 예를 들어, 하나의 컨테이너에 2개의 코어만 할당받았다면, 다수의 스레드를 생성해도 실제로 동시에 실행될 수 있는 스레드 수는 제한적입니다.
2. **자원 할당의 유연성 부족**: 클라우드 환경에서는 애플리케이션이 실행되는 동안 자원이 동적으로 조정될 수 있습니다. 이로 인해 고정된 수의 스레드를 사용하는 멀티스레딩 모델은 자원 변화에 유연하게 대응하기 어렵습니다.
3. **메모리 사용량 증가**: 각 스레드는 독립적인 스택 메모리를 필요로 합니다. 제한된 메모리 자원을 가진 클라우드 환경에서는 다수의 스레드를 생성할 경우 메모리 사용량이 급격히 증가하여 비용 효율성이 저하될 수 있습니다.

```python:examples/on_prem_context_switching.py
import threading
import time

def task(name):
    print(f"Task {name} 시작")
    time.sleep(1)
    print(f"Task {name} 완료")

threads = []
for i in range(5):
    thread = threading.Thread(target=task, args=(f"스레드-{i+1}",))
    threads.append(thread)
    thread.start()

for thread in threads:
    thread.join()
```

*위 예제는 온프레미스 환경에서 멀티스레딩을 사용하여 여러 작업을 동시에 실행하는 간단한 파이썬 코드입니다. 스레드 간의 Context Switching으로 인해 오버헤드가 발생할 수 있습니다.*

## 클라우드 환경으로의 전환과 동시성 기법의 필요성

클라우드 환경은 **유연성(Flexibility)**과 **확장성(Scalability)**을 제공하지만, 동시에 높은 동시성을 요구하는 애플리케이션의 개발을 촉진합니다. 클라우드는 다양한 지역에 분산된 자원을 효율적으로 활용할 수 있도록 지원하며, 이는 동시성과 병렬성을 더욱 중요하게 만듭니다. 클라우드 환경에서는 다음과 같은 이유로 기존의 멀티스레딩 방식의 한계를 극복할 필요가 있습니다:

1. **자원의 효율적 활용**: 클라우드에서는 자원을 동적으로 할당하고 관리할 수 있으므로, 자원의 효율적 활용을 위해 경량화된 동시성 기법이 요구됩니다.
2. **확장성**: 클라우드 애플리케이션은 수평적으로 확장되어야 하므로, 많은 수의 작업을 효율적으로 관리할 수 있는 동시성 기법이 필요합니다.
3. **응답성**: 사용자 경험을 향상시키기 위해 애플리케이션의 응답성을 높이는 것이 중요합니다.

## 멀티스레딩의 한계와 새로운 동시성 기법의 등장

### 멀티스레딩의 한계

멀티스레딩은 동시성을 구현하는 강력한 도구이지만, 클라우드 환경에서 다음과 같은 한계점을 지닙니다:

- **높은 메모리 사용량**: 각 스레드는 독립된 스택 메모리를 필요로 하므로, 많은 수의 스레드를 생성할 경우 메모리 사용량이 급격히 증가합니다.
- **컨텍스트 스위칭 오버헤드**: 스레드 간의 빈번한 전환은 시스템의 성능을 저하시킵니다.
- **복잡한 동기화 문제**: 스레드 간의 자원 공유 시 교착 상태(Deadlock)나 경쟁 조건(Race Condition)과 같은 문제가 발생할 수 있습니다.

### 버추얼 쓰레드(Virtual Threads)

**버추얼 쓰레드**는 자바의 **프로젝트 로옴(Project Loom)**에서 도입된 경량 스레드로, 기존의 플랫폼 스레드에 비해 훨씬 적은 자원을 사용하면서 대규모 동시성을 지원합니다. 버추얼 쓰레드는 기존의 블로킹 I/O 작업을 효율적으로 처리할 수 있으며, 많은 수의 쓰레드를 생성하더라도 메모리 및 성능 오버헤드가 적습니다.

```java
public class VirtualThreadExample {
    public static void main(String[] args) throws InterruptedException {
        for (int i = 0; i < 1000; i++) {
            Thread.startVirtualThread(() -> {
                System.out.println("버추얼 쓰레드 실행");
            });
        }
    }
}
```

*위 예제는 자바의 버추얼 쓰레드를 사용하여 수천 개의 경량 쓰레드를 생성하는 예제입니다. 버추얼 쓰레드는 기존 스레드보다 훨씬 적은 자원을 사용하여 높은 동시성을 구현할 수 있습니다.*

### 코루틴(Coroutines)

**코루틴**은 주로 **Kotlin**과 **Python**과 같은 언어에서 제공되는 경량화된 동시성 기법으로, 비동기 작업을 효율적으로 관리할 수 있게 해줍니다. 코루틴은 스레드를 생성하지 않고도 비동기 작업을 수행할 수 있으며, 이를 통해 높은 동시성을 구현할 수 있습니다.

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    repeat(1000) { 
        launch {
            println("코루틴 실행")
        }
    }
}
```

*위 예제는 코루틴을 사용하여 수천 개의 경량 작업을 동시에 실행하는 간단한 Kotlin 코드입니다. 코루틴은 빠르고 효율적으로 동시성을 처리할 수 있습니다.*

### WebFlux

**WebFlux**는 자바의 **Spring Framework**에서 제공하는 비동기, 논블로킹 웹 프레임워크로, 높은 동시성을 요구하는 서버 애플리케이션에 적합합니다. WebFlux는 리액티브 스트림(Reactive Streams) 기반으로 설계되어, 비동기 I/O를 효율적으로 처리할 수 있습니다.

```java
@RestController
public class WebFluxExampleController {

    @GetMapping("/hello")
    public Mono<String> hello() {
        return Mono.just("Hello, WebFlux!")
                   .delayElement(Duration.ofSeconds(1));
    }
}
```

*위 예제는 WebFlux를 사용하여 비동기적으로 "Hello, WebFlux!" 메시지를 반환하는 간단한 컨트롤러입니다. WebFlux는 비동기 작업을 쉽게 처리할 수 있게 해줍니다.*

## 클라우드 환경에서의 동시성 기법 선택

클라우드 환경에서는 다음과 같은 기준에 따라 적절한 동시성 기법을 선택할 수 있습니다:

1. **애플리케이션의 특성**:
   - **I/O 바운드**: WebFlux나 코루틴과 같은 비동기, 논블로킹 기법이 적합합니다.
   - **CPU 바운드**: 버추얼 쓰레드나 멀티프로세싱이 더 효율적일 수 있습니다.

2. **확장성과 자원 관리**:
   - 클라우드의 동적 자원 할당 특성을 활용하기 위해 경량화된 동시성 기법이 유리합니다.

3. **유지보수와 코드 복잡성**:
   - 기존의 멀티스레딩 모델보다 더 간단하게 동시성을 구현할 수 있는 코루틴이나 WebFlux가 유지보수에 도움이 될 수 있습니다.

## 결론

온프레미스에서 클라우드로의 전환은 서비스의 구동 환경을 근본적으로 변화시켰습니다. 이러한 변화는 기존의 멀티스레딩 방식의 한계를 드러내었고, 이를 극복하기 위한 새로운 동시성 기법들의 등장을 촉진했습니다. **버추얼 쓰레드**, **코루틴**, **WebFlux**와 같은 기술들은 클라우드 환경에서의 높은 동시성을 효율적으로 처리하며, 성능과 자원 활용을 극대화하는 데 기여하고 있습니다. 특히, 클라우드 환경에서는 제한된 자원을 효율적으로 활용하기 위해 이러한 경량화된 동시성 기법이 더욱 중요해지고 있습니다. 앞으로도 클라우드의 발전과 함께 동시성 기법은 더욱 진화할 것이며, 개발자들은 이러한 기술들을 적절히 활용하여 고성능의 안정적인 애플리케이션을 개발할 수 있을 것입니다.

---

### 참고 자료

1. [Understanding Concurrency and Parallelism](https://powerclabman.tistory.com/118)
2. [Concurrency vs Parallelism](https://binux.tistory.com/169)
3. [멀티스레드와 멀티프로세스의 차이점](https://livenow14.tistory.com/67)
4. [Java Virtual Threads and Project Loom](https://perfectacle.github.io/2023/07/10/java-virtual-thread-vs-kotlin-coroutine/)
5. [Project Loom의 버추얼 스레드 소개](https://hvroom.tistory.com/entry/Project-Loom-자바-버추얼-쓰레드)
6. [Understanding Kotlin Coroutines](https://charming-kyu.tistory.com/67)
7. [Python Coroutines Explained](https://onlyfor-me-blog.tistory.com/539)
8. [Spring WebFlux Introduction](https://rlaehddnd0422.tistory.com/240)
9. [Reactive Programming with Spring WebFlux](https://velog.io/@dyllis/%EB%8F%99%EC%8B%9C%EC%84%B1%EC%97%90-%EB%8C%80%ED%95%9C-%ED%95%B4%EA%B2%B0%EB%B0%A9%EB%B2%95%EC%9D%84-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90)

## 지난 기록

- [동시성과 병렬성의 이해: 동기, 비동기, 블로킹, 논블로킹의 개념](https://yeti.tistory.com/380)
- [동시성과 병렬성의 이해: 멀티스레딩 vs 멀티프로세싱](https://yeti.tistory.com/379)