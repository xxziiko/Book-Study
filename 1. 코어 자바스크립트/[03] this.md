### 전역공간에서의 this

- this는 **함수를 호출할 때 결정**
    - **전역공간에서 this는 전역 객체**를 가리킴 (전역 컨텍스트를 생성하는 주체가 전역 객체이기 때문)
    - 실행 컨텍스트는 변수를 수집해서 렉시컬환경의 프로퍼티로 저장하는데, 전역컨텍스트의 경우 렉시컬환경은 전역객체를 그대로 참조

<br/>

### 메서드로서 호출할 때 그 메서드 내부에서의 this

- 함수와 메서드
    - **함수**는 그 자체로 **독립적인 기능**을 수행하는 반면에 **메서드**는 **자신을 호출한 대상 객체에 관한 동작**을 수행. → 자바스크립트는 상황별로 `this` 키워드에 다른 값을 부여하게 함으로써 구현
- 어떤 함수를 객체의 프로퍼티에 할당한다고 해서 그 자체로서 메서드가 되는게 아니라 **객체의 메서드로서 호출할 경우에만 메서드로 동작**하고, 그렇지 않으면 **함수로 동작**한다.
    
    ```tsx
    const func = function(x) {
    	console.log(this, x);
    }
    
    func(1) // Window {...} 1
    
    const obj = {
    	method: func
    }
    
    obj.method(2) // {method:f} 2
    ```
    
- `this`에는 호출한 주체에 대한 정보가 담긴다.
    - 함수를 메서드로서 호출하는 경우 → 호출 주체는 함수명(프로퍼티 명) 앞의 객체
 
<br/>
  

### 함수로서 호출할 때 그 함수 내부에서의 this

- 함수를 함수로서 호출할 경우 `this`가 지정되지 않음.
    - 함수로서 호출하는 것은 호출 주체를 명시하지 않고 개발자가 코드에 직접 관여하여 실행한 것이기 때문에 호출 주체의 정보를 할 수 없음.
    - 실행 컨텍스트 활성화 당시 `this`가 지정되지 않은 경우 `this`는 **전역 객체**를 바라봄. → 함수를 함수로서 호출할경우 `this`는 전역 객체를 가르킨다.
- `this` 바인딩에 관해서는 함수를 실행하는 당시의 주변 환경은 중요하지 않고, 오직 해당 함수를 호출하는 구문 앞이 중요하다.
- 함수 내부에서 `this`가 전역객체를 바라보는 문제를 보완하고자 `this`를 바인딩 하지 않는 **화살표 함수** 도입
    - 실행 컨텍스트를 생성할 때 `this` 바인딩 과정을 생략하여 **상위 스코프**의 `this`를 활용
    
<br/>



### 생성자 함수 내부에서의 this

- 프로그래밍적으로 생성자는 구체적인 인스턴스를 만들기 위한 일종의 틀
- 생성자 함수를 호출하면 생성자의 `prototype` 프로퍼티를 참조하는 `__proto__` 프로퍼티가 있는 인스턴스(객체)를 만들고, `this`를 부여
    
    → 내부에서의 `this`는 인스턴스 자신

<br/>



### 명시적으로 this를 바인딩하는 방법

1. `call`
    - 메서드의 호출 주체인 함수를 즉시 실행하는 명령
    - 첫번째 인자를 `this`로 바인딩, 이후의 인자들은 호출할 함수의 매개변수
    
    ```tsx
    const func = function(a,b,c) {
    	console.log(this, a, b, c);
    }
    
    func(1, 2, 3) // window{...} 1 2 3
    func.call({x: 1} , 4, 5, 6) // {x: 1} 4 5 6
    ```
    
2. `apply`
    - `call` 메서드와 기능적으로 완전히 동일
    - `apply` 메서드는 두 번째 인자를 배열로 받아 그 배열의 요소들을 호출할 함수의 매개변수로 지정
    
    ```tsx
    const func = function(a,b,c) {
    	console.log(this, a, b, c);
    }
    
    func.apply({x: 1}, [4, 5, 6])
    
    const obj ={
    a: 1,
    method: function(x, y) {
    	console.log(this, a, x, y);
    	}
    }
    
    obj.method.apply({a: 4}, [5, 6]) // 4 5 6
    ```
    

**call / apply 메서드의 활용**

1. 유사배열 객체에 배열 메서드 적용
    1. 키가 0 또는 양의 정수인 프로퍼티가 존재하고 length 프로퍼티 값이 0 또는 양의 정수인 객체(배열의 구조와 유사할 경우)
    
    ```tsx
    const obj = {
    	0 :'a',
    	1: 'b',
    	2: 'c',
    	length: 3
    }
    
    Array.prototype.push.call(obj, 'd')
    
    const arr = Array.prototype.slice.call(obj); // ['a', 'b', 'c', 'd']
    ```
    

