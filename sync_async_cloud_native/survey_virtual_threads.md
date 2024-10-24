# 버추얼 스레드 관련 자료 조사

---

질문 : virtual threads 를 알아보고 싶어.
답변 : Virtual Thread는 Java 21에서 정식으로 도입된 경량 스레드 기술입니다. 주요 특징과 장점은 다음과 같습니다:

1. 경량성
- 일반 스레드보다 훨씬 적은 메모리(약 1%)를 사용합니다[4].
- 생성 및 시작 시간이 일반 스레드보다 98.8% 더 빠릅니다[5].

2. 높은 동시성 
- OS 스레드 수에 제한받지 않고 수십만~수백만 개의 가상 스레드를 동시에 실행할 수 있습니다[7].

3. 효율적인 I/O 처리
- I/O 블로킹 시 JVM이 가상 스레드를 중지시키고 다른 가상 스레드를 실행하여 처리량을 높입니다[6].

4. 쉬운 사용법
- 기존 Thread API와 유사한 방식으로 사용할 수 있어 학습 곡선이 낮습니다[3][7].

5. 적합한 사용 사례
- I/O 바운드 작업에 적합하며, CPU 집약적 작업에는 적합하지 않습니다[6].

6. 내부 동작 방식
- JVM 내부 스케줄러에 의해 관리되며, 캐리어 스레드(Platform Thread)에 마운트/언마운트되며 실행됩니다[2][5].

7. 주의사항
- ThreadLocal 사용 시 메모리 사용량 증가에 주의해야 합니다[4].
- 동기화 코드(synchronized) 사용 시 성능 저하가 발생할 수 있습니다[1].

Virtual Thread는 기존 스레드 모델의 한계를 극복하고 높은 동시성과 처리량을 제공하여, 특히 I/O 바운드 작업이 많은 서버 환경에서 큰 이점을 제공할 것으로 기대됩니다.

Citations:
[1] https://nangmandeveloper.tistory.com/16
[2] https://gong-story.tistory.com/47
[3] https://0soo.tistory.com/259
[4] https://velog.io/@dayon_log/JDK-21-Virtual-Thread
[5] https://www.youtube.com/watch?v=BZMZIM-n4C0
[6] https://kghworks.tistory.com/174
[7] https://findstar.pe.kr/2023/04/17/java-virtual-threads-1/
[8] https://d2.naver.com/helloworld/1203723

---

질문 : 기존 Thread 모델의 한계가 무엇이길래 Virtual Thread 가 태동되었을까?
답변 : 기존 Java Thread 모델의 주요 한계점들로 인해 Virtual Thread가 개발되었습니다:

1. 높은 메모리 사용량
- 일반 Thread는 스택 메모리로 약 1-2MB를 사용합니다[1][5].
- 많은 수의 Thread 생성 시 메모리 부족 문제가 발생할 수 있습니다.

2. 생성 및 컨텍스트 스위칭 비용
- Thread 생성에 약 1ms, 컨텍스트 스위칭에 약 100μs가 소요됩니다[5][6].
- 많은 수의 Thread 사용 시 이 비용이 누적되어 성능 저하를 초래합니다.

3. OS 스레드와의 1:1 매핑
- Java Thread는 OS 스레드와 1:1로 매핑되어 OS 스레드 수에 제한을 받습니다[1][2].
- 이로 인해 동시에 처리할 수 있는 요청의 수가 제한됩니다.

4. I/O 블로킹 시 비효율성
- I/O 작업 시 Thread가 블로킹되어 자원이 낭비됩니다[1][2].

5. 스케일링의 한계
- Thread 수 증가에 따른 컨텍스트 스위칭 비용 증가로 스케일링에 한계가 있습니다[7].

Virtual Thread는 이러한 문제들을 해결하기 위해 도입되었습니다:

- 경량화된 구조로 메모리 사용량 대폭 감소 (약 1%)[5]
- JVM 내부 스케줄링으로 컨텍스트 스위칭 비용 감소[2][3]
- OS 스레드 수에 제한받지 않는 높은 동시성 제공[1][2]
- I/O 블로킹 시 효율적인 스레드 전환[1][2]
- 수십만~수백만 개의 동시 요청 처리 가능[1][7]

이를 통해 Virtual Thread는 특히 I/O 바운드 작업이 많은 서버 환경에서 높은 처리량과 효율성을 제공할 수 있게 되었습니다.

