---
description: 빠르게 배워보는 kotlin
---

# Kotlin

## Kotlin 사용 목적

서버를 위한 언어

* 서버단의 어플리케이션 개발에 좋은 언어
* 이미 있는 JAVA 기반 기술들과도 호환

Android가 지원하는 언어

* Android 이전 버전들과 호환 및 java로 개발한 앱 만큼 성능

Javascript

* Kotlin으로 작성한 코드를 JS로 변환시켜주는 기능 (Typescript처럼 가능하나, 사실 라이브러리 생태계가 그닥인듯..)

Native 지원 

* VM 없이 실행 가능한 Binary 파일 생성 가능 (LLVM 기반)

## Variables

변수 var 상수 val (한번 값을 입력하면 변경이 안되는 변수) 

타입은 변수명 : 뒤에 붙는다.

```
val a: Int = 1
val b = 2
val c :Int 
c = 3
var x = 5 
x = 1

//String
var v =1
var s1 = "a string"
var s2 = "$s1 and $v"
var s3 = """
abc
def
efg
"""
```

## function

fun 키워드를 사용하며 파라미터에는 변수명:타입을 쓰고 리턴타입을 지정

```
fun sum(a: Int, b:Int): Int{
    return a + b
}

fun sum(a:Int,b:Int) = a + b 
```

아무것도 리턴하지 않으면 Unit을 리턴 타입으로 사용하거나 생략한다.

```
fun sum(a:Int, b:Int): Unit{
    print("sum(${a},${b})=${a+b}")
}
```

## Program entry point

main 함수를 entry point로 가진다.

```
fun main(){
    println("Hello world")
}
```

## 조건문

```
fun maxOf(a:Int, b:Int) :Int{ 
    if(a > b){ 
    return a 
}else{ 
    return b 
    } 
} 

println(maxOf(5,2))
```

## NULL 키워드

Null 을 가질수 있는 타입은 반드시 명시적으로 표기가 되어야됨

```
fun parseInt(str:String) : Int?{ 
  return str.toIntOrNull() 
}

if(parseInt("A")==null) { 
  println("a is not a number") 
}
```

## Type 확인 (=typeOf)

is 연산자로 타입 확인

```
if(obj is String)
```

## Loop

### for in

```
val items = listOf("a","b","c")

for(item in items){ 
    println(item) 
}
    
for((index, value) in array.withIndex()){ 
    println("the element at $index is $value") 
}
```

### while

```
var index = 0
while(index < items.size){
    println("items[$index]=${items[index]}")
    index++
}

do{
    val y:Int? = null
    println("!")
}while( y != null) 

```

## when (=switch)

```
fun describe(x:Int) =
    when(x){
        1 -> println("x == 1")
        2 -> println("x == 2")
        3, 4 -> println("x == 3 or x == 4")
        else -> println("x is neither 1 nor 2")
    }
describe(5)

when(x){
    in 1..10 -> print("x is in the range")
    in validNumbers -> print("x is valid")
    !in 10..20 -> print("x is outside the range")
    else -> print("none of the above")
}

```

## Ranges

..키워드로 특정 범위의 값을 표현식에 사용

```
if (2 in 1..3){
    println("2 is in range")
}
(= 1 <= 2 && 2 <= 3)

for(x in 1..3){
    println(x)
}

for(x in 1..10 step 2){
    print(x)
}
-> 1,3,5,7,9
```

## Numbers 타입

* byte, short, Int, Long, Float, Double

```
val one = 1 // Int 
val threeBillion = 3000000000 // Long 
val oneLong = 1L // Long 
val oneByte: Byte = 1 
val pi = 3.14 // Double 
val e = 2.7182818284 // Double 
val eFloat = 2.7182818284f // Float, actual value is 2.7182817

//밑줄 표현
//숫자 사이에 밑줄을 써서 표현가능
val oneMillion = 1_000_000 

```

## Representation

\=== 는 참조하는 object를 비교하고 == 는 가지고 있는 value를 비교한다.(= java equals) 

Java에선 null을 가질 수 없는 숫자 변수는 primitive type으로 처리 

