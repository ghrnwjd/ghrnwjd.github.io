---
layout: post
title: 자바 기초 [1]
tags:
  - 프로그래밍
---

![java roadmap](https://roadmap.sh/roadmaps/java.png)


## Basic Syntax
### Variable
변수의 종류는 `인스턴스 변수`, `클래스 변수`, `로컬 변수`, `매개 변수`로 나눌 수 있다.

1. 인스턴스 변수
	객체의 개별 상태를 저장하는 변수
2. 클래스 변수
	`static` 키워드로 선언된 필드를 말하며, 해당 변수는 정확히 하나만 존재한다는 것을 컴파일러에게 전달한다. 또한 `final` 키워드를 통하여 절대 변경되지 않는 정적 변수를 만들 수 있다.
3. 로컬 변수
4. 매개 변수

변수의 이름은 대소문자를 구별하며 변수의 이름은 소문자로 시작하여 여러개의 단어로 이루어진 경우 후속 단어의 시작은 대문자로 시작한다.

기본 변수 타입
1. short
	1. 크기: 2byte
	2. 기본값: 0
2. int, float
	1. 크기: 4byte
	2. 기본값: 0, 0.0f
3. long, double
	1. 크기: 8byte
	2. 기본값: 0L, 0.0d
4. boolean
	1. 기본값: false
5. char
	1. 크기: 2byte
	2. 기본값: \u0000
이 외 String도 지원하며(기본값 null) 불변 객체임으로 값을 변경할 수 없다.

***8가지 기본 데이터 유형***
`byte`, `short`, , `int`, `long`, `float`, `double`, `boolean`, `char`. `java.lang.String`

```java
boolean result = true;
char capitalC = 'C';
byte b = 100;
short s = 10000;
int i = 100000;
```

숫자의 표현으로는 10진수, 16진수, 2진수로 표현할 수 있다.
```java
// The number 26, in decimal
int decimalValue = 26;

//  The number 26, in hexadecimal
int hexadecimalValue = 0x1a;

// The number 26, in binary
int binaryValue = 0b11010;

double d1 = 123.4;

// same value as d1, but in scientific notation
double d2 = 1.234e2;
float f1  = 123.4f;
```

char와 string에는 모든 유니코드 문자를 포함할 수 있으며 탈출문자 `\b`, `\t`, `\n`, `\f`, `\r`, `\"`, `\'`, `\\`를 지원한다. 

### var 키워드
Java SE 10부터 var 키워드를 통해 로컬 변수를 선언할 수 있으며 컴파일러가 변수의 유형을 결정하게 된다.

```java
String message = "Hello world!";
Path path = Path.of("debug.log");
InputStream stream = Files.newInputStream(path);

var message = "Hello world!";
var path = Path.of("debug.log");
var stream = Files.newInputStream(path);
```

var 사용에 주의해야 할 점
1. You can only use it for _local variables_ declared in methods, constructors and initializer blocks.
	로컬 변수에만 사용할 수 있다.
2. `var` cannot be used for fields, nor for method or constructor parameters.
	메서드나 생성자 매개변수에는 사용할 수 없다.
3. The compiler must be able to choose a type when the variable is declared. Since `null` has no type, the variable must have an initializer.
	컴파일러에 의해 타입이 결정되기에 선언과 동시에 초기화 되어있어야 한다.(null 유형 X)

### 연산자
1. 할당 연산자 
	=
2. 산술 연산자

|연산자|설명|
|---|---|
|`+`|덧셈 연산자(문자열 연결에도 사용됨)|
|`-`|뺄셈 연산자|
|`*`|곱셈 연산자|
|`/`|나누기 연산자|
|`%`|나머지 연산자|

3. 단항 연산자

|연산자|설명|
|---|---|
|`+`|단항 플러스 연산자; 양수 값을 나타냅니다(이 연산자 없이도 숫자는 양수입니다)|
|`-`|단항 빼기 연산자; 표현식을 부정합니다.|
|`++`|증가 연산자; 값을 1씩 증가시킵니다.|
|`--`|감소 연산자; 값을 1만큼 감소시킵니다.|
|`!`|논리적 보수 연산자; 부울 값을 반전합니다.|

4. 동등, 관계 연산자

| 연산자  | 설명        |
| ---- | --------- |
| `==` | ~와 같다     |
| `!=` | 같지 않다     |
| `>`  | 보다 크다     |
| `>=` | 보다 크거나 같다 |
| `<`  | 보다 작음     |
| `<=` | 이하 또는 같음  |
5. 조건 연산자

| 연산자  | 설명      |
| ---- | ------- |
| `&&` | 조건부-AND |
| `\|` | 조건부-OR  |

7. 유형 비교 연산자
	instanceOf
8. 비트 연산자

| 연산자  | 설명      |
| ---- | ------- |
| `~`  | 비트 반전   |
| `<<` | 왼쪽 시프트  |
| `>>` | 오른쪽 시프트 |
| `\|` | OR      |
| `&`  | AND     |
| `^`  | XOR     |
### 시간 API
사용할 수 있는 JAVA 8 이전 라이브러리
1. java.util.Date
2. java.util.Calendar
3. java.text.SimpleDateFormat
4. java.util.TimeZone

자바 8 이전 시간 관련 API에서는 실행 중인 서버의 시간대를 선택하기에 다른 시간대에 호스팅된 경우 시간의 문제가 발생할 수 있다. 이에 자바 8에서 새로운 API가 제공되었다.

1. java.time.LocalDate
2. java.time.LocalTime
3. java.time.LocalDateTime
4. 등등

```java
LocalDate today = LocalDate.now();  
System.out.println("Today : "+today);  
  
LocalTime thisTime = LocalTime.now();  
System.out.println("This time : "+thisTime);  
  
LocalDateTime currentDateTime = LocalDateTime.now();  
System.out.println("Current Time : "+currentDateTime);  
  
LocalDate someDay = LocalDate.of(2025, Month.JANUARY, 13);  
System.out.println("Someday : "+someDay);  
  
LocalTime someTime = LocalTime.of(23, 53);  
System.out.println("Sometime : "+someTime);  
  
LocalDateTime otherDateTime = LocalDateTime.of(2025, Month.JANUARY, 13, 10, 51, 44);  
System.out.println("Other Date Time : "+otherDateTime);
```

출력결과
```
Today : 2025-01-13
This time : 17:08:26.508184
Current Time : 2025-01-13T17:08:26.508257
Someday : 2025-01-13
Sometime : 23:53
Other Date Time : 2025-01-13T10:51:44
```

시간 연산의 관한 API

1. plusX()
2. minusX()
3. isBefore(), isAfter()

```java
LocalDateTime currentDateTime = LocalDateTime.now();  
LocalDateTime futureDateTime = currentDateTime  
        .plusYears(1)   
.plusDays(2)  
        .plusHours(3);  
  
boolean isBefore = futureDateTime.isBefore(currentDateTime); //false  
boolean isAfter = futureDateTime.isAfter(currentDateTime); //true
```