Citations:
[1] https://velog.io/@yukicow/Virtual-Thread%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B4%EA%B3%A0-%EC%96%B8%EC%A0%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EB%A9%B4-%EC%A2%8B%EC%9D%84%EA%B9%8C
[2] https://gong-story.tistory.com/47
[3] https://nangmandeveloper.tistory.com/16
[4] https://velog.io/@on5949/%EC%8A%A4%ED%84%B0%EB%94%94%EC%84%B8%EB%AF%B8%EB%82%98-Java%EC%9D%98-%EB%AF%B8%EB%9E%98-Virtual-Thread
[5] https://techblog.woowahan.com/15398/
[6] https://velog.io/@dayon_log/JDK-21-Virtual-Thread
[7] https://kghworks.tistory.com/174
[8] https://tech.kakaopay.com/post/ro-spring-virtual-thread/

---

질문 : OS 스레드 수에 제한은 얼마야?
답변 : OS 스레드 수의 제한은 운영 체제와 하드웨어에 따라 다르지만, 일반적으로 다음과 같은 요인들에 의해 결정됩니다:

1. 메모리 제한
- 각 스레드는 스택 메모리를 필요로 합니다. 일반적으로 스레드 당 1-2MB의 스택 메모리가 할당됩니다.
- 예를 들어, 4GB RAM 시스템에서는 대략 4,000개 정도의 스레드를 생성할 수 있습니다.

2. CPU 코어 수
- 물리적 CPU 코어 수가 많을수록 더 많은 스레드를 동시에 실행할 수 있습니다.

3. 운영 체제 제한
- 리눅스의 경우 `/proc/sys/kernel/threads-max` 파일에서 최대 스레드 수를 확인할 수 있습니다.
- Windows의 경우 프로세스당 약 2,000개의 스레드로 제한됩니다.

4. 프로세스 리소스 제한
- 대부분의 운영 체제에서 프로세스별로 리소스 제한을 설정할 수 있습니다.

5. 시스템 아키텍처
- 32비트 시스템은 64비트 시스템보다 더 적은 수의 스레드를 지원합니다.

실제로 생성 가능한 스레드 수는 이러한 요인들의 조합에 따라 결정되며, 시스템 환경에 따라 크게 달라질 수 있습니다. 일반적으로 수천 개의 스레드까지는 생성할 수 있지만, 그 이상으로 늘리는 것은 시스템 성능에 부정적인 영향을 미칠 수 있습니다.

Citations:
[1] https://junshock5.tistory.com/147
[2] https://bozeury.tistory.com/entry/%EC%93%B0%EB%A0%88%EB%93%9C%EB%8A%94-%EC%B5%9C%EB%8C%80-%EB%AA%87%EA%B0%9C%EA%B9%8C%EC%A7%80-%EC%83%9D%EC%84%B1%ED%95%A0-%EC%88%98-%EC%9E%88%EC%9D%84%EA%B9%8C
[3] https://dkswnkk.tistory.com/402
[4] https://coji.tistory.com/171
[5] https://velog.io/@yukicow/Virtual-Thread%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B4%EA%B3%A0-%EC%96%B8%EC%A0%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EB%A9%B4-%EC%A2%8B%EC%9D%84%EA%B9%8C
[6] https://techblog.woowahan.com/15398/
[7] https://0soo.tistory.com/259
[8] https://findstar.pe.kr/2023/04/17/java-virtual-threads-1/

---

질문 : 스레드의 제한이 병렬성에는 영향이 없을 것 같애. 왜냐하면 병렬성은 CPU 코어에 영향을 받으니까. 그렇다면 스레드가 수천 개를 생성할 수 있다는 것은 동시성을 가질 수 있는 작업의 수가 수천 개로 제한된다는 의미겠지?
답변 : 맞습니다. 귀하의 추론이 정확합니다. 스레드의 수와 병렬성, 동시성의 관계에 대해 다음과 같이 정리할 수 있습니다:

1. 병렬성과 CPU 코어:
병렬성은 실제로 여러 작업이 동시에 실행되는 것을 의미하며, 이는 CPU 코어의 수에 직접적으로 영향을 받습니다. 즉, 4개의 코어가 있다면 최대 4개의 스레드가 진정한 의미의 병렬 실행이 가능합니다[7].