but Int?와 같이 null을 가질 수 있는 타입은 boxing 되어 처리된다. ==> 값은 같으나, 즉 주소값에 따라 다른 객체로 인식한다. (Integer 타입으로 변환) (Int와 Integer 타입의 차이)

```
val : a Int = 10000
val boxedInt : Int? = a
val anotherBoxedA: Int? = a
println(boxedInt === anotherBoxedA) // false
```

## Explicit conversions

작은 데이터 타입의 변수를 더 큰 데이터 타입으로 변환할수 없다. 

ex) Byte 변수의 값을 Int에 바로 대입할수 없다.

```
val a : Int? = 1
val b : Long? = a
print(a == b) // -- false

val b:Byte = 1
val i:Int = b.toInt()

* 모든 숫자 타입은 변환 함수가 존재
toByte(): Byte
toShort(): Short
toInt(): Int
toLong(): Long
toFloat(): Float
toDouble(): Double
toChar(): Char
```

## 소수점 비교

변수 a,b가 Float, Double로 타입이 명확하면, Floating point에 따른 연산 불명확하면, equals()와 compareTo()함수를 사용한다.

NaN is considered equal to itself:

NaN 이 NaN 자신과 동등한 것으로써 비교가 가능하다.

NaN is considered greater than any other element including POSITIVE_INFINITY:

NaN 은 POSITIVE_INFINITY 을 포함해 다른 어떤 것보다 크다. -0.0 is considered less than 0.0: -0.0 이 0.0 보다 작은것으로 간주된다.

```
fun compare(a:Any, b:Any){
    println(a == b)
}

fun main(){
    println(Double.NaN == Double.NaN)
    compare(Double.NaN == Double.NaN)
    println(-0.0 == 0.0)
    println(-0.0, 0.0)
}

```

## Character

문자는 char를 사용하고, 작은 따온표로 표시 당연히 숫자값이 직접 대입해서 쓸 수 없다. (JAVA와 다름 주의!) 

특수 문자는 \ 역 슬래쉬를 사용한다.

```
var a:Char = '1'
var b:Int = a.toInt()
```

## Booleans

* true / false
* nullable reference 일 때 boxed 된다.

## Arrays

생성 함수 arrayOf(), arrayOfNulls(), emptyArray() 

element 접근 : get(), set()

```
val a = arrayOf(1,2,3)
a.set(2,5) // 1,2,5

var b = arrayOfNulls<String>(3) //(size) [ , , ] == new int [3]
if(b[0] == null){
    println("b[0] is null")
}

```

Array 배열의 크기와 초기값 설정 함수

```
val a = Array<String>(3,{i -> i.toString()})
a.forEach{ print(it) } // 012
```

## primitive type Arrays

boxing overhead 없이 primitive type의 배열을 위한 클래스

ex) ByteArray, ShortArray, IntArray

```
val x : IntArray = intArrayOf(1,2,3)
x[0] = x[1] + x[2]

val arr = IntArray(5) // [0,0,0,0,0]
```

## if Expression

if else모양이지만 값이 리턴되는 표현식이 존재, if를 표현식으로 쓰려면, 모든 경우의 리턴값이 존재해야 하므로, else branch가 반드시 함께 사용되어야한다.

```
val max = if(a > b) a else b
val max = if(a > b){
    print("Choose a)"
    a
}else{
    print("Choose b)"
    b
}

```

## Kotlin의 jump expressions : return, break, continue

```
val s = person.name ? : return

Break and Continue Label

expression은 Label로 표시될 수 있으며 식별자(=@) 뒤에 붙인다.
ex) abc@

loop@ for(i in 1..10){
    for(j in 1..10){
        if(...) break@loop
    }
}

listOf(1,2,3,4,5).forEach{
        if(it == 3) return
        print(it)
    }
// 1,2

    listOf(1,2,3,4,5).forEach lit@{
        if(it == 3) return@lit
        print(it)
    }

// 1,2,4,5 

    listOf(1,2,3,4,5).forEach{
        if(it == 3) return@forEach
        print(it)
    }
    //1,2,4,5

    listOf(1,2,3,4,5).forEach(fun(value:Int){
        if(value == 3) return
        print(value)
    })
    //=> 익명함수를 사용하면 return은 자신의 함수만을 종료하게 되고 외부에는 영향을 끼치지 않는다.
    //1,2,4,5

```