b. arguments, nodeList에 배열 매서드 적용 가능

c. 문자열에 배열 메서드 적용 가능

→ call / apply 를 이용해서 형변환 하는 것은 ‘`this`를 원하는 값으로 지정해 호출한다’라는 본래의 의도와는 다른 활용법 → ES6에서 `Array.from()` 메서드 도입

```tsx
const obj = {
	0 :'a',
	1: 'b',
	2: 'c',
	length: 3
}

const arr = Array.from(obj)
```

1. 생성자 내부에서 다른 생성자 호출
    
    ```tsx
    function Person(name, gender) {
    	this.name = name;
    	this.gender = gender;
    }
    
    function Student(name, gender, school) {
    	Person.call(this, name, gender);
    	this.school = shool;
    }
    
    function Employee (name, gender, company) {
    	Person.apply(this, [name, gender]);
    	this.company = company;
    }
    ```
    

1. 여러 인수를 묶어 하나의 배열로 전달 (`apply`)
    
    ```tsx
    const numbers = [10, 20, 3]
    const max = Math.max.apply(null, numbers)
    const min = Math.min.apply(null, numbers)
    
    console.log(max, min) // 20 3
    
    // ES6
    const max = Math.max(...numbers)
    const min = Math.min(...numbers)
    ```
    

1. `bind`
    - `call` 과 비슷하지만 바로 호출하지 않고 넘겨받은 `this` 및 인수들을 바탕으로 새로운 함수를 반환
        
        ```tsx
        const func = function (a,b,c,d) {
        	console.log(this, a,b,c,d)
        }
        
        func(1,2,3,4)
        
        // 함수에 this를 미리 적용
        const bindFunc1 = func.bind({x: 1})
        bindFunc1(5, 6, 7, 8) // {x: 1} 5 6 7 8
        
        // 부분 적용 함수를 구현
        const bindFunc2 = func.bind({x: 1}, 4, 5)
        bindFunc2(6, 7) // {x : 1} 4 5 6 7
        
        // name 프로퍼티에 동사 bind의 수동태인 bound 접두어가 붙음
        console.log(func.name) //func
        console.log(bindFunc2.name) //bound func
        ```
        
    - ‘bound xxx ‘ 라는 것은 곧 함수명이 xxx인 원본 함수에 bind 메서드를 적용한 새로운 함수라는 의미 → `call`이나 `apply`보다 코드를 추적하기 수월
    - 상위 컨텍스트의 `this`를 내부함수나 콜백 함수에 전달하기 위해 `call`, `apply`, `bind` 메서드를 이용할 수 있다.
    
2. 화살표 함수의 예외사항
    - 화살표함수는 실행 컨텍스트 생성 시 `this`를 바인딩하는 과정 제외 됐기 때문에 **함수 내부에 `this`가 없으며** 접근하고자 하면 **스코프 체인상 가장 가까운** `this`에 접근 → 별로의 변수로 `this`를 우회하거나 `call` / `apply` / `bind`를 적용할 필요 없음
3. 별도의 인자로 `this`를 받는 경우 (콜백 함수 내에서의 `this`)
    
    ```tsx
    Array.prototype.forEach(callback[, thisArg])
    Array.prototype.map(callback[, thisArg])
    Array.prototype.filter(callback[, thisArg])
    ...
    ```
    
    - 콜백함수를 인자로 받는 메서드 중 일부는 추가로 `this`로 지정할 객체(`thisArg`)를 인자로 지정할 수 있는 경우가 있다. → 값을 지정하면 콜백 함수 내부에서 `this` 값을 원하는 대로 변경

<br/>
    
    
### 정리
    
- 전역공간에서의 `this`는 전역객체(window, global)를 참조
- 어떤 함수를 **메서드로서 호출한 경우** `this`는 메서드 호출 주체(**메서드 앞의 객체**)를 참조
- 어떤 함수를 **함수로서 호출한 경우** `this`는 **전역객체를 참조**하고 메서드의 내부 함수에서도 마찬가지이다.
- 콜백함수 내부에서의 `this`는 해당 콜백 함수의 제어권을 넘겨받은 함수가 정의한 바에 따르며, 정의하지 않은 경우 전역객체를 참조
- **생성자 함수**에서의 `this`는 생성될 **인스턴스를 참조**
- `call`, `apply`메서드는 `this`를 명시적으로 지정하면서 함수 또는 메서드를 호출
- `bind` 메서드는 `this` 및 함수에 넘길 인수 일부를 지정해서 새로운 함수를 반환
- 요소를 순회하면서 콜백 함수를 반복 호출하는 내용의 일부 메서드는 별도의 인자로 `this`를 받기도 한다.