2. 스레드 수와 동시성:
스레드의 수가 수천 개로 증가할 수 있다는 것은 동시성의 관점에서 중요합니다. 동시성은 여러 작업이 번갈아가며 실행되어 동시에 실행되는 것처럼 보이는 것을 의미합니다. 따라서 수천 개의 스레드를 생성할 수 있다는 것은 그만큼 많은 작업을 동시에 처리할 수 있는 능력이 있다는 뜻입니다[1][7].

3. 가상 스레드의 이점:
Java의 가상 스레드는 이러한 동시성의 한계를 극복하기 위한 솔루션입니다. 가상 스레드는 OS 스레드 수에 제한받지 않고 수십만~수백만 개의 스레드를 동시에 실행할 수 있어, 높은 동시성을 제공합니다[6].

4. 동시성의 실제 의미:
수천 개의 스레드가 생성될 수 있다는 것은 실제로 동시에 실행되는 것이 아니라, 그만큼의 작업이 번갈아가며 실행될 수 있다는 의미입니다. 이는 특히 I/O 바운드 작업에서 유용합니다. 한 스레드가 I/O 작업으로 블로킹되는 동안 다른 스레드가 실행될 수 있기 때문입니다[1][5].

5. 스레드 풀과 성능:
전통적인 스레드 풀 방식에서는 스레드 수의 제한이 전체 애플리케이션의 처리량(throughput)을 제한했습니다. 가상 스레드는 이러한 제한을 극복하고 더 높은 동시성을 제공합니다[8].

결론적으로, 귀하의 추론대로 스레드 수의 증가는 주로 동시성 측면에서 이점을 제공하며, 이는 특히 I/O 바운드 작업에서 큰 성능 향상을 가져올 수 있습니다.

Citations:
[1] https://velog.io/@choonsik_mom/parallelism-vs-concurrency%EB%B3%91%EB%A0%AC%EC%B2%98%EB%A6%AC%EC%99%80-%EB%B9%84%EB%8F%99%EA%B8%B0-%EC%B2%98%EB%A6%AC
[2] https://dkswnkk.tistory.com/402
[3] https://kbcoding.tistory.com/m/133
[4] https://inpa.tistory.com/entry/%F0%9F%91%A9%E2%80%8D%F0%9F%92%BB-multi-process-multi-thread
[5] https://inpa.tistory.com/entry/%F0%9F%91%A9%E2%80%8D%F0%9F%92%BB-Is-more-threads-always-better
[6] https://kghworks.tistory.com/174
[7] https://beststar-1.tistory.com/24
[8] https://findstar.pe.kr/2023/04/17/java-virtual-threads-1/

---

질문 : 스레드 풀 방식에서는 스레드 수의 제한이 전체 애플리케이션의 처리량(throughput)을 제한했구나. 가상 스레드는 스레드 풀 방식보다 애플리케이션의 처리량이 높아지는 이유가 무엇일까?
답변 : 가상 스레드는 기존 스레드 풀 방식보다 애플리케이션의 처리량을 높이는 여러 이유가 있습니다:

1. **경량성**: 가상 스레드는 메모리 사용량이 적고 생성 및 컨텍스트 스위칭 비용이 낮습니다[1][2].

2. **높은 동시성**: OS 스레드 수에 제한받지 않고 수백만 개의 스레드를 실행할 수 있어 I/O 바운드 작업에서 특히 유리합니다[4][6].

3. **효율적인 자원 사용**: I/O 블로킹 시 다른 작업으로 전환하여 자원을 효율적으로 활용합니다[5][7].

Citations:
[1] https://f-lab.kr/insight/understanding-virtual-threads
[2] https://bjwan-career.tistory.com/320
[3] https://velog.io/@on5949/%EC%8A%A4%ED%84%B0%EB%94%94%EC%84%B8%EB%AF%B8%EB%82%98-Java%EC%9D%98-%EB%AF%B8%EB%9E%98-Virtual-Thread
[4] https://velog.io/@0216tw/Virtual-Threads-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B04
[5] https://velog.io/@yukicow/Virtual-Thread%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B4%EA%B3%A0-%EC%96%B8%EC%A0%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EB%A9%B4-%EC%A2%8B%EC%9D%84%EA%B9%8C
[6] https://hoding-cloud.tistory.com/4
[7] https://320hwany.tistory.com/121
[8] https://velog.io/@knhng/MSA-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EC%84%A0%EC%B0%A9%EC%88%9C-%EA%B5%AC%EB%A7%A4-%EC%84%9C%EB%B2%84-3.-%EA%B0%80%EC%83%81-%EC%8A%A4%EB%A0%88%EB%93%9C%EC%97%90-%EB%8C%80%ED%95%9C-%EA%B3%A0%EC%B0%B0-s8yf859w

