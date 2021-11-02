---
description: Kotlin Study 2주차
---

# Kotlin Study 2

## Extension

kotlin은 확장이라는 기능을 이용해 간단한게 객체의 함수나 프로머티를 임의로 확장 정의 할수 있다.&#x20;

* 유틸리티 클래스 등을 별도로 지정하지 않고, 직접적인 객체 확장의 방법 제공
* 함수와 프로퍼티 양측에 대한 확장을 지원
* Generic을 통해 객체의 타입을 처리 가능
* Extension이 적용될 범위를 지정가능

### Extension function의 예시

#### kotlin

```kotlin
// Some code
fun String.hello() : String{
    return "Hello, $this"
}


fun main() {
    val whom = "songmyeongjin"
    println(whom.hello())
}
```

#### Java

```java
// Some code
public final class MainKt {
   @NotNull
   public static final String hello(@NotNull String $this$hello) {
      Intrinsics.checkNotNullParameter($this$hello, "$this$hello");
      return "Hello, " + $this$hello;
   }

   public static final void main() {
      String whom = "songmyeongjin";
      String var1 = hello(whom);
      boolean var2 = false;
      System.out.println(var1);
   }

   // $FF: synthetic method
   public static void main(String[] var0) {
      main();
   }
}
ㄷ
```

### Extension은 정적으로 처리된다.

확장 대상인 String을 인자로 받는 static final로 메서드가 생성됨. 이는 클래스 자체가 확장된 것이 아닌, 정적인 메소드 형태로 코드가 생성되었으므로, 객체 멤버 접근에 제한 접근이 존재할수 있음

실제로 클래스를 상속/수정 하는 것이 아닌, 클래스에 새 멤버를 삽입하지 않고 단순히 해당 타입의 변수에 .을 기반으로 호출가능한 함수를 생성&#x20;

```kotlin
// Some code

open class C

class D: C()

fun C.foo() = "c"
fun D.foo() = "d"

fun printFoo(c:C){
    println(c.foo())
}

fun main() {
    printFoo(D())
}


```

```kotlin
// Some code

public final class MainKt {
   @NotNull
   public static final String foo(@NotNull C $this$foo) {
      Intrinsics.checkNotNullParameter($this$foo, "$this$foo");
      return "c";
   }

   @NotNull
   public static final String foo(@NotNull D $this$foo) {
      Intrinsics.checkNotNullParameter($this$foo, "$this$foo");
      return "d";
   }

   public static final void printFoo(@NotNull C c) {
      Intrinsics.checkNotNullParameter(c, "c");
      String var1 = foo(c);
      boolean var2 = false;
      System.out.println(var1);
   }

   public static final void main() {
      printFoo((C)(new D()));
   }

   // $FF: synthetic method
   public static void main(String[] var0) {
      main();
   }E
```

### Extension은 멤버가 우선이다.

동일한 시그니처를 가지는 멤버 함수가 존재한다면 언제나 멤버가 호출된다. 멤버가 Extensions에 대해 항상 우선권을 가진다.

```kotlin
// Some code
class Person{
    fun hello() {
        println("Hello!")
    }
}

fun Person.hello(){
    println("world!")
}

fun main() {
    Person().hello()
}

class Person{
    fun hell() {
        println("Hello!")
    }
}

fun Person.hello(){
    println("world!")
}

fun main() {
    Person().hello()
}

```

```java
// Some code
public final class MainKt {
   public static final void hello(@NotNull Person $this$hello) {
      Intrinsics.checkNotNullParameter($this$hello, "$this$hello");
      String var1 = "world!";
      boolean var2 = false;
      System.out.println(var1);
   }

   public static final void main() {
      (new Person()).hello();
   }

   // $FF: synthetic method
   public static void main(String[] var0) {
      main();
   }
}
```

멤버가 Extension에 대해 항상 우선권을 가지기에 Complie시 이를 반영해서 컴파일된다. 별도로 에러가 발생하지 않기 때문에 조심해야함 (인텔리제이 상에선 우선순위가 있는것이 색깔이 들어옴)

![](<.gitbook/assets/스크린샷 2021-11-02 오후 10.44.49.png>)

### Extension 역시 범위를 갖는다.

Extension을 클래스 멤버로 선언하면 해당 클래스 내에서만 범위가 결정. 클래스 멤버로 선언된 Extension은 당연히 자신이 선언된 클래스 멤버에 자유롭게 접근가능. 아래 예시에선 C클래스 내에 선언된 D.foo() extension의 경우 C클래스 내에서만 유효하다. (D().foo()는  따로 선언하면 안됨)

```kotlin
// Some code

class D{
    fun bar() {
        println("D.bar()")
    }
}

class C{
    fun baz(){
        println("C.bar()")
    }
    fun D.foo(){
        bar() // calls D.bar
        baz() // calls C.baz
    }

    fun caller(d:D){
        d.foo()
    }
}


fun main() {
    C().caller(D())
}




```

## Extensions 정의하기

![](<.gitbook/assets/스크린샷 2021-11-02 오후 11.00.54.png>)

fun 뒤에 Receiver type을 정의하고, Nullable이 필요하다면 ?을 추가한다. 그리고 Receiver type과 함수의 이름 사이에는 .으로 구분한다. Receiver type으로 받은 object는 this로 접근해 사용할수 있다.

```kotlin
inline fun String?.isNullOrBlank(): Int {
    if(this == null || this.isBlank()) return 1;
	return 0;
}

fun main() {
    val temp = ""
    println(temp.isNullOrBlank())
}
```

Higer Order functions에서도 동일하게 사용한다.&#x20;

```kotlin
// Some code
    val isNotEmpty:String ?.() -> Boolean ={
        this != null && this.isNotEmpty()
    }

    val temp = "111"
    println(temp.isNotEmpty())
```

