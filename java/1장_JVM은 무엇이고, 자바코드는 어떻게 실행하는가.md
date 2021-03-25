# 1장_JVM은 무엇이고, 자바 코드는 어떻게 실행하는가?

## 목표

자바 소스 파일(.java)을 JVM으로 실행하는 과정 이해하기.

## 목차

1. [자바가 컴파일 하는 방법](#1-자바가-컴파일-하는-방법)
2. [자바 바이트코드란 무엇인가](#2-자바-바이트코드란-무엇인가)
3. [자바 애플리케이션 실행하기](#3-자바-애플리케이션-실행하기)
4. [JVM이란 무엇인가](#4-JVM이란-무엇인가)
5. [JVM의 구성 이미지](#5-JVM의-구성-이미지)
6. [JVM 구성 요소가 하는 일](#6-JVM-구성-요소가-하는-일)
7. [JIT 컴파일러란 무엇이며 어떻게 동작하는가](#7-JIT-컴파일러란-무엇이며-어떻게-동작하는가)
8. [JDK와 JRE의 차이](#8-JDK와-JRE의-차이)
9. [Garbage Collection에 관하여](#9-Garbage-Collection에-관하여)

> Ref: [WhiteShip 라이브 스터디 1주차 과제](https://github.com/whiteship/live-study/issues/1)

## 1. 자바가 컴파일 하는 방법

### 자바 코드의 컴파일 순서

````
소스코드(.java) -> 어휘 분석 -> 구문 분석 -> 의미 분석 -> 중간코드 생성 후, 최적화
````

### 어휘 분석

어휘 분석에서는 키워드(public, class, ..), 리터럴("Hello World", ..), 연산자(+, ..)와 같은 어휘소를 수집한다. 그리고 이렇게 모인 어휘소를 하나의 스트림(**토큰 스트림**)으로 만든다.

### 구문 분석(Syntax Analysis)

구문 분석에서는 위의 **토큰 스트림**을 가지고, **문법 검사**를 진행한다. 

- 만약 여기서 문법 에러가 발생하면, `Syntax error (on token "~")` 이런 식의 메시지가 나타난다. 

### 의미 분석

의미 분석에서는 **타입 검사, 자동 타입 변환** 등을 진행한다. (만약 String 타입 변수에 Integer 값을 할당하면 에러가 발생한다.)

위의 어휘, 구문, 의미 분석이 모두 끝나면 마지막으로 **중간 코드**가 생성된다.<br>여기서 중간 코드란 JVM이 읽을 수 있는 언어로, **바이트코드**를 의미한다.

### 터미널에서 컴파일 하기

소스코드(.java)가 있는 곳으로 이동 후,  (컴파일하려면 당연히 소스코드가 있어야 한다.)

- `javac [소스코드 이름].java` 입력

- `javap`는 간단히 바이트코드(.class)를 사람이 이해할 수 있는 언어로 바꾸어주는 프로그램이다. (`javap`는 `역어셈블러`라고 부르고, `javap`를 이용한 결과물은 `자바 어셈블리`라고 부른다.)

  > `javap -c [이름].class` 에서 `-c` 옵션은 역어셈블된 `Code` 부분을 출력하게 한다.

  ```sh
  // 파일 목록 출력
  $ ls
  Mode                 LastWriteTime         Length Name
  ----                 -------------         ------ ----
  -a----      2021-03-17   오후 9:37            122 Hello.java
  
  // 컴파일 진행
  $ javac Hello.java
  
  // 다시 파일 목록 출력
  $ ls
  Mode                 LastWriteTime         Length Name
  ----                 -------------         ------ ----
  -a----      2021-03-17   오후 9:38            414 Hello.class  // 자바 바이트코드가 생성됨
  -a----      2021-03-17   오후 9:37            122 Hello.java
  
  
  // javap(역어셈블) 명령 실행
  $ javap -c .\Hello.class  
  Compiled from "Hello.java"
  public class Hello {
    public Hello();
      Code:
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: return
  
    public static void main(java.lang.String[]);
      Code:
         0: getstatic     #2                  // Field java/lang/System.out:Ljava/io/PrintStream;
         3: ldc           #3                  // String Hello Java
         5: invokevirtual #4                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
         8: return
  }
  ```

- 참고로, IntelliJ 등의 IDE에서는 알아서 컴파일 과정이 진행된다. 잘못된 문법을 입력했을 때 빨간 줄 또는 빨간 표시가 뜬다면 컴파일이 진행되어 컴파일 에러가 발생한 것이다.

> [Reference](https://catch-me-java.tistory.com/9?category=438116)

## 2. 자바 바이트코드란 무엇인가

### 자바 바이트코드

- **JVM이 이해할 수 있는 코드**로,  **`.class` 파일에 들어 있는 코드**이다.

  > 바이트코드는 **JVM의 인터프리터**를 거쳐야 한다. **기계어가 아니기 때문에** **OS에서 바로 실행되지 않는다**. 

- **바이트코드 명령어 OpCode**들은 **1바이트**의 바이트 번호로 표현된다. (그래서 `바이트`코드..)<br>
  (예, aload_0 = 0x2a, getfield = 0xb4, invokevirtual = 0xb6)<br>1바이트로 표현되는 자바 바이트코드 명령어 OpCode는 **최대 256개**가 된다.

### 왜 바이트코드를 사용(변환)하는가

- 바이트코드로 변환하는 이유는 

  - 우선, JVM을 거치기 때문에, **특정 하드웨어에 대한 의존성을 줄일 수 있다**.
  - 그리고 작성한 코드를 1차적으로 숨길 수 있다.
  - 바이트코드로 일단 바꾸어놓으면 이후 문법 검사가 없기 때문에 시간을 단축시킬 수 있다. (단 다른 종류의 구문 분석은 남아있다.) 

- 바이트코드의 역할

  - 클래스 파일 생성

    프로그램 분석

  - 코드에서 버그 찾는 툴, 코드 복잡도 계산

  - 프록시, 특정 API 호출 접근 제한, 스칼라 같은 언어의 컴파일러 만들기

> [Reference](https://catch-me-java.tistory.com/10?category=438116)

## 3. 자바 애플리케이션 실행하기

### 실행 순서

```
(컴파일 작업 이후)
자바 애플리케이션 시작 
-> JRE 동작 
-> public static void main(String[] args) 클래스 로딩 
-> main() 메소드 호출 
```

`JDK`의 `javac`를 통해 바이트코드가 생성된 후, 이 바이트코드는 `JRE`에게 간다. 
그리고 `JRE`를 통해 바이트코드를 `java` 명령어로 실행하게 되는데, 이때 **`JVM`**이 '실행' 단계를 걸친다. 

> JVM이 무엇인지는 [다음 목차]()에서 살펴보자. <br>
> ( + JDK, JRE는 [JDK와 JRE의 차이]() 목차를 참고하자.)

### 터미널에서 실행하기

```
// 자바 소스코드 내용 출력
$ cat Hello.java 
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello Java");
    }
}

// java 명령어 실행
$ java Hello  
Hello Java
```

- IntelliJ 등 IDE에서는 그냥 `run`하면 된다. 

## 4. JVM이란 무엇인가

### JVM 

>  Java Virtual Machine, 자바 가상 기계

- 말그대로, **자바 기술을 활용한 애플리케이션을 실행시키는 가상 기계**를 뜻한다. 모든 자바 애플리케이션은 이 JVM 위에서 동작한다. (사실 Java 뿐 아니라 Kotlin, Scala 등도 JVM 위에서 작동한다.)
- JVM은 **특정 OS에 맞게 자바 언어를 변환한다**. (`Interpreter`와 `JIT Compiler`가 기계어로 변환해준다.)<br> 결국 특정 OS와 마주하는 건 JVM이기 때문에, **자바는 운영체제에 독립적**이고, **JVM은 운영체제에 종속적**이다. 
- 다른 애플리케이션과 달리 자바는 JVM을 한 번 더 거쳐야 하기 때문에 속도가 느리다고 하는데, 요즘에는 기술이 발전했기 때문에 꼭 그렇지 않다.
- `Oracle`에서 JVM의 명세(스펙)을 정의하면, 공급업체(vendor)들이 실제 그래픽카드처럼 JVM 실물을 만들어 배포한다. 

### JVM의 등장 배경

- C/C++처럼 크로스 컴파일(`타겟 플랫폼에 맞춰 컴파일`)해서 배포하면 되는데, 굳이 왜 JVM을?

> 우선 읽자.  **WORA** ( \*Write Once Run Anywhere\* )

- 자바는 **네트워크에 연결된 모든 디바이스에서 작동하는 것이 목적**이었다.

- 하지만 디바이스마다 운영체제나 하드웨어가 달랐기 때문에, **플랫폼에 의존하지 않는** 언어가 설계되었다.

  그리고 Java Bytecode, JVM 또한 등장하게 되었다.

> Reference :  [[10분 테코톡] 무민의 JVM Stack & Heap - 우아한Tech 채널](https://www.youtube.com/watch?v=UzaGOXKVhwU)

## 5. JVM의 구성 이미지

<img src="https://images.velog.io/images/bky373/post/50e994f6-f444-4a9d-a732-f01e6e767f90/image.png" style="zoom: 40%;" />	

<img src="https://images.velog.io/images/bky373/post/53d20bcd-9987-49a7-9f43-00dcde6b1a7a/image.png" style="zoom: 40%;" />

<img src="https://images.velog.io/images/bky373/post/8a32487e-921c-4d39-ba3e-8684ed8e2840/image.png" style="zoom: 40%;" />

> Reference : [The JVM Architecture Explained - DZone Java](https://dzone.com/articles/jvm-architecture-explained) 

JVM은 크게

1. **클래스 로더**(Class Loader)

2. **런타임 데이터 영역**(Runtime Data Area)

3. **실행 엔진**(Excecution Engine)으로 나뉜다.

## 6. JVM 구성 요소가 하는 일

클래스 로더(Class Loader)가 자바 바이트코드를 런타임 데이터 영역(Runtime Data Areas)에 로드하고, 실행 엔진(Execution Engine)이 자바 바이트코드를 실행한다.

> 이하 각 구성 요소의 내용은 [Naver D2 - JVM Internal](https://d2.naver.com/helloworld/1230)의 내용을 상당 부분 참조하였다.

### 1. 클래스 로더

클래스 로더 작업은 **런타임** 때 **처음**으로 **클래스**를 **참조**할 때 발생한다. 클래스 로드를 요청받으면, 클래스 로더 캐시, 상위 클래스 로더, 자기 자신의 순서로 해당 클래스가 있는지 확인한다. 부트스트랩 클래스 로더까지 확인해도 없으면, 요청받은 클래스 로더가 파일 시스템에서 해당 클래스를 찾는다.

1. 부트스트랩 클래스 로더(Boostrap Class Loader) : JVM을 가동할 때 생성되며, Object 클래스들을 비롯한 자바 API들을 로드한다. 
2. 익스텐션 클래스 로더(Extension Class Loader) : 기본 자바 API를 제외한 확장 클래스들을 로드한다. (자바 9부터 플랫폼 클래스 로더로 이름 변경)
3. 시스템 클래스 로더(System Class Loader) : 위의 두 로더가 JVM 자체의 구성 요소들을 로드하는 거라면, 시트템 클래스 로더는 애플리케이션의 클래스들을 로드한다고 볼 수 있다. 사용자가 지정한 $CLASSPATH 내의 클래스들을 로드한다. (자바 9 이전엔 애플리케이션 클래스 로더)
4. 사용자 정의 클래스 로더(User-Defined Class Loader) : 애플리케이션 사용자가 직접 코드 상에서 생성해서 사용하는 클래스 로더이다.

클래스 로더가 아직 로드되지 않은 클래스를 발견하면, 아래 그림과 같은 과정을 거쳐 클래스를 로드, 링크, 초기화한다.

<img src="https://images.velog.io/images/bky373/post/deaad8d3-9004-4b27-9724-e7d743442721/image.png" style="zoom:67%;" />

> Reference : [Naver D2 - JVM Internal](https://d2.naver.com/helloworld/1230)

### 로드

클래스를 파일에서 가져와서 JVM 메모리에 로드한다.

> 로딩은 특정 이름을 가진 클래스 또는 인터페이스 유형의 이진 표현을 **찾고**(finding) 해당 이진 표현에서 클래스 또는 인터페이스를 **만드는**(creating) 프로세스이다.
>
> ( [Oracle Docs](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-5.html) ) 
>
> 특정 이름의 클래스 또는 인터페이스 유형을 나타내는 클래스 파일을 **찾아** 바이트 배열로 읽는 프로세스이다. 다음으로 바이트는 클래스 객체를 나타내고 올바른 메이저 버전과 마이너 버전이 있는지 확인하기 위해 구문 분석된다. 직접 수퍼 클래스로 명명된 모든 클래스 또는 인터페이스도 로드된다. 이 작업이 완료되면 이진 표현에서 클래스 또는 인터페이스 개체가 **생성**된다.
>
> ( [JVM Internals](https://blog.jamesdbloom.com/JVMInternals.html#dynamic_linking) )

### 링크

연결은 클래스 또는 인터페이스를 **가져와** 이들의 유형과 다이렉트 수퍼 클래스와 수퍼 인터페이스를 **검사**하고 **준비**하는 프로세스이다. 연결은 검사, 준비 및 선택적으로 **해결**하는 세 단계로 구성된다. ( [JVM Internals](https://blog.jamesdbloom.com/JVMInternals.html#dynamic_linking) )

1. 검증(Verifing) : 읽어들인 클래스가 자바 언어 명세(Java Language Specification) 및 JVM 명세에 명시된 대로 잘 구성되어 있는지 검사한다. 만약 검증에 실패하면, 검증 에러가 발생한다.

2. 준비(Preparing) : 클래스를 위한 메모리를 할당하고, 클래스에서 정의된 필드, 메소드, 인터페이스들을 나타내는 데이터 구조를 준비한다.

3. 분석/해결(Resolving) : 클래스의 상수 풀 내 모든 심볼릭 레퍼런스를 다이렉트 레퍼런스로 변경한다.

   > Dynamic Linking and Resolution과 관련해서 [여기](https://www.artima.com/insidejvm/ed2/linkmodP.html) 자세한 내용이 담겨있다.

### 초기화

클래스 변수들을 적절한 값으로 초기화한다. static initializer들을 수행하고, static 필드들을 **설정된 값으로 초기화**한다.

> 클래스 또는 인터페이스의 초기화는 클래스 또는 인터페이스 **초기화 메소드 실행으로 구성된다**. 
>
> ( [Oracle Docs]( https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-5.html) )

### 2. 런타임 데이터 영역

런타임 데이터 영역은 JVM이 운영체제 위에서 실행되면서 할당받는 메모리 영역이다. 이 영역은 크게 **5개**(런타임 상수 풀까지 6개)의 구성 요소를 갖는다.

<img src="https://images.velog.io/images/bky373/post/6886366f-5bc1-4e05-b6d3-f4dc82a4a5db/image.png" style="zoom:40%;" />

> 처음 JVM Architecture 그림과 동일하나, 달라진 부분이 하나 있다면, Runtime Constant Pool이 Method Area 내부에 추가되었다.<br> [Naver D2 - JVM Internal](https://d2.naver.com/helloworld/1230)의 그림 자료를 참고하였다. 

모든 스레드가 공유하는 영역들

1. 메소드 영역(Method Area): **클래스별 정보**가 저장된다. 

   - 클래스 로더 레퍼런스(Class Loader Reference)
   - 런타임 상수 풀(Runtime Constant Pool)
   - 필드 데이터(필드별 이름, 타입 등)
   - 메소드 데이터(메소드별 이름, 리턴 타입, 매개변수 타입 등)
   - 메소드 코드(메소드별 바이트코드, 지역 변수 테이블 등) 등을 저장하고 있다. JVM이 시작될 때 생성된다.

2. 런타임 상수 풀(Runtime Constant Pool): **심볼릭 레퍼런스**의 역할을 하는 곳이다. 메소드 영역 안에 위치한다. 

   - 숫자 리터럴(Numeric Literals)

   - 문자열 리터럴(String Literals)

   - 클래스 레퍼런스(Class References)

   - 필드 레퍼런스(Field References)

   - 메소드 레퍼런스(Method References)

     를 담고 있다. [클래스 파일 포맷](https://blog.jamesdbloom.com/JVMInternals.html#constant_pool)에서는 constant_pool 테이블에 해당하는 영역이다. 

   > 심볼릭 레퍼런스(Symbolic Reference) : 실제로 물리적 메모리 위치를 가리키는 참조가 아닌 논리적 참조를 의미한다. JVM 구현은 이 심볼릭 레퍼런스를 해석할 시기를 선택할 수 있다.

   ### 런타임 상수 풀 사용 예시

   ```java
   Object foo = new Object();
   ```

   ```sh
   // 위의 코드를 바이트코드로 나타내면,
   0: 	new #2 		    // Class java/lang/Object
   1:	dup
   2:	invokespecial #3    // Method java/ lang/Object "<init>"( ) V
   ```

   `new` opcode (피연산자 코드) 다음에는 `#2` 피연산자가 온다. 이 피연산자는 상수 풀에 대한 인덱스이므로 상수 풀의 두 번째 항목을 참조한다. 두 번째 항목은 클래스 참조이며, 이 항목은 상수 UTF8 문자열로 `// Class java / lang / Object` 값을 가지고 있는 상수 풀의 다른 항목을 참조한다. 그런 다음 이 심볼릭 링크를 사용하여 `java.lang.Object` 클래스를 검색한다. `new` opcode는 클래스 인스턴스를 만들고 변수를 초기화한다. 새로 만들어진 인스턴스의 레퍼런스는 피연산자 스택에 추가된다. 

   그런 다음 `dup` opcode가 수행된다. 피연산자 스택에 탑 레퍼런스의 복사본을 만들고, 피연산자 스택의 상단에 이를 추가한다. 

   마지막으로 `invokespecial`에 의해 `2행`에서 인스턴스 초기화 메소드가 호출된다. 이 피연산자 역시 상수 풀에 대한 참조를 포함한다. 초기화 메소드는 메소드에 대한 인수로 피연산자 풀의 탑 레퍼런스를 사용(팝)한다. 마지막에는 생성 및 초기화된 새 객체 레퍼런스 하나만 남게 된다.

3. 힙(Heap Area): **인스턴스 또는 객체**(클래스 타입이 선언된 것), **배열**을 저장하는 공간으로 **가비지 컬렉션의 대상**이다. **JVM 성능** 등의 이슈에서 가장 많이 언급되는 공간이다. 메모리를 여러 스레드에서 공유하기 때문에 데이터가 **스레드로부터 안전하지 않다**.

<br>

스레드마다 하나씩 생성되는 영역들

4. PC 레지스터(PC Register) : 현재 스레드의 메소드에서 실행되는 **명령어 (또는 OpCode)의 주소**를 갖는다. JVM은 PC를 통해 실행 중인 명령의 위치를 추적하고, 실제로 PC는 메소드 영역의 메모리 주소를 가리킨다. PC레지스터는 스레드가 시작될 때 생성된다. 

5. JVM 스택: 스레드가 시작될 때 생성되며, 메소드가 호출될 때마다 **스택 프레임(Stack Frame)**이 만들어진다. 스택 영역은 공유 리소스가 아니기 때문에 **스레드로부터 안전하다**. (스택 프레임에 대한 설명은 아래 참조)

6. 네이티브 메소드 스택(Native Method Stack): 자바가 아닌 언어로 작성된 **네이티브 코드**를 위한 스택이다. JNI(Java Native Interface)를 통해 호출하는 C/C++ 등의 코드를 수행하기 위한 스택으로 언어에 맞게 C 스택이나 C++ 스택이 생성된다.

### Stack Frame

JVM 내에서 메소드가 수행될 때마다 하나의 스택 프레임이 생성되어 해당 스레드의 JVM 스택에 추가되고, 메소드가 종료되면 스택 프레임이 제거된다. 
**스택 프레임**은 아래 **세 가지** 항목으로 나뉜다.

<img src="https://images.velog.io/images/bky373/post/ca16bc20-6143-4eed-801d-bfc68a179d69/image.png" style="zoom:67%;" />

> 그림 및 설명 Reference : [Naver D2 - JVM Internal](https://d2.naver.com/helloworld/1230) <br>예시 및 설명 Reference : [JVM stack과 frame 기계인간 John Grib](https://johngrib.github.io/wiki/jvm-stack/)

1. 지역 변수 배열(Local Variable Array) : **`this` 레퍼런스**, 메소드 실행 중에 사용되는 **모든 매개변수와 지역 변수**들이 저장되는 배열이다. 인덱스는 0부터 시작하는데 0은 메소드가 속한 `클래스 인스턴스`의 `this 레퍼런스`다. 1부터는 메소드에 전달된 `파라미터`들이 저장되며, 그 이후에는 메소드의 `지역 변수`들이 저장된다.

   ```java
   // 자바 코드
   class Test {
       public int hello(int a, double b, String c) {
           return 0;
       }
   }
   
   //-----------------------------------------------
   // 위에 자바 코드에서
   // 로컬 변수 배열이 아래와 같이 생성된다.
   
     +-----------+
   0 | reference | hidden this (always)
     +-----------+
   1 |    int    | int a
     +-----------+
   2 |  double   | double b
   3 |           |
     +-----------+
   4 | reference | String c
     +-----------+
         
   '''
   - reference는 heap의 레퍼런스를 의미한다.
   - primitive 타입은 값을 그냥 프레임에 저장한다.
         - 그래서 int, double이 Integer, Double보다 좀 더 빠르다.
   - double, long은 두 칸씩 차지한다.
   '''
   ```

2. 피연산자 스택(Operand Stack) : **메소드가 실제로 작업하는 공간**이다. 각 메소드는 피연산자 스택과 지역 변수 배열 사이에서 데이터를 교환하고, 다른 메소드 호출 결과를 추가하거나(push) 꺼낸다(pop). 

   ```java
   package main;
   
   // 4+3을 계산하는 자바 코드
   public class SumSample {
       public int sumOfAB() {
           int a = 4;
           int b = 3;
           return a + b;
       }
   }
   ```

   ```sh
   // 컴파일 후, 바이트코드를 출력하면 다음과 같다.
   $ javac SumSample.java
   $ javap -c SumSample.class
   Compiled from "SumSample.java"
   public class SumSample {
     public SumSample();
       Code:
          0: aload_0
          1: invokespecial #1            // Method java/lang/Object."<init>":()V
          4: return
   
     public int sumOfAB();
       Code:
          0: iconst_4
          1: istore_1
          2: iconst_3
          3: istore_2
          4: iload_1
          5: iload_2
          6: iadd
          7: ireturn
   }
   ```

   `public int sumOfAB()` 아래 `Code` 부분을 살펴보면,

   > 잠깐! 표를 읽기 전 아래 내용을 확인하자.
   >
   > - `LVA` : 지역 변수 배열 
   >
   > - `OS` : 오퍼랜드 스택

   | 바이트코드  | 작동                                   | 자바 코드  |
   | ----------- | -------------------------------------- | ---------- |
   | 0: iconst_4 | 상수 4를 OS에 push                     | 4          |
   | 1: istore_1 | pop하여 LVA의 1번 인덱스(int a)에 저장 | int a = 4  |
   | 2: iconst_3 | 상수 3을 OS에 push                     | 3          |
   | 3: istore_2 | pop하여 LVA의 2번 인덱스(int b)에 저장 | int b = 3  |
   | 4: iload_1  | LVA의 1번 인덱스의 값을 읽는다         | a          |
   | 5: iload_2  | LVA의 2번 인덱스의 값을 읽는다         | b          |
   | 6: iadd     | 읽은 두 값을 더한다                    | a+b        |
   | 7: ireturn  | 더한 값을 리턴한다                     | return a+b |

3. 프레임 데이터(Frame data): (런타임) **상수 풀 레퍼런스** 정보와, 메소드가 정상 종료되었을 때의 정보 또는 비정상 종료되었을 때 발생하는 **Exception 정보**를 저장한다. 

   - 상수 풀 레퍼런스(Constant Pool Reference)는 실행중인 메서드의 클래스에 대한 **상수 풀**을 가리킨다. 상수 뿐만 아니라 다른 클래스를 참조하거나, 메소드에 접근할 경우에도 상수 풀을 참조해야 한다.
   - 참고로, 상수 풀에 저장된 심볼릭 레퍼런스를 JVM에서 실제 접근할 수 있는 다이렉트 레퍼런스로 변경하는 것을 해결(Resolution)이라고 한다. 

   > Reference : [Runtime Data Area 구조](https://www.kdata.or.kr/info/info_04_view.html?field=&keyword=&type=techreport&page=19&dbnum=183668&mode=detail&type=techreport)

### 3. 실행 엔진 (Execution Engine)

**바이트코드**를 **실행**한다. 이때 바이트코드의 명령어는 1바이트의 **OpCode와 추가 피연산자**로 이루어지고, 실행 엔진은 하나의 OpCode를 가져와 피연산자와 함께 작업한 후, 그 다음 OpCode를 수행하는 식으로 동작한다.

바이트코드는 JVM은 이해할 수 있지만 아직 기계가 이해할 수 없는 코드다. 따라서 실행 엔진는 바이트코드를 기계가 실행할 수 있는 코드로 **변환**한다. 그리고 그 방식에는 다음 두 가지가 있다.

1. **인터프리터**: 바이트코드의 기본적인 실행 방식이다. 바이트코드 **명령어를 한 줄씩** 해석하고 실행한다. 하나씩 실행하기 때문에 하나하나의 해석은 빠르지만 인터프리팅 결과의 실행은 느리다.
2. **JIT 컴파일러**(Just-In-Time Compiler): 인터프리터의 단점을 보완하기 위해 도입되었다. 인터프리터 방식대로 실행하다가 적절한 때에 바이트코드 **전체를 컴파일**해서 네이티브 코드로 변경하고, 이후에는 변경된 메소드를 더 이상 인터프리팅하지 않고 **네이티브 코드로 직접 실행**한다. 네이티브 코드를 실행하는 것이 하나씩 인터프리팅하는 것보다 빠르고, 네이티브 코드는 **캐시**에 보관하기 때문에 한 번 컴파일된 코드는 계속 빠르게 수행된다.  

> Reference : [Naver D2 - JVM Internal](https://d2.naver.com/helloworld/1230)

## 7. JIT 컴파일러란 무엇이며 어떻게 동작하는가

- JIT의 기본적인 작동 방식은 바로 위의 **실행 엔진**을 참고하고 아래에서는 세부 구조를 살펴보자.

  ![](https://images.velog.io/images/bky373/post/65eafde2-5d57-46cb-ad66-c75701c0d09e/image.png)

  > Reference : [Naver D2 - JVM Internal](https://d2.naver.com/helloworld/1230)

- JIT 컴파일러는 바이트코드를 일단 중간 단계의 표현, **IR**(Intermediate Representation)로 변환하여 **최적화**(Optimization)를 수행하고 그 다음에 **네이티브 코드를 생성**(Generating Native Code)한다.

- 프로파일러(Profiler): 메소드가 여러 번 호출되는, 컴파일이 필요한 **핫스팟** 부분을 찾는 요소이다. 

- JIT 컴파일러가 컴파일하는 과정은 인터프리팅보다 훨씬 오래 걸리기 때문에, 만약 한 번만 실행되는 코드라면 컴파일하지 않고 인터프리팅하는 것이 훨씬 유리하다.

- JIT가 먼저 실행되고 인터프리터가 그 다음에 실행되는 게 아니라, 런타임 영역에서 일종의 스레드로 같이 실행된다. 그러다가 자주 사용되는 코드가 발견되면 위와 같이 동작한다.

- javac하고는 다르다. javac로 실행할 때랑 전혀 관련이 없다.

  ![](https://images.velog.io/images/bky373/post/dd8719bf-07d1-4aeb-b94e-3748a2a71b8b/image.png)

  

## 8. JDK와 JRE의 차이

> J'D'K > J'R'E > J'V'M

<img src="https://images.velog.io/images/bky373/post/1b1418ef-373c-480b-9fbc-bed4a59776a7/image.png" style="zoom:67%;" />

> [Reference](https://catch-me-java.tistory.com/11?category=438116)

### JDK

> Java **Development** Kit , 자바 **개발** 도구

- 자바 언어를 활용해 포로그램을**개발**할 때 필요하다. 
- **컴파일러, 역 어셈블러, 디버거**, 의존관계 분석 등 개발에 필요한 도구를 제공한다.

### JRE

> Java **Runtime** Environment, 자바 **실행** 환경

- 자바 언어로 된 프로그램을 **실행**할 때 필요하다.
- **자바 API와 JVM**으로 구성되며, **자바 애플리케이션**을 클래스 로더를 통해 읽어 들여 **자바 API와 함께 실행**한다. ( [참고](https://d2.naver.com/helloworld/1230) )
- 자바 9부터는 JRE가 JDK에 포함되었고, 자바 11부터는 JRE만 따로 제공하지 않는다.

## 9. Garbage Collection에 관하여

[이곳]을 참조

## Referneces

- [[자바 뉴스 라이브] 2020/11/14, 자바 스터디 1주차 - 백기선님 채널](https://www.youtube.com/watch?v=T7NyR5UvyYo&list=PLfI752FpVCS96fSsQe2E3HzYTgdmbz6LU&index=3)
- [자바 메모리 구조 뿌시기 [ JVM이란? ] - 이정록님 채널 ](https://www.youtube.com/watch?v=AWXPnMDZ9I0)
- [Naver D2 - JVM Internal](https://d2.naver.com/helloworld/1230)

- https://catch-me-java.tistory.com/11?category=438116
- [The JVM Architecture Explained - DZone Java](https://dzone.com/articles/jvm-architecture-explained)
- [Inside the Java Virtual Machine](https://www.artima.com/insidejvm/ed2/jvm8.html)
- [An Introduction to the Constant Pool in the JVM](https://www.baeldung.com/jvm-constant-pool)