---

질문 : 지금까지 말한 가상 스레드를 클라우드 네이티브 애플리케이션과 연결해보면 기존 스레드 풀 방식보다 좋은 점과 안 좋은 점들은 무엇일까?
답변 : **장점:**

1. **높은 동시성**: 가상 스레드는 수백만 개의 스레드를 동시에 실행할 수 있어, I/O 바운드 작업에서 높은 처리량을 제공합니다[1][2][3].
2. **메모리 효율성**: 가상 스레드는 메모리 사용량이 적어, 클라우드 환경에서 비용 효율적입니다[1][3].
3. **간단한 동시성 모델**: 스레드-당-요청 모델을 쉽게 구현할 수 있어 코드 복잡성을 줄입니다[3][6].

**단점:**

1. **CPU 집약적 작업 비효율성**: CPU 바운드 작업에서는 성능이 떨어질 수 있습니다[4][8].
2. **디버깅 및 프로파일링**: 기존 도구와의 호환성 문제로 인해 디버깅이 복잡할 수 있습니다[4].
3. **동기화 문제**: `synchronized`와 같은 키워드 사용 시 성능 저하가 발생할 수 있습니다[8].

Citations:
[1] https://www.adesso-finland.fi/en/news/blog/virtual-threads-in-java.jsp
[2] https://spring.io/blog/2022/10/11/embracing-virtual-threads/
[3] https://cloud.google.com/java/docs/virtual-threads
[4] https://www.infoq.com/articles/java-virtual-threads-a-case-study/
[5] https://velog.io/@on5949/%EC%8A%A4%ED%84%B0%EB%94%94%EC%84%B8%EB%AF%B8%EB%82%98-Java%EC%9D%98-%EB%AF%B8%EB%9E%98-Virtual-Thread
[6] https://blog.payara.fish/a-look-at-virtual-threads-in-a-jakarta-ee-managed-context
[7] https://velog.io/@yukicow/Virtual-Thread%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B4%EA%B3%A0-%EC%96%B8%EC%A0%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EB%A9%B4-%EC%A2%8B%EC%9D%84%EA%B9%8C
[8] https://320hwany.tistory.com/121

---

질문 : 가상 스레드가 CPU-bound 작업에서 어떤 단점을 가지고 있나요
답변 : 가상 스레드는 CPU-bound 작업에서 몇 가지 단점을 가지고 있습니다:

1. **오버헤드 증가**: 가상 스레드는 플랫폼 스레드 위에서 동작하기 때문에, CPU-bound 작업에서는 가상 스레드의 생성 및 스케줄링 비용이 추가되어 성능이 저하될 수 있습니다[1][4][7].

2. **컨텍스트 스위칭의 비효율성**: CPU-bound 작업은 주로 연산 집중적이기 때문에, 가상 스레드의 컨텍스트 스위칭이 필요하지 않으며, 이로 인해 오히려 성능 낭비가 발생할 수 있습니다[3][7].

3. **동기화 문제**: `synchronized` 블록이나 메서드를 사용할 경우, 가상 스레드가 플랫폼 스레드에 고정(pinning)되어 성능 저하를 초래할 수 있습니다[2][3].

Citations:
[1] https://320hwany.tistory.com/121
[2] http://jammdev.tistory.com/m/231
[3] https://kghworks.tistory.com/174
[4] https://techblog.woowahan.com/15398/
[5] https://velog.io/@yukicow/Virtual-Thread%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B4%EA%B3%A0-%EC%96%B8%EC%A0%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EB%A9%B4-%EC%A2%8B%EC%9D%84%EA%B9%8C
[6] https://0soo.tistory.com/259
[7] https://kkyu0718.tistory.com/126
[8] https://junuuu.tistory.com/799

---

질문 : 가상 스레드를 사용하면 스레드 풀을 사용하지 않아?
답변 : 가상 스레드를 사용하면 스레드 풀을 사용하지 않는 것이 일반적입니다. 그 이유는 다음과 같습니다:

