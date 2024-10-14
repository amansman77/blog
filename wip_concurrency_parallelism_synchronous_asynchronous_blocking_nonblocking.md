# 동시성과 병렬성의 이해: 동기, 비동기, 블로킹, 논블로킹의 개념

프로그램의 효율성과 응답성을 극대화하기 위해 **동시성(Concurrency)**과 **병렬성(Parallelism)**은 필수적인 개념입니다. 이전 포스팅에서는 동시성과 병렬성의 기본 개념과 이를 구현하는 대표적인 방법인 **멀티스레딩(Multithreading)**과 **멀티프로세싱(Multiprocessing)**에 대해 알아보았습니다. 이번 포스팅에서는 동시성 문제를 더욱 깊이 있게 이해하기 위해 **동기(Synchronous)**, **비동기(Asynchronous)**, **블로킹(Blocking)**, **논블로킹(Non-Blocking)**의 개념이 동시성과 어떻게 연결되는지 살펴보겠습니다.

## 동시성에서의 동기와 비동기

### 동기(Synchronous) 프로그래밍이란?

동기 프로그래밍은 작업이 순차적으로 실행되는 방식을 의미합니다. 하나의 작업이 완료될 때까지 다음 작업은 시작되지 않습니다. 이러한 방식은 코드의 흐름이 명확하고 이해하기 쉬우나, 긴 작업이 있을 경우 전체 프로그램의 응답성이 저하될 수 있습니다.

**예시: 동기 파일 읽기**
```python
def read_file_sync(file_path):
    with open(file_path, 'r') as file:
        data = file.read()
    print(data)

# 함수 호출
read_file_sync('example.txt')
```

*위 예제는 파일을 동기적으로 읽는 간단한 파이썬 코드입니다. 파일 읽기가 완료될 때까지 다음 코드가 실행되지 않습니다.*


### 비동기(Asynchronous) 프로그래밍이란?

비동기 프로그래밍은 작업이 백그라운드에서 실행되도록 하여, 현재 작업이 완료될 때까지 기다리지 않고 다음 작업을 계속할 수 있게 합니다. 이를 통해 프로그램의 응답성을 유지하면서 여러 작업을 효율적으로 처리할 수 있습니다.

**예시: 비동기 파일 읽기**
```python
import asyncio

async def read_file_async(file_path):
    loop = asyncio.get_event_loop()
    with open(file_path, 'r') as file:
        data = await loop.run_in_executor(None, file.read)
    print(data)

# 이벤트 루프에서 함수 실행
asyncio.run(read_file_async('example.txt'))
```

*위 예제는 파일을 비동기적으로 읽는 파이썬 코드입니다. 파일 읽기 작업이 백그라운드에서 실행되는 동안 다른 작업을 수행할 수 있습니다.*


## 블로킹과 논블로킹

### 블로킹(Blocking) I/O란?

블로킹 I/O는 입출력 작업이 완료될 때까지 해당 스레드가 대기 상태에 머무르는 방식을 말합니다. 이 방식은 구현이 간단하고 직관적이지만, 많은 수의 블로킹 호출이 발생하면 전체 애플리케이션의 성능이 저하될 수 있습니다.

**예시: 블로킹 네트워크 요청**
```python
import requests

def fetch_data(url):
    response = requests.get(url)
    return response.text

# 네트워크 요청 호출
data = fetch_data('https://api.example.com/data')
print(data)
```

*위 예제는 블로킹 방식으로 네트워크 요청을 수행하는 파이썬 코드입니다. 요청이 완료될 때까지 코드 실행이 멈춥니다.*

### 논블로킹(Non-Blocking) I/O란?

논블로킹 I/O는 입출력 작업이 즉시 반환되어, 작업의 완료 여부와 상관없이 다음 작업을 계속할 수 있는 방식을 의미합니다. 이를 통해 입출력 대기 시간 동안 다른 작업을 수행할 수 있어 시스템 자원의 효율성을 높일 수 있습니다.

