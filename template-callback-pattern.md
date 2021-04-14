---
description: 토비의 스프링 3.1 3장 정리
---

# Template Callback Pattern

## 들어가면서

가장 정형적인 템플릿/콜백 패턴의 후보는 try/catch/finally 블록을 사용하는 코드이다. 일정한 리소스를 만들거나 가져와 작업하면서, 예외가 발생할 가능성이 있는 코드는 보통 이 구조로 만들어짐 하지만 중첩된 구조로 될 경우엔, 가독성의 문제를 처리하고, 사이드 이펙트를 야기한다.

## 샘플 1 \(일반\)

파일을 하나 열어서 모든 라인의 숫자를 더한 합을 돌려주는 코드를 만드는 예시

### 샘플코드

```java
package com.company;

import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class Calculator {
    public Integer calcSum(String filePath) throws IOException{
        BufferedReader br = new BufferedReader(new FileReader(filePath));
        Integer sum = 0;
        String line = null;
        while((line = br.readLine())!= null){
            sum += Integer.valueOf(line);
        }
        br.close();
        return sum;
    }
}

```

### 테스트 코드 

```java
package com.company;

import org.junit.jupiter.api.Test;
import java.io.IOException;
import static org.hamcrest.CoreMatchers.is;
import static org.hamcrest.MatcherAssert.assertThat;


class CalculatorTest {
    @Test
    public void sumOfNumbers() throws IOException{
        Calculator calculator = new Calculator();
        int sum = calculator.calcSum("src/main/resources/numbers.txt");
        assertThat(sum,is(10));
    }
}
```

위의 코드를 살펴보면, 파일을 읽거나 처리하다가 예외가 발생하면, 파일이 정상적으로 닫히지 않고 메소드를 빠져나간다. 따라서 try/finally 블록을 적용해서 어떤 경우에라도 파일이 열렸으면, 반드시 닫아주도록 만들어줘야된다. 

## 샘플 2 \(try catch finally 적용\)

### 샘플코드 

```java
package com.company;

import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class Calculator {
    public Integer calcSum(String filePath) throws IOException{
        BufferedReader br = null;
        try{
            br = new BufferedReader(new FileReader(filePath));
            Integer sum = 0;
            String line = null;
            while((line = br.readLine()) != null){
                sum += Integer.valueOf(line);
            }
            return sum;
        }catch (IOException e){
            System.out.println(e.getMessage());
            throw e;
        }finally {
            if(br != null){
                try {
                    br.close();
                }
                catch (IOException e) {
                    System.out.println(e.getMessage());
                }
            }
        }
    }
}

```

만들어진 모든 리소스는 확실히 정리하고 빠져나오도록 만드는 것과 모든 예외상황에 대해 적절한 처리를 해줬으나, 만약 여기에 요구사항이 추가된다면? 어떻게 될까?

## 샘플 3 \(중복의 제거와 템플릿/콜백 설계\)

만약에 이번에는 파일에 있는 모든 숫자의 곱을 계산하는 기능을 추가해야한다는 요구가 생겼을때, 파일을 읽어서 처리하는 비슷한 기능이 새로 필요할때 마다 앞에서 만든 코드를 복사해서 만들어야할까? 

No! -&gt; 객체지향언어를 사용하고 객체지향 설계를 통해 코드를 작성하는 개발자의 기본적인 자세

### 템플릿/콜백 패턴의 설계

아래의 흐름대로 콜백 인터페이스를 정의해야한다. 

1. 템플릿에 담을 반복되는 작업 흐름이 무엇인지 살펴본다. 
2. 콜백이 템플릿에게 돌려줄 내용은 무엇인지 살펴본다.
3. 템플릿이 작업을 마친뒤  클라이언트에게 전달해야 할것은?

가장 쉽게 생각해볼 수 있는 구조는 **템플릿이 파일을 열고 각 라인을 읽어올수 있는 BufferedReader를 만들어서 콜백에게 전달** -&gt; 콜백이 각 라인을 읽어서 알아서 처리한 후 -&gt; 최종 결과만 템플릿에게 전달한다.  

### Callback Interface

```java
package com.company;

import java.io.BufferedReader;
import java.io.IOException;

public interface BufferedReaderCallback {
    Integer doSomethingWithReader(BufferedReader br) throws IOException;
}
```

템플릿에서는 BufferedReaderCallback 인터페이스 타입의 콜백 오브젝트를 받아서 적절한 시점에 실행해준다. 콜백이 돌려준 결과는 최종적으로 모든 처리를 마친 후에 다시 클라이언트에게 돌려준다.

### Template Method

```java
package com.company;

import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class Calculator {
    public Integer fileReadTemplate(String filePath, BufferedReaderCallback callback) throws IOException{
        BufferedReader br = null;
        try{
            br = new BufferedReader(new FileReader(filePath));
            int ret = callback.doSomethingWithReader(br);
            return ret;
        }catch (IOException e){
            System.out.println(e.getMessage());
            throw e;
        }finally {
            if(br != null){
                try {
                    br.close();
                }
                catch (IOException e) {
                    System.out.println(e.getMessage());
                }
            }
        }
    }
}

```

 콜백이 돌려준 결과는 최종적으로 모든 처리를 마친 후에 다시 클라이언트에게 되돌려준다. BufferedReader를 만들어서 넘겨주는 것과 그 외의 모든 번거로운 작업에 대한 작업의 흐름은 템플릿에서 진행하고, 준비된 BufferedReader를 이용해 작업을 수행하는 부분은 콜백을 호출해서 처리한다. 

템플릿으로 분리한 부분을 제외한 나머지 코드를 BufferedReaderCallback 인터페이스로 만든 익명 내부 클래스에 담는다. 처리할 파일의 경로와 함께 준비된 익명 내부 클래스의 오브젝트를 템플릿에 전달한다.   


### Template + Callback Method

```java
    public Integer calcSum(String filePath) throws IOException{
        BufferedReaderCallback sumCallback = new BufferedReaderCallback() {
            @Override
            public Integer doSomethingWithReader(BufferedReader br) throws IOException {
                Integer sum = 0;
                String line = null;
                while((line = br.readLine())!= null){
                    sum += Integer.valueOf(line);
                }
                return sum;
            }
        };
        return fileReadTemplate(filePath,sumCallback);
    }
```

### 테스트 코드 

```java
package com.company;

import org.junit.jupiter.api.BeforeAll;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import java.io.IOException;
import static org.hamcrest.CoreMatchers.is;
import static org.hamcrest.MatcherAssert.assertThat;


class CalculatorTest {
    Calculator calculator;
    String numFilePath;

    @BeforeEach
    void setUp(){
        this.calculator = new Calculator();
        this.numFilePath = "src/main/resources/numbers.txt";
    }

    @Test
    public void sumOfNumbers() throws IOException{
        assertThat(calculator.calcSum(numFilePath),is(10));
    }
}
```

### 곱셈 로직 추가

```java
    public Integer calcMul(String filePath) throws IOException{
        BufferedReaderCallback mulCallback = new BufferedReaderCallback() {
            @Override
            public Integer doSomethingWithReader(BufferedReader br) throws IOException {
                Integer multiply = 1;
                String line  = null;
                while((line = br.readLine())!= null){
                    multiply *= Integer.valueOf(line);
                }
                return multiply;
            }
        };
        return fileReadTemplate(filePath,mulCallback);
    }
```

콜백함수를 구성하는 구조는 생각보다 다른언어들과 비슷하다. ex\) JS  인터페이스 부분을 익명함수 선언을 하면서, 로직이 들어간다. 

ㅋ