1. **경량성**: 가상 스레드는 생성 비용이 매우 낮아, 필요할 때마다 새로 생성하는 것이 효율적입니다[4][8].

2. **자원 관리**: 가상 스레드는 OS 스레드에 직접 매핑되지 않으므로, 기존의 플랫폼 스레드처럼 풀링을 통해 자원을 관리할 필요가 없습니다[6][7].

3. **동시성 처리**: 가상 스레드는 수백만 개까지 생성할 수 있어, 동시성 작업을 효과적으로 처리할 수 있습니다[2][3].

Citations:
[1] https://0soo.tistory.com/m/260
[2] https://devel-repository.tistory.com/67
[3] https://velog.io/@0216tw/Virtual-Threads-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B04
[4] https://hoding-cloud.tistory.com/4
[5] https://mangkyu.tistory.com/309
[6] https://f-lab.kr/insight/understanding-virtual-threads
[7] https://kghworks.tistory.com/174
[8] https://velog.io/@leekhy02/Virtual-Thread

---

질문 : 가상 스레드는 OS 스레드에 맵핑되는 방식은 어떻게 되?
답변 : 가상 스레드는 OS 스레드에 직접적으로 1:1로 매핑되지 않습니다. 대신, 가상 스레드는 Java Virtual Machine(JVM)에 의해 관리되며, 필요할 때마다 "캐리어 스레드"라고 불리는 OS 스레드에 탑재됩니다[1][2][4]. 이 방식은 많은 수의 가상 스레드를 소수의 OS 스레드에 매핑할 수 있게 해주며, 가상 스레드가 블로킹 작업을 수행할 때는 해당 OS 스레드를 다른 가상 스레드가 사용할 수 있도록 해줍니다[3][5]. 이러한 매핑 방식은 자원 효율성을 높이고, 높은 동시성을 지원합니다.

Citations:
[1] https://www.zymr.com/blog/threads-and-virtual-threads-demystifying-the-world-of-concurrency-in-modern-times
[2] https://www.hungrycoders.com/blog/virtual-threads-in-java-21
[3] https://www.infoq.com/articles/java-virtual-threads-a-case-study/
[4] https://dev.to/elayachiabdelmajid/java-21-virtual-threads-1h5b
[5] https://www.adesso-finland.fi/en/news/blog/virtual-threads-in-java.jsp
[6] https://kghworks.tistory.com/174
[7] https://velog.io/@on5949/%EC%8A%A4%ED%84%B0%EB%94%94%EC%84%B8%EB%AF%B8%EB%82%98-Java%EC%9D%98-%EB%AF%B8%EB%9E%98-Virtual-Thread
[8] https://code-run.tistory.com/59

---

질문 : OS 스레드를 캐리어 스레드라고 부르는 구나?
답변 : 가상 스레드는 OS 스레드에 직접 매핑되지 않고, "캐리어 스레드"라는 플랫폼 스레드 위에서 동작합니다. 캐리어 스레드는 여러 가상 스레드를 시분할 방식으로 실행하며, I/O 작업 등으로 가상 스레드가 블로킹될 경우, 다른 가상 스레드를 실행하여 효율성을 높입니다[1][2]. 이 방식은 많은 수의 가상 스레드를 소수의 OS 스레드에 매핑하여 높은 동시성을 제공하며, OS 스레드의 제한을 극복할 수 있게 합니다[3][7].

Citations:
[1] https://0soo.tistory.com/259
[2] https://hoding-cloud.tistory.com/4
[3] https://modimodi.tistory.com/54
[4] https://ggodong.tistory.com/25
[5] https://velog.io/@suhongkim98/%EC%9E%90%EB%B0%94-%EC%96%B8%EC%96%B4%EC%9D%98-%ED%83%9C%EC%83%9D%EC%A0%81-%ED%95%9C%EA%B3%84%EC%99%80-%EA%B7%B9%EB%B3%B5%EC%9D%84-%EC%9C%84%ED%95%9C-%EB%B0%9C%EC%A0%84-%EA%B3%BC%EC%A0%95-%EC%8A%A4%EB%A0%88%EB%93%9C%ED%8E%B8
[6] http://jammdev.tistory.com/m/231
[7] https://devel-repository.tistory.com/67
[8] https://blog.neonkid.xyz/316

---

