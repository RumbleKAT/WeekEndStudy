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



