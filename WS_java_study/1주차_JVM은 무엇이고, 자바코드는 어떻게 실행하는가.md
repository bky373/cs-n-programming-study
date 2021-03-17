# 1) JVM은 무엇이고, 자바 코드는 어떻게 실행하는가?

## 목표

자바 소스 파일(.java)을 JVM으로 실행하는 과정 이해하기.

## 목차

1. 자바가 컴파일 하는 방법
2. 자바 바이트코드란 무엇인가

## 1. 자바가 컴파일 하는 방법

### 자바의 컴파일 순서

````
어휘 분석 -> 구문 분석 -> 의미 분석 -> 중간코드 생성 후, 최적화
````

### 어휘 분석

어휘 분석에서는

- **키워드**(예, public, class)
- **리터럴**(예, "Hello World")
- **연산자**(예, +)

와 같은 어휘소를 수집한다. 그리고 이렇게 모인 어휘소를 하나의 스트림(**토큰 스트림**)으로 만든다. 이 토큰 스트림까지 뽑아내는 과정을 어휘 분석이라고 한다.

### 구문 분석(Syntax Analysis)

구문 분석에서는 위의 토큰 스트림을 가지고, **문법 검사**를 진행한다. 

- 만약 여기서 문법이 틀려 에러가 발생하면, `Syntax error (on token "~")` 이런 식의 메시지가 나타난다. 

### 의미 분석

의미 분석에서는 **타입 검사, 자동 타입 변환** 등을 진행한다. 

- 만약 String 타입 변수에 Integer 값을 할당하면 여기서 에러가 발생한다.

결국, 위의 어휘, 구문, 의미 분석이 모두 끝나면 마지막으로 **중간 코드**가 생성된다. (여기서 중간 코드란 간단히 JVM이 읽을 수 있는 언어로, 다시 말해 **바이트코드**를 의미한다)

### 터미널에서 컴파일 하기

컴파일하길 원하는 소스코드(.java)가 있는 곳으로 이동 후,  (컴파일하려면 당연히 소스코드가 있어야 한다..)

- `javac [이름].java` 입력

- `javap`는 간단히 바이트코드(.class)를 사람이 이해할 수 있게 바꾸어주는 프로그램이라고 한다. 

  ```sh
  PS C:\Users\bobo\Desktop\java_tmp> ls
  
      디렉터리: C:\Users\bobo\Desktop\java_tmp
  
  Mode                 LastWriteTime         Length Name
  ----                 -------------         ------ ----
  -a----      2021-03-17   오후 9:37            122 Hello.java
  
  
  PS C:\Users\bobo\Desktop\java_tmp> javac Hello.java
  PS C:\Users\bobo\Desktop\java_tmp> ls
  
      디렉터리: C:\Users\bobo\Desktop\java_tmp
  
  Mode                 LastWriteTime         Length Name
  ----                 -------------         ------ ----
  -a----      2021-03-17   오후 9:38            414 Hello.class  // 자바 바이트코드가 생성됨
  -a----      2021-03-17   오후 9:37            122 Hello.java
  
  
  PS C:\Users\bobo\Desktop\java_tmp> javap Hello.class
  Compiled from "Hello.java"
  public class Hello {
    public Hello();
    public static void main(java.lang.String[]);
  }
  PS C:\Users\bobo\Desktop\java_tmp>
  ```

- IntelliJ 등 IDE에서는 알아서 컴파일 과정이 진행된다. 잘못된 문법을 입력했을 때 빨간 줄이 뜨는데, 그게 바로 컴파일 에러다.

> [Reference](https://catch-me-java.tistory.com/9?category=438116)

## 2. 자바 바이트코드란 무엇인가

- 쉽게, **`.class` 파일에 들어 있는 코드**이다.
- 그리고 이는 **JVM이 이해할 수 있는 형태로 번역된 코드**이다.

> 바이트코드는 **JVM의 인터프리터**를 거쳐야 한다. **기계어가 아니기 때문에** **OS에서 바로 실행되지 않는다**. 

### 왜 바이트코드를 사용하는가

- 바이트코드로 변환하는 이유는 작성한 코드를 1차적으로 숨기는 의미도 있고, 바이트코드로 이미 바꿨으니까 문법 검사(구문 분석)를 이후에 하지 않으면서 시간을 단축시킬 수 있기 때문이다. 또한 특정 하드웨어에 대한 의존성을 줄이는 이점도 있다.
- 혹시나,, 바이트코드가 하는 일들을 추가로 넣어봤다.
  - 클래스 파일 생성
  - 프로그램 분석
  - 코드에서 버그 찾는 툴, 코드 복잡도 계산
  - 프록시, 특정 API 호출 접근 제한, 스칼라 같은 언어의 컴파일러 만들기

> [Reference](https://catch-me-java.tistory.com/10?category=438116)