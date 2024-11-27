# 운영 체제의 등장 배경 (feat. Operating System)

안녕하세요. yeTi입니다.
오늘은 운영 체제(Operating System, OS)가 태동하게 된 배경을 알아본 것을 공유해 보고자 합니다.

## 서론

운영 체제란 무엇일지, 왜 만들어지게 되었는지, Operating System 이라는 말이 어떤 것을 어떻게 하겠다는 뜻일지 궁금하여 이를 알아보면서 알아차린 것들을 공유하는 글입니다.

## 운영체제는 왜 필요하게 되었을까?

컴퓨터는 운영체제가 없어도 작동할 수 있지만 기능에 상당한 제약이 있습니다. 초기 컴퓨터인 에니악(ENIAC)처럼 운영체제가 없는 경우 정해진 계산만 수행할 수 있으며 새로운 기능을 추가하기 위해서는 하드웨어를 직접 변경해야 하는 제약사항이 있습니다. 그리고 프로그램을 메모리에 올리거나 여러 프로그램을 동시에 실행하는 것이 불가능하며 자원을 효율적으로 관리할 수 없습니다. 따라서 운영체제는 자원 할당, 보호, 사용자 인터페이스 제공 등의 역할을 통해 컴퓨터라는 하드웨어를 효율적으로 사용할 수 있게 해줍니다.

이러한 배경에서 운영 체제는 1950년대부터 발전해 왔으며 일괄처리 시스템, 시분할 시스템, 다중 프로그래밍 시스템 등을 거쳐 현대의 복잡하고 강력한 운영 체제로 진화해 왔습니다. 특히, 1960년대의 Multics 프로젝트와 Unix의 개발은 운영 체제의 개념을 확립하고 현대 운영 체제의 많은 기초를 마련했습니다.

## Unix의 등장

멀틱스(Multics) 프로젝트는 1960년대 중반에 MIT, General Electric, Bell Labs의 공동 연구 프로젝트로 시작되었습니다. 멀틱스는 "Multiplexed Information and Computing Service"의 약자로, 대규모 컴퓨터 유틸리티를 제공하는 것을 목표로 한 초기 시분할 운영체제입니다. 이 프로젝트의 주요 목표는 고가용성과 보안을 갖춘 시스템을 개발하여 여러 사용자가 동시에 컴퓨터 자원을 효율적으로 사용할 수 있도록 하는 것이었습니다. 멀틱스는 컴퓨터 과학에 많은 혁신적인 아이디어를 제공했으며, 이후 유닉스(Unix) 운영체제 개발에 큰 영향을 미쳤습니다.

Unix는 1969년 켄 톰슨(Ken Thompson)과 데니스 리치(Dennis Ritchie) 등이 벨 연구소에서 개발한 운영체제로, 멀틱스(Multics) 프로젝트의 복잡성과 비효율성을 개선하기 위해 만들어졌습니다. Unix는 간단하고 효율적인 운영체제를 목표로 하여 다중 사용자와 멀티태스킹 기능을 지원하며 C 언어로 작성되어 다양한 하드웨어 플랫폼에서 이식성과 호환성을 높였습니다. 이러한 특성 덕분에 Unix는 학계와 산업계에서 널리 채택되었습니다.

## Operating System의 어원

Operating System이라는 단어의 정확한 최초 사용자를 특정하기는 어렵습니다. 하지만 사용 사례를 통해 추정할 수 있습니다.

1955년경 General Motors(GM)에서 개발한 시스템이 초기 운영체제의 개념을 도입했습니다. 이 시스템은 나중에 "General Motors Operating System(GM OS)"이라고 불렸습니다. 이는 운영체제라는 개념이 처음으로 구체화된 사례 중 하나입니다.

1956년에는 GM과 North American Aviation(NAA)이 공동으로 GM-NAA I/O 시스템을 개발했는데, 이는 좀 더 현대적인 의미의 운영체제에 가까워졌습니다. 이 시스템은 배치 방식으로 작동하며, 입출력 장치들을 제어하는 루틴들을 라이브러리 형태로 갖추고 있었습니다.

1960년대 초반에 이르러 운영체제의 개념이 더욱 명확해졌고, 시분할 시스템과 멀티태스킹의 개념이 등장했습니다. 이 시기에 "Operating System"이라는 용어가 보다 널리 사용되기 시작했을 것으로 추정됩니다.

따라서 "Operating System"이라는 용어의 사용은 1950년대 중반부터 1960년대 초반 사이에 점진적으로 발전하고 보편화된 것으로 보입니다. 그러나 이 용어를 최초로 사용한 특정 개인을 명확히 지목하기는 어렵습니다.

## 결론

운영 체제(Operating System)의 태동 배경은 계산을 할 수 있는 하드웨어 자원을 다양한 어플리케이션들이 효율적으로 사용하고 다양한 사용자가 동시에 사용하고 싶은 욕망으로 인해 등장하게 되었다고 느껴집니다. 그러다보니 자연스럽게 하드웨어를 추상화하여 응용 프로그램들에게 편리한 인터페이스를 제공하는 것이 필요하였고, 다양한 응용 프로그램들이 등장하면서 쓰임새가 다양해지고 최종적으로 사용자 편의성을 높이려다보니 보니 GUI 환경이 필요해졌다고 생각합니다.

따라서 운영 체제(Operating System)란 하드웨어 리소스를 사용하는 응용 프로그램들을 사용자에게 효과적으로 제공하기 위해 조직하는 방식을 의미합니다.

## 참고 자료

- https://maxo.tistory.com/91
- https://thalals.tistory.com/304
- https://zzsza.github.io/development/2018/07/27/os-intro/
- https://hongong.hanbit.co.kr/%EC%BB%B4%ED%93%A8%ED%84%B0-%EA%B5%AC%EC%A1%B0%EC%99%80-%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C%EB%A5%BC-%EC%95%8C%EC%95%84%EC%95%BC-%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0/
- https://jitolit.tistory.com/11
- https://wikidocs.net/230919
- https://rebugs.tistory.com/308
- https://lovelyalien.tistory.com/24
- https://programmingjun.tistory.com/78
- https://woongsios.tistory.com/161
- https://namu.wiki/w/UNIX/%EC%97%AD%EC%82%AC
- https://velog.io/@ongsim123/Linux%EB%A6%AC%EB%88%85%EC%8A%A4%EC%9D%98-%EC%97%AD%EC%82%AC
- https://blog.naver.com/ggump/120013136473
- https://wormwlrm.github.io/2022/04/26/UNIX-A-History-and-a-Memoir.html
- https://ko.wikipedia.org/wiki/%EC%9A%B4%EC%98%81_%EC%B2%B4%EC%A0%9C
- https://donghwada.tistory.com/entry/OSOperating-System%EC%9D%98-%EC%97%AD%EC%82%AC
- https://courses.cs.washington.edu/courses/cse451/15wi/lectures/extra/MulticsDesign.pdf
- https://en.wikipedia.org/wiki/Multics
- https://web.mit.edu/multics-history/
- https://gunkies.org/wiki/Multics
- https://multicians.org/history.html
- https://en.namu.wiki/w/%EB%A9%80%ED%8B%B1%EC%8A%A4
- https://www.multicians.org/multics.html
- https://touslesjourscoding.tistory.com/9
- https://codinghentai.tistory.com/87
- http://www.tmaxcloud.com/blog/?bmode=view&idx=19858554
- https://kne-coding.tistory.com/180

