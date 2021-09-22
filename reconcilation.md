---
description: React Virtual DOM
---

# Reconcilation

## **DOM이란?**

* Document Object Model
* 객체로 문서 구조를 표현하는 방법
* 트리 형태로 특정 노드 순회, 수정, 제거 ,생성
* 정적
* JS를 이용하여 동적으로 만들 수있다.

  
 DOM 자체는 빠르지만, 요소의 개수가 수백개 수천개로 늘어날 경우, 허비되는 시간이 많아져 결국엔 느려진다. 

결정적인 이유는 그 과정에서 DOM은 변화가 일어날 때

1. CSS의 재연산
2. 레이아웃 구성
3. 페이지 리페인트

## Virtual DOM

속도 이슈에 대한 해결책은 결국 DOM을 최소한으로 조작할수 밖에 없다.

Virtual DOM 방식을 사용해 DOM의 업데이트를 추상화 처리하여 처리 횟수를 최소화\(=실제 DOM의 사본 정도\)

1. 업데이트한 전체 UI를 Virtual DOM에 리렌더링
2. 실제 DOM과 생성된 Virtual DOM을 비교
3. 바뀐 부분만 실제 DOM에 적용

## 그렇다면 Virtual DOM은 정말로 빠를까?

* 지속적으로 변화하는 대규모 애플리케이션 구축시에만 유리
* 즉 간단한 작업\(단순 라우팅 작업만 있는 경우\)일 땐 리엑트를 사용하지 않는 것이 더 효율적
* 코드 최적화를 열심히 하면 속도 문제를 개선할 수 있다. 

리엑트가 보장하는 성능은 업데이트 처리 간결성이지, 무조건적으로 리엑트를 사용하는 것이 아닌 때때로 유연하게 개발을 해야됨

## Reconcilation

React를 사용하다 보면, ‘render\(\) 함수는 React 엘리먼트 트리를 만드는 것이다.’ State 와 props가 갱신되면 render\(\) 함수는 새로운 React Element Tree를 반환. React는 이때 만들어진 트리에 맞게 가장 효과적으로 UI를 갱신하는 법을 알아내야함

하나의 트리를 가지고 다른 트리로 변환하기 위한 최소한의 연산 수를 구하는 알고리즘의 시간복잡도는 n^3 

React에서 이 알고리즘을 적용한다면, 1000개의 엘리먼트를 그리기 위해 10억번의 비교 연산을 수행 

React는 두 가지 가정을 기반하여 O\(n\) 복잡도의 휴리스틱 알고리즘을 구현

1. 서로 다른 타입의 두 엘리먼트는 서로 다른 트리를 만들어낸다.
2. 개발자가 key props를 통해, 여러 렌더링 사이에서 어떤 자식 엘리먼트가 변경되지 않아야 할지 표시해 줄수있다. 

## 비교 알고리즘

### **엘리먼트의 타입이 다른 경우**

React는 두 root element부터 비교한다. 두 root element의 타입이 다르면 React는 이전 트리를 버리고 완전히 새로운 트리를 구축한다. &lt;a&gt;에서 &lt;img&gt;로, &lt;Article&gt;에서 &lt;Comment&gt;로, 혹은 &lt;Button&gt;에서 &lt;div&gt;로 바뀌는 것 모두 트리 전체를 재구축하는 경우

트리를 버릴때 이전 DOM 노드들은 모두 파괴된다. 컴포넌트 인스턴스는 componentWillUnmount\(\)가 실행. 새로운 트리가 만들어질 때, 새로운 DOM 노드들이 DOM에 삽입. 그에 따라 컴포넌트 인스턴스는 UNSAFE\_componentWillMount\(\)가 실행되고 componentDidMount\(\)가 이어서 실행된다. 이전 트리와 연관된 모든 state는 사라진다. 

```markup
[Before]
<div>
  <Counter />
</div>

[After]
<span>
  <Counter />
</span>
```

### DOM 엘리먼트의 타입이 같은 경우

