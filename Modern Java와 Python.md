# Modern Java와 Python
## 모던 자바란? 함수형 프로그래밍 도입으로 큰 변화가 있었던 Java8 이후

## 오늘 다룰 내용
```
함수형과 스트림
고수준 병렬/동시성 지원
And more....
그리고 파이썬
```

### Java의 간략한 역사
1996년 1월 23에 Java1 출시 -> 당시에는 느리다는 비판(1.2, 1.3 버전에서 JIT Compiler, HotSpot JVM 도입으로 많이 해결)
국내에서는 Java5(2004)부터 많이 활용
6, 7은 큰 변화가 없었음
8에서 큰 변화
2022년 현재 최신 버전은 Java18

### 모던 자바의 특징
```
함수형 패러다임 도입
쉬운 동시성(병렬처리) 도입
모듈성 강화
개발자 편의 API 추가
```
**변화의 원인은?**
```
세상의 변화, 개발자의 요구 반영, 파생언어/라이브러리의 장점 수용, 어른의 사정? 등
```

#### 예제 1
** 음양 더하기 **
자연수 배열과 부호 배열의 SumProduct 구하기
ex) func({1, 2, 3}), {T, F, T}) = (1*1) + (2*-1) + (3*1) = 2

** 옛날 자바 **
```
int answer = 0;
for (int i=0; i<sign.length; i++)
	answer += absolutes[i] + (signs[i]? 1: -1);
return answer;
```
** 모던 자바 **
```
return IntStream.range(0, absolutes.length)
	.map(i -> absolutes[i] * (signs[i]? 1: -1))
	.reduce(0, Integer::sum);
```

#### 예제 2
** 문자열 리스트에서 길이가 5~10인 것만 대문자로 출력 **
** 단 반복문, 조건문 없이... **

** 옛날 자바 **
```
안 될 듯?
```
** 모던 자바 **
```
list.stream().filter(s -> s.length() >= 5 && s.length() <= 10)
	.map(s-> s.toUpperCase()).forEach(System.out::println);
```

### 함수형
** OOP라는 특징에 맞게, 객체지향적으로 함수를 도입했다! **
완전한 함수라고 보기에는 어려움~
```
함수를 일급 시민(First Class Citizen)에 포함 -> 사실은...
익명 클래스의 번거로움을 람다로 간편하게, 메서드 참조로 재사용
코드 블록을 주입(동작 파라미터화)하고 조합(Pipeline)할 수 있게 됨.
스트림의 기반, 병렬처리와 조화
```
** 주요 패키지, 클래스
```
@FunctionalInterface
java.util.function
Consumer, Supplier, Function, Predicate...
Operator
기본형 Int, Long, Double
```

#### 함수형_람다, 메서드 참조
```
람다(lambda) = 익명 함수, 익명 클래스를 대체
함수형 인터페이스(이름 있는 람다) : 하나의 추상 메서드를 가진 인터페이스
메서드 참조 : 메서드나 생성자를 참조하기(::)
```
ex) 문자열 리스트를 길이에 따라 정렬)
```
Collections.sort(words, new Comparator<String>() {
	public int compare(String o1, String o2) {
		return Integer.compare(o1.length(), o2.length());
```
```
- Collection.sort(words, (o1, o2) -> Integer.compare(o1.length(), o2.length()));
- Collection.sort(words, Comparator.comparingInt(String::length));
- words.sort(Comparator.comparingInt(String::length));
```

#### 함수형_Use Case
** 공식 문서 : "함수형은 이런 데다 쓰세요~" **
주요 인터페이스와 용례: 함수형이 적합한 곳 vs 아닌 곳
```
predicate : test 메서드, true, false 반환
- 조합 : and, or, not, negate
- Use case : 필터, 실행 결과만 확인할 때, stream.filter
```
```
Function : apply 메서드, 인자와 리턴 존재, 여러 용도로 사용
- 조합 : andThen, compose
- Use Case : 범용적, stream.map
```
```
Consumer : accept 메서드, 리턴 없음, 최종 소비자일 때
- 조합 : andThen
- Use Case : 메세지 소비자, 처리기, stream.forEach, peek
```
```
Supplier : get 메서드, 인자 없음(리턴만)
- Use Case : 메세지 생산, 조회, 실행 지연, 의존성 주입, stream.collect, generate
```

