
# OWASP TOP 10, 시큐어 코딩 실습

## Learning Goals
```
1. 시큐어코딩과 OWASP TOP 10의 의미를 알 수 있고 코드에 적용할 수 있다.
2. 해시 함수에 대해서 알 수 있다.
3. 실무에서 쓰이는 유용한 팁과, 하면 안 되는 보안 위배 코딩과 해결에 대해서 알 수 있다.
```

## 시큐어코딩? 상용화하려면 필수!
**한국인터넷진흥원(KISA)에서 안전한 코드 인증**
**OWASP TOP 10**
```
SQL Injection -> 외부에서 들어오는 값은 검증하고 활용
```

## 시큐어코딩
**ID, PS를 4~12자, 대소문자 활용 이유**
```
의도치 않은 공격을 방지하기 위해 긴 문자열 금지, 브루트포싱 무차별 데이터 공격을 방지하기 위해 짧은 문자열 금지.
```
```
제약사항은 FE, BE에서 전부 구현 -> FE는 사용자를, BE는 시스템을 위한 것.
```

## Cryptography
### 암호화_복호화 가능
```
대칭키 암호화: AES, DES, ARIA, SEED...
```
```
비대칭키 암호화(공개키 암호화): RSA, ECC, EDCSA, DS(전자서명)
```
### 암호화_복호화 불가능(단방향 암호화)(해시 함수)
```
SHA256, keccak256, RIPEMD-160
```
```
복호화 불가능한 함수를 쓰는 이유: 검증
ex) 비밀번호 찾기의 경우 비밀번호를 새로 입력받는 경우
```

## SECURE CODING
1. 스트링 비교 시 string.equals("") 대신에 "".equals(string) 사용
ex) request.getCheckType().equals(anObject: "business"))
-> "business".equals(request.getCheckType())
-> Null Exception 방지

2. @RequestBody 바인딩 되는 부분을 JPA Entity 객체로 받으면 안 됨
ex) @RequestBody User request
-> @RequestBody Map<String, Object> request
String email = request.get(key: "email").toString();

## SUMMARY $ QUIZ
1. OWASP TOP 10의 취약점 1위로서 시스템 DB에 쿼리문을 주입하는 방법으로 SQL Injection이라고 한다.
2. 복호화 불가능(단방향) 암호화에 쓰이는 SHA256이나 keccak256를 해시함수라고 한다.
3. 평문 문자열 값을 해시암호화한 값은 salt에 따라서 결과가 달라질 수도 있다. (X)
4. 다음 중 변수 String 타입의 id 값이 null이어도 에러가 나지 않는 구문은?
"TESTID".equals(id)
5. 동형암호는 4세대 암호라 부르며 데이터를 암호화한 채로 연산할 수 있는 암호화 기법으로서, 평문을 암호화한 것에 연산을 한 결과와, 평문에 연산을 하여 암호화한 것이 같은 것을 말한다.
E(m1) + E(m2) = E(m1 + m2)
E(m1) * E(m2) = E(m1 * m2)

```
Q. 아스키코드로 특수문자 넣을 수 있는지?
A. 넣을 수 있음.
```
```
Q. 현업에서의 방어 기법은?
A. 소나큐브 등 정적 분석 툴을 활용하여 일정 점수 이상을 받지 못하면 빌드 불가.
```