같은 타입의 두 React DOM 엘리먼트를 비교할때, React는 두 엘리먼트의 속성을 확인하여, 동일한 내역은 유지하고 변경된 속성만 갱신한다. 

```markup
[Before]
 <div className=“before” title=“stuff” />

[After]
<div className=“after” title=“stuff” />
```

DOM 노드의 처리가 끝나면, React는 이어서 해당 노드의 자식들을 재귀적으로 처리

### 같은 타입의 컴포넌트 엘리먼트

컴포넌트가 갱신되면 인스턴스는 동일하게 유지되어 렌더링 간 State가 유지된다. React는 새로운 엘리먼트의 내용을 반영하기 위해 현재 컴포넌트 인스턴스의 props를 갱신한다. 다음으로 render\(\) 메소드가 호출되고 비교 알고리즘이 이전 결과와 새로운 결과를 재귀적으로 처리한다.

### 자식에 대한 재귀적처리

DOM 노드의 자식들이 재귀적으로 처리할때, React는 기본적으로 동시에 두 리스트를 순회하고 차이점이 있으면 변경을 생성한다. 

예를 들어 자식의 끝에 엘리먼트를 추가하면, 두 트리 사이의 변경은 잘 작동될 것이다.

```markup
[Before]
<ul>
  <li>first</li>
  <li>second</li>
</ul>

[After]
<ul>
  <li>first</li>
  <li>second</li>
  <li>third</li>
</ul>
```

반대로, 리스트의 맨 앞에 엘리먼트를 추가하는 경우 성능이 좋지 않다. 

React는 &lt;li&gt;Duke&lt;/li&gt;와 &lt;li&gt;Villanova&lt;/li&gt; 종속 트리를 그대로 유지하는 대신 모든 자식을 변경하기에 비효율은 문제가 될수 있음

```markup
[Before]
<ul>
  <li>Duke</li>
  <li>Villanova</li>
</ul>

[After]
<ul>
  <li>Connecticut</li>
  <li>Duke</li>
  <li>Villanova</li>
</ul>
```

이러한 문제를 해결하기 위해 React는 key 속성을 지원한다. 자식들이 key를 가지고 있으면, React는 키를 통해 기존 트리와 이후 트리의 자식들이 일치하는지 확인한다. 

```markup
[Before]
<ul>
  <li key="2015">Duke</li>
  <li key="2016">Villanova</li>
</ul>

[After]
<ul>
  <li key="2014">Connecticut</li>
  <li key="2015">Duke</li>
  <li key="2016">Villanova</li>
</ul>
```

일반적으로 식별자를 가지지 못한 key값에 대해선 키값을 적용해서 키를 생성할수 있다. 해당 키는 오로지 형제 사이에서만 유일하면 되고, 전역에서는 유일할 필요는 없다.

최후의 수단으로 배열의 인덱스를 key로 사용할수 있다. 만약 항목들이 재배열되지 않는다면 이방법도 잘 동작할 것이지만, 재배열되는 경우 비효율적으로 동작한다. 

## React Virtual DOM의 단점

**React는 휴리스틱에 의존하고 있기 때문에, 휴리스틱이 기반하고 있는 가정에 부합하지 않는 경우 성능이 나빠질 수 있습니다.**

1. 알고리즘은 다른 컴포넌트 타입을 갖는 종속 트리들의 일치 여부를 확인하지 않습니다. 만약 매우 비슷한 결과물을 출력하는 두 컴포넌트를 교체하고 있다면, 그 둘을 같은 타입으로 만드는 것이 더 나을 수도 있습니다. 우리는 실제 사용 사례에서 이 가정이 문제가 되는 경우를 발견하지 못했습니다.
2. key는 반드시 변하지 않고, 예상 가능하며, 유일해야 합니다. 변하는 key\(Math.random\(\)으로 생성된 값 등\)를 사용하면 많은 컴포넌트 인스턴스와 DOM 노드를 불필요하게 재생성하여 성능이 나빠지거나 자식 컴포넌트의 state가 유실될 수 있습니다.