**예시: 논블로킹 네트워크 요청**
```python
import aiohttp
import asyncio

async def fetch_data_non_blocking(url):
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as response:
            return await response.text()

# 이벤트 루프에서 함수 실행
async def main():
    data = await fetch_data_non_blocking('https://api.example.com/data')
    print(data)

asyncio.run(main())
```

*위 예제는 논블로킹 방식으로 네트워크 요청을 수행하는 비동기 파이썬 코드입니다. 요청이 진행되는 동안 다른 작업을 수행할 수 있습니다.*


## 동시성과 동기 절차의 상호작용

동기와 비동기, 블로킹과 논블로킹은 동시성 프로그래밍의 핵심 개념으로, 이를 적절히 활용하면 프로그램의 성능과 응답성을 크게 향상시킬 수 있습니다. 다음은 이들 개념이 어떻게 상호작용하며, 동시성을 구현하는데 어떻게 기여하는지에 대한 이해를 돕는 표입니다:

| 개념               | 동기(Synchronous) | 비동기(Asynchronous) |
|--------------------|-------------------|----------------------|
| **블로킹(Blocking)**    | 참                | 거짓                  |
| **논블로킹(Non-Blocking)**| 거짓               | 참                   |
| **동시성 구현 방식**     | 순차적             | 병렬적 또는 이벤트 기반 |

## 동시성 문제 해결을 위한 Best Practices

동시성과 관련된 문제를 효과적으로 해결하기 위해 다음과 같은 Best Practices를 고려할 수 있습니다:

1. **작업의 특성 파악**:
   - **CPU 바운드 작업**: 멀티프로세싱이나 멀티스레딩을 활용하여 병렬 처리를 고려합니다.
   - **I/O 바운드 작업**: 비동기 프로그래밍이나 논블로킹 I/O를 활용하여 응답성을 높입니다.

2. **적절한 동기화 메커니즘 사용**:
   - 스레드 간의 자원 공유 시 **뮤텍스(Mutex)**, **세마포어(Semaphore)** 등을 사용하여 데이터 일관성을 유지합니다.

3. **비동기 코드의 예외 처리**:
   - 비동기 작업에서 발생할 수 있는 예외를 적절히 처리하여 프로그램의 안정성을 확보합니다.

4. **테스트와 디버깅**:
   - 동시성 관련 버그는 재현이 어렵기 때문에, **스트레스 테스트**와 **디버깅 도구**를 활용하여 문제를 조기에 발견하고 해결합니다.

## 결론

동시성은 현대 소프트웨어 개발에서 필수적인 개념으로, 이를 효과적으로 구현하기 위해서는 **동기(Synchronous)**, **비동기(Asynchronous)**, **블로킹(Blocking)**, **논블로킹(Non-Blocking)**의 개념을 명확히 이해하고 적절히 활용하는 것이 중요합니다. 멀티스레딩과 멀티프로세싱과 같은 병렬성 기법과 함께 이러한 동기화 개념을 결합하면, 성능과 효율성을 극대화할 수 있는 고성능 애플리케이션을 개발할 수 있습니다.

---

### 참고 자료

1. [Understanding Concurrency and Parallelism](https://powerclabman.tistory.com/118)
2. [Concurrency vs Parallelism](https://binux.tistory.com/169)
3. [Asynchronous Programming Explained](https://f-lab.kr/insight/understanding-async-and-concurrency)
4. [Blocking vs Non-Blocking I/O](https://cordcat.tistory.com/208)
5. [멀티프로세스와 멀티스레드의 차이점](https://wooody92.github.io/os/%EB%A9%80%ED%8B%B0-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4%EC%99%80-%EB%A9%80%ED%8B%B0-%EC%8A%A4%EB%A0%88%EB%93%9C/)
6. [Non-Blocking I/O in Python](https://velog.io/@hosunghan0821/CPU-Bound-IO-Bound)
7. [비동기 프로그래밍과 코루틴](https://perfectacle.github.io/2023/07/10/java-virtual-thread-vs-kotlin-coroutine/)
8. [Concurrency Programming Best Practices](https://f-lab.kr/insight/understanding-concurrency-programming)

```