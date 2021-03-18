# JVM은 무엇이고, 자바 코드는 어떻게 실행하는가?

## 목표

자바 소스 파일(.java)을 JVM으로 실행하는 과정 이해하기.

## 학습할 것

1. 자바가 컴파일 하는 방법
2. 자바 바이트코드란 무엇인가
3. 자바 애플리케이션 실행하기
4. JVM이란 무엇인가

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
- 그리고 **JVM이 이해할 수 있는 형태로 번역된 코드**이다.
- 바이트코드 명령어 OpCode들은 **1바이트**의 바이트 번호로 표현된다. 
  (예, aload_0 = 0x2a, getfield = 0xb4, invokevirtual = 0xb6). 
  따라서, 1바이트로 표현되는 자바 바이트코드 명령어 OpCode는 **최대 256개**이다.

> 바이트코드는 **JVM의 인터프리터**를 거쳐야 한다. **기계어가 아니기 때문에** **OS에서 바로 실행되지 않는다**. 

### 왜 바이트코드를 사용하는가

- 바이트코드로 변환하는 이유는 
  - 우선 JVM을 거치므로, 특정 하드웨어에 대한 의존성을 줄인다.
  - 작성한 코드를 1차적으로 숨길 수 있다
  - 바이트코드로 일단 바꾸어놓으면 이후 문법 검사(구문 분석)를 하지 않아도 되므로(이전에 했기 때문에) 시간을 단축시킬 수 있다.
- 혹시나,, 바이트코드가 하는 일들을 추가로 넣어봤다.
  - 클래스 파일 생성
  - 프로그램 분석
  - 코드에서 버그 찾는 툴, 코드 복잡도 계산
  - 프록시, 특정 API 호출 접근 제한, 스칼라 같은 언어의 컴파일러 만들기

> [Reference](https://catch-me-java.tistory.com/10?category=438116)

## 3. 자바 애플리케이션 실행하기

### 실행 순서

```
자바 애플리케이션 시작 
-> JRE 동작 
-> public static void main(String[] args) 클래스 로딩 
-> main() 메소드 호출 
```

`JDK`의 `javac`를 통해 바이트코드가 생성된 후, 이 바이트코드는 `JRE`에게 간다. 
**`JRE`를 통해 바이트코드를 `java` 명령어로 실행**하게 되는데, **JVM이 '실행' 단계를 걸친다.** 

> **JVM**이 실행 단계를 걸친다고 했다. JVM이 무엇인지는 바로 [다음 목차]()에서 살펴보자.
> ( + JDK, JRE는 아래 [JDK와 JRE의 차이]()를 참고하자.)

### 터미널에서 실행하기

```
PS C:\Users\bobo\Desktop\java_tmp> cat .\Hello.java  // 소스코드 내용 출력 
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello Java");
    }
}

PS C:\Users\bobo\Desktop\java_tmp> java Hello  // java 명령어 실행
Hello Java
```

- IntelliJ 등 IDE에서는 그냥 `run`하면 된다. 

## 4. JVM이란 무엇인가

### JVM ( Java Virtual Machine, 자바 가상 기계 )

- 말그대로, **자바 기술을 활용한 애플리케이션을 실행시키는 가상 기계**를 의미한다. 모든 자바 애플리케이션이 이 JVM 위에서 동작한다. 
- **JVM은 자바 언어를 특정 OS에 맞게 변환해준다**. (`Interpreter`와 `JIT Compiler`가 기계어로 변환해준다.) 결국 특정 OS와 마주하는 건 자바가 아닌 JVM이기 때문에, **자바 자체는 운영체제에 독립적**이고, **JVM은 운영체제에 종속적**이다. 
- 다른 애플리케이션과 달리 자바는 JVM을 한 번 더 거쳐야 하기 때문에 속도가 느릴 수 있다고 하는데, 요즘에는 기술이 발전했기 때문에 꼭 그렇지만은 않다.
- `Oracle`에서 JVM의 스펙(명세)을 정의하면, 공급업체(vendor)들이 실제 그래픽카드처럼 JVM 실물을 만들어 배포한다고 한다. 

### 왜 JVM을 사용해야 하나 ? (등장 배경)

- c/c++ 도 크로스 컴파일해서 배포하면 되는데, 굳이 JVM을 사용해야 하나?

- 자바는 **네트워크에 연결된 모든 디ㄴ바이스에서 작동하는 것이 목적**이었다.

- 디바이스마다 운영체제나 하드웨어가 다르기 때문에, 자연스럽게 **플랫폼에 의존하지 않도록** 언어를 설계했다.

  > **WORA** ( \*Write Once Run Anywhere\* )

  그리하여 Java Bytecode, JVM을 사용하였다.

> Reference :
>
> - [[10분 테코톡] 무민의 JVM Stack & Heap - 우아한Tech 채널](