### 스트림
** 컬렉션 + 함수형, 데이터 처리 연산을 지원하도록 소스에서 추출된 연속된 요소 **
```
외부 순환(for, while) vs 내부 순환(VM아 이것 좀 해조)
SQL처럼 선언형 스타일로 데이터를 처리
쉽게 병렬처리 적용 : parallelStream 메서드
```
** 주요 패키지, 클래스, 메서드 **
```
java.util.stream
BaseStream, Stream
map(), filter(), reduce(), min()
C.stream(), C.parallelStream()
```

#### 스트림_주요 개념
```
중간 연산과 최종 연산
- 중간 연산은 스트림을 반환, 여러 연산을 조합할 수 있음
- 최종 연산을 스트림을 모두 소비하고 닫음
- 스트림은 1회용(최종 연산 이후 사용 불가)
```
#### 스트림_예제
** 직원 리스트 -> 부서별 직원 리스트 **
```
Map<Department, List<Empliyee>> byDept = employees.stream()
	.collect(Collectors.groupingBy(Employee::getDepartment));
```
** 직원 리스트 -> 부서별 급여 합계 **
```
Map<Department, Integer> totalByDept = employees.stream()
	.collect(Collectors.groupingBy(Employee::getDepartment,
	Collectors.summingInt(Employee::getSalary)));
```
** 좋은 직원, 안 좋은 직원 나누기 **
```
Map<Boolean, List<Employee>> byGood = employees.stream()
	.collect(Collectors.partitioningBy(Employee::isGood));
```

### 한편 파이썬은...
```
원래 함수형(v1.0, since 1994)
내장 컬렉션 = 리스트, 맵(딕셔너리), 튜플, 세트, ...
lambda, itertools, functools, generator
```

### 병렬/동시성_concurrent
```
저수준 병렬 처리의 어려움 : Thread, Lock, synchronized, ..
안전하고 쉬운 병렬처리 방법 제공 -> 마법은 아니야~
- 많이 사용되는 패턴들을 언어 차원에서 API로 지원
- 고수준, 추상화, Thread Safety, 비동기 지원
```
주요 패키지, 클래스
```
java.util, concurrent
Executor(s), ExecutorService
xxThreadPool, ForkJoinPool
Future, CompletableFucture
Runnable, Callable
```

#### Executor / Service / Etc
```
Thread를 직접 생성, 관리하지 않고 ExecutorService에서 스레드 관리
작업(Runnable, Callable)을 Executor 서비스에 요청하고 결과 받기
작업 스케쥴링(cron, at) 기능 : ScheduledExecutorService
Concurrent Collection
- 스레드 안전한 List / Map 제공
Atomic Variable
- 변수 자체가 원자성을 보장
Lock 객체
- 동기화 패턴에 따라 사용할 수 있는 유틸리티
```

### 비동기 지원_Async
** 동기는 7기, 비동기는 8기 아 ㅋㅋ**
```
동기 vs 비동기 and 블록(block) vs 넌블록(non-block)
작업이 끝날 때까지 기다리기 vs 하고 있어. 나중에 물어볼게.
Future : 비동기 연산 지원, 완료 확인/대기/결과 조회/취소
CompletableFuture : Future 작업 연결, 순서 정의 등
```

### 예제_스프링에서
** 여러 API 호출을 병렬로 실행 **
```
for(ApiService api : apis) {
	apiResults.add(api.callApi(param)):
}
for(CompletableFuture<void> future : apiResults) {
	future.get();
}
```
```
@Async
public CompletableFuture<void> callApi(String param) {
	/// ..api를 호출
	return new CompletableFuture<>();
}
```

### 한편 파이썬은
```
파이썬은 느려요~!? 일부만 맞는 이야기
GIL 문제: 스레드 활용을 제한하는 요소
multiprocessing
asyncio, conroutine
future, executors, ThreadPoolExecutor..
```


### 마지막으로_오늘 못 다룬 것들
```
Reactive(Flow API)
모듈화
Optional
타입 추론
컬렉션 API 개선
날짜와 시간 API 개선
Fork-Join 프레임 워크
Spliterator
인터페이스에 구현을 포함(static, default, private)
try-with resources(AutoCloseable)
http client
강화된 switch문
etc..
```

### 마지막으로2
** Java Support Tools **
```
- visualVM, Jconsole
- jps, jstack, jstat, jhat, jmap
- jshell
- flight recorder, jmx, Spring Actuator
```
** Python Tools **
```
-profile(cProfile), memory_profiler, vProf
```