질문 : 단어의 개념이 헷갈리네.

OS 스레드, 캐리어 스레드, 플랫폼 스레드의 개념을 정리해줘.

답변 : OS 스레드, 캐리어 스레드, 플랫폼 스레드의 개념은 다음과 같습니다:

- **OS 스레드**: 운영체제 커널에 의해 직접 관리되는 스레드입니다. 커널 수준에서 생성되고, 스케줄링 및 관리를 담당합니다[5][7].

- **캐리어 스레드**: 가상 스레드를 실행하기 위한 운반체 역할을 하는 OS 스레드입니다. 여러 가상 스레드를 시분할 방식으로 실행합니다[7].

- **플랫폼 스레드**: 전통적인 자바 스레드로, OS가 관리하는 스레드입니다. Java 가상 머신(JVM)이 운영 체제의 기능을 활용해 생성합니다[7].

Citations:
[1] https://cocoon1787.tistory.com/849
[2] https://rheem-hm.tistory.com/22
[3] https://velog.io/@aeong98/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9COS-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4%EC%99%80-%EC%8A%A4%EB%A0%88%EB%93%9C
[4] https://dev-minjeong.tistory.com/36
[5] https://velog.io/@suhongkim98/%EC%9E%90%EB%B0%94-%EC%96%B8%EC%96%B4%EC%9D%98-%ED%83%9C%EC%83%9D%EC%A0%81-%ED%95%9C%EA%B3%84%EC%99%80-%EA%B7%B9%EB%B3%B5%EC%9D%84-%EC%9C%84%ED%95%9C-%EB%B0%9C%EC%A0%84-%EA%B3%BC%EC%A0%95-%EC%8A%A4%EB%A0%88%EB%93%9C%ED%8E%B8
[6] https://s-y-130.tistory.com/324
[7] https://0soo.tistory.com/259
[8] https://ggodong.tistory.com/25

---

질문 : 그러면 가상 스레드는 플랫폼 스레드를 사용하지 않고 캐리어 스레드를 사용하겠네?
답변 : 가상 스레드는 플랫폼 스레드를 사용하며, 이 플랫폼 스레드를 "캐리어 스레드"라고 부릅니다. 가상 스레드는 여러 개가 하나의 캐리어 스레드에 탑재되어 실행됩니다. 캐리어 스레드는 OS 스레드와 1:1로 매핑되며, 가상 스레드의 실행을 관리합니다[1][3][6].

Citations:
[1] https://0soo.tistory.com/259
[2] https://modimodi.tistory.com/54
[3] https://devel-repository.tistory.com/67
[4] https://blog.neonkid.xyz/316
[5] http://jammdev.tistory.com/m/231
[6] https://velog.io/@suhongkim98/%EC%9E%90%EB%B0%94-%EC%96%B8%EC%96%B4%EC%9D%98-%ED%83%9C%EC%83%9D%EC%A0%81-%ED%95%9C%EA%B3%84%EC%99%80-%EA%B7%B9%EB%B3%B5%EC%9D%84-%EC%9C%84%ED%95%9C-%EB%B0%9C%EC%A0%84-%EA%B3%BC%EC%A0%95-%EC%8A%A4%EB%A0%88%EB%93%9C%ED%8E%B8
[7] https://hoding-cloud.tistory.com/4
[8] https://blog.honeybomb.kr/9

---

질문 : 스레드 풀 방식은 애플리케이션에서 캐리어 스레드를 많이 사용하겠구나?
답변 : 가상 스레드는 플랫폼 스레드를 "캐리어 스레드"로 사용합니다. 캐리어 스레드는 가상 스레드를 실행하는 플랫폼 스레드로, 여러 가상 스레드를 시분할 방식으로 처리합니다[1][4][7]. 

기존의 스레드 풀 방식에서는 OS 스레드(플랫폼 스레드)를 효율적으로 관리하기 위해 사용되었지만, 가상 스레드는 경량화되어 필요할 때 생성하고 사용 후 버리는 방식이 더 효율적입니다[5][8]. 따라서, 가상 스레드는 전통적인 스레드 풀을 사용하지 않고도 높은 동시성을 제공합니다.