## Kotlin 클래스의 프로퍼티와 필드

* property는 var 또는 val로 선언

![](<.gitbook/assets/image (1).png>)

* property_initializer는 property의 데이터 초기화 
* getter, setter는 데이터 변경, 접근을 위한 접근자
* 위 기능들은 생략이 가능하며, 암묵적으로 기본적인 기능이 구현된다. initalizer로 부터 property의 타입 추론이 가능한 경우 type을 생략 가능 => ex) var a = 1

## Late-initalized Propeties

보통의 property는 constructor에서 null이 아닌 어떤 값으로 초기화되어야됨 바로 초기화를 하지 않고, 특정 조건이 만족한 후 초기화를 하고 싶은 경우, lateinit을 사용

클래스 body안에서 var 에서 사용할수 있고, top-level properties와 지역변수에 쓸수 있음. 변수는 null이 될수 없고, promitive type이 아니여야한다. primary constructor 안에서 사용할 수 없으며,custom 생성자를 쓰지 않는 경우에만 사용 가능

```
public class MyTest{
    lateinit var subject:TestSubject

    @Setup fun setup(){
        subject = TestSubject()
    }

    @Test fun test(){
        subject.method()
    }
}

* 초기화 여부 확인 
if(foo::bar.isInitialized){
    println(foo.bar)
}
```

## Interface

추상화 메소드와 구현코드를 가지고 있고, state를 저장할 수 없다. 한개의 클래스나 오브젝트는 한개 이상의 인터페이스를 가질수있음

인터페이스는 property를 가질 수 있으나, 추상화하거나 접근자를 구현해야한다. state를 지정할 수 없는 속성 때문에 property는 backing fields를 가질수 없으므로, interface 내의 접근자들 또한 property를 참조할 수 없다.

```
interface Named{
    val name : String
}

inteface Person : Named{
    val firstName: String
    val lastName : String

    override val name : String get() = "$firstName $lastName"
}

data class Employee{
    override val firstName: String,
    override val lastName: String,
    val position: Position
} : Person

```

## Visibility Modifer

private, protected, internal, public

* public은 모든 곳에서 접근 가능 
* private 선언한 파일 내에서만 접근 가능 
* internal 같은 모듈 안 어디에서나 접근 가능 
* protected 최상위 선언이 불가능 다른 패키지로부터 최상위 선언들을 사용하려면 import를 해야한다.

## Extension

kotlin의 클래스는 상속이나 디자인 패턴인 Decorator를 통한 기능확장 외의 방법인 extension을 제공 수정이 불가능한 3rd party 라이브러리 클래스에 함수 추가 가능

```
fun MutableList<Int>.swap(index1:Int,index2:Int){
    val tmp = this[index1]
    this[index1] = this[index2]
    this[index2] = tmp
}

val list = MutableListOf(1,2,3)
list.swap(0,2)

```

확장 함수가 클래스의 멤버함수를 overload 하는 것은 가능하다.

```
class Example{
    fun printFunctionalType(){
        println("Class Method")
    }
}

fun Example.printFunctionalType(i:Int){
    println("$i Extension function")
}

Example().printFunctionalType(1)

```

extionsion은 nullable receiver type으로 정의 가능 = object의 값이 null일 수도 있고 함수에서 this == null처럼 체크가 가능

```
fun Any?.toString():String{
    if(this == null) return "null"
    return toString()
}
```

## Data class

데이터를 저장하기 위한 클래스

컴파일러가 자동으로 primary constructor의 properties로부터 아래의 멤버 생성

* equals() / hashcode()
* "User(name=john, age=42)" 폼의 toString()
* copy()

data class가 파라미터가 없는 constructor가 필요하면 모든 프로퍼티에 대한 기본값이 설정되어야한다.

특정 property를 제외하려면 클래스 내부에 선언하면 된다.

```
data class Person(val name:String){
    var age: Int = 0
}

//Destructuring Declarations
val jane = User("Jane", 35)
val (name, age) = jane
```

