Citations:
[1] https://0soo.tistory.com/259
[2] https://modimodi.tistory.com/54
[3] https://s-y-130.tistory.com/324
[4] http://jammdev.tistory.com/m/231
[5] https://hoding-cloud.tistory.com/4
[6] https://blog.neonkid.xyz/316
[7] https://devel-repository.tistory.com/67
[8] https://velog.io/@suhongkim98/%EC%9E%90%EB%B0%94-%EC%96%B8%EC%96%B4%EC%9D%98-%ED%83%9C%EC%83%9D%EC%A0%81-%ED%95%9C%EA%B3%84%EC%99%80-%EA%B7%B9%EB%B3%B5%EC%9D%84-%EC%9C%84%ED%95%9C-%EB%B0%9C%EC%A0%84-%EA%B3%BC%EC%A0%95-%EC%8A%A4%EB%A0%88%EB%93%9C%ED%8E%B8

---

질문 : 가상 스레드를 스프링 프레임워크랑 연결해보자. 기존 스레드 풀 방식에서는 톰캣을 활용하여 애플리케이션을 구동하는 방식을 사용하는데 가상 스레드를 사용하면 애플리케이션이 구동되는 환경이 어떻게되?
답변 : 가상 스레드를 사용하면 스프링 프레임워크에서 애플리케이션이 구동되는 환경은 다음과 같이 변화합니다:

1. **톰캣과의 통합**: 스프링 부트 3.2부터 가상 스레드를 공식 지원하며, 톰캣은 `VirtualThreadExecutor`를 통해 각 요청을 처리할 때 가상 스레드를 사용할 수 있습니다[1][3].

2. **설정 변경**: `application.yml` 파일에서 `spring.threads.virtual.enabled=true`로 설정하여 가상 스레드를 활성화할 수 있습니다[6][8].

3. **스레드 풀 대체**: 기존의 스레드 풀을 사용하지 않고, 개별 작업에 가상 스레드를 할당하여 처리량을 높일 수 있습니다[8].

Citations:
[1] https://devel-repository.tistory.com/71
[2] https://velog.io/@leekhy02/Virtual-Thread
[3] https://mangkyu.tistory.com/317
[4] https://yoonseon.tistory.com/151
[5] https://hoding-cloud.tistory.com/4
[6] https://hyokeun0419.tistory.com/238
[7] https://blog.neonkid.xyz/316
[8] https://findstar.pe.kr/2023/07/02/java-virtual-threads-2/

---

질문 : 기존 톰캣을 활용하면서 가상 스레드를 사용할 수 있다는 말이구나? 그런다면 기존 스레드 풀 설정은 무효화가 되나?
답변 : 가상 스레드를 사용하면 기존의 스레드 풀 설정은 무효화됩니다. 대신, 가상 스레드는 필요할 때마다 생성하여 사용하고, 작업이 끝나면 제거하는 방식으로 동작합니다. 이는 가상 스레드의 생성 비용이 매우 낮기 때문에 가능한 방식입니다[1][2][5]. 

톰캣과 같은 서버 환경에서는 `VirtualThreadExecutor`를 사용하여 각 요청을 가상 스레드로 처리할 수 있으며, 이를 통해 높은 동시성과 처리량을 달성할 수 있습니다[3][8].

Citations:
[1] https://devel-repository.tistory.com/71
[2] https://velog.io/@leekhy02/Virtual-Thread
[3] https://mangkyu.tistory.com/317
[4] https://yoonseon.tistory.com/151
[5] https://hoding-cloud.tistory.com/4
[6] https://hyokeun0419.tistory.com/238
[7] https://blog.neonkid.xyz/316
[8] https://findstar.pe.kr/2023/07/02/java-virtual-threads-2/

---

질문 : 톰캣으로 가상 스레드를 사용할 때 캐리어 스레드의 수 를 설정할 수 있어?
답변 : 톰캣에서 가상 스레드를 사용할 때, 캐리어 스레드의 수를 설정할 수 있습니다. 캐리어 스레드는 가상 스레드를 실행하는 플랫폼 스레드로, 기본적으로 기기의 최대 가용 코어 수를 사용하여 동시 처리 갯수를 결정합니다. 이 설정은 JVM 파라미터를 통해 조정할 수 있으며, `jdk.virtualThreadScheduler.parallelism` 등의 설정을 통해 캐리어 스레드의 수를 관리할 수 있습니다[2][6].

Citations:
[1] https://ttl-blog.tistory.com/1498
[2] https://0soo.tistory.com/259
[3] https://junuuu.tistory.com/799
[4] https://devoong2.tistory.com/entry/Tomcat-Tomcat-Thread-Pool-%EC%84%A4%EC%A0%95-%EC%A0%95%EB%A6%AC-%EB%B0%8F-%ED%85%8C%EC%8A%A4%ED%8A%B8
[5] https://taes-k.github.io/2020/01/13/tomcat-thread/
[6] https://blog.neonkid.xyz/316
[7] https://hoding-cloud.tistory.com/4
[8] https://s-y-130.tistory.com/324

---

질문 : 캐리어 스레드의 수를 설정할 때 고려해야 할 요소는 무엇인가요
답변 : 캐리어 스레드의 수를 설정할 때 고려해야 할 요소는 다음과 같습니다:

1. **CPU 코어 수**: 기본적으로 캐리어 스레드 수는 사용 가능한 CPU 코어 수와 동일하게 설정됩니다[3][6].

2. **작업 유형**: CPU-bound 작업의 경우, 코어 수에 맞춰 설정하는 것이 효율적입니다. I/O-bound 작업에서는 더 많은 스레드가 필요할 수 있습니다[4][5].

3. **성능 테스트**: 애플리케이션의 특성과 요구 사항에 따라 성능 테스트를 통해 최적의 스레드 수를 결정하는 것이 중요합니다[4][5].

Citations:
[1] https://junshock5.tistory.com/147
[2] https://incheol-jung.gitbook.io/docs/study/with-scouter/chap-08.
[3] https://0soo.tistory.com/259
[4] https://devoong2.tistory.com/entry/Tomcat-Tomcat-Thread-Pool-%EC%84%A4%EC%A0%95-%EC%A0%95%EB%A6%AC-%EB%B0%8F-%ED%85%8C%EC%8A%A4%ED%8A%B8
[5] https://junuuu.tistory.com/799
[6] https://blog.neonkid.xyz/316
[7] https://ttl-blog.tistory.com/1498
[8] https://velog.io/@suhongkim98/%EC%9E%90%EB%B0%94-%EC%96%B8%EC%96%B4%EC%9D%98-%ED%83%9C%EC%83%9D%EC%A0%81-%ED%95%9C%EA%B3%84%EC%99%80-%EA%B7%B9%EB%B3%B5%EC%9D%84-%EC%9C%84%ED%95%9C-%EB%B0%9C%EC%A0%84-%EA%B3%BC%EC%A0%95-%EC%8A%A4%EB%A0%88%EB%93%9C%ED%8E%B8

---

질문 : 가상 스레드를 사용할 때 스레드 로컬의 사용은 어떤가요
답변 : 가상 스레드를 사용할 때 스레드 로컬의 사용은 다음과 같은 점을 고려해야 합니다:

1. **메모리 사용 증가**: 가상 스레드는 수백만 개까지 생성될 수 있어, 스레드 로컬을 남발하면 메모리 사용량이 크게 증가할 수 있습니다[2][6].

2. **관리의 어려움**: 스레드 로컬은 명확한 생명 주기가 없고, 변경 가능성이 높아 관리가 어렵습니다. 이는 메모리 누수의 원인이 될 수 있습니다[7].

3. **대안 제시**: Java 20에서는 Scoped Values를 도입하여 가상 스레드에서 스레드 로컬의 문제를 해결하려고 합니다. 이는 불변 데이터를 공유할 수 있는 방법을 제공합니다[3][7].

Citations:
[1] https://f-lab.kr/insight/understanding-and-utilizing-threadlocal
[2] https://modimodi.tistory.com/54
[3] https://0soo.tistory.com/m/260
[4] https://blog.neonkid.xyz/316
[5] https://yoonseon.tistory.com/151
[6] https://hoding-cloud.tistory.com/4
[7] https://mangkyu.tistory.com/317
[8] https://velog.io/@suhongkim98/%EC%9E%90%EB%B0%94-%EC%96%B8%EC%96%B4%EC%9D%98-%ED%83%9C%EC%83%9D%EC%A0%81-%ED%95%9C%EA%B3%84%EC%99%80-%EA%B7%B9%EB%B3%B5%EC%9D%84-%EC%9C%84%ED%95%9C-%EB%B0%9C%EC%A0%84-%EA%B3%BC%EC%A0%95-%EC%8A%A4%EB%A0%88%EB%93%9C%ED%8E%B8

---

