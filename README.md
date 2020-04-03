# 당신은 ES6의 const와 let의 분명한 차이점을 알고 있습니까?
> 이 글은 Medium의 'Ethan Nam'님의 'JavaScript's Memory Mode' 글을 번역 및 제가 읽고 이해한 내용을 옮겨 놓은 글 입니다.<br>
원작 URL : https://medium.com/@ethannam/javascripts-memory-model-7c972cd2c239

# 1. Goodbye 'var'!
```javascript
// Declare some variables and initalize them
var a = 5;
let b = 'xy';
const c = true;

// Assgin new values
a = 6;
b = b + 'z';
c = false; // TypeError : Assignment to constant variable
```
프로그래머로써, 변수를 선언하고 초기화하거나 그렇지 않거나, 새로운 값을 할당하는 일련의 작업을 우리들은 매일 기본으로 하고 있다. 그러나, 실제로 이것들(변수를 선언하고 초기하고 값을 할당하는 작업들) 실행 될 때 실제로 어떻게 동작하는지 알고 있는가? 너는 JS의 내부 기본 기능들이 어떻게 동작하는지 알고 있는가? 그리고 더 중요한 것은 과연 이러한 내부 기본 기능들의 동작까지 세세하게 안다고 해서 'Javascript'을 이해하고 개발하는 프로그래머(개발자)에게 어떤 도움이 있을까?
<br>

# 2. 변수 선언 그리고 primitive(7가지 데이터 타입, String, Number, Bigint, Boolean, Null, undefined,symbol) 할당
아래 간단한 코드를 보자. 아래 코드에서, 우리는 변수를 선언하고 초기화를 했으며, 초기 값은 23으로 할당했고 변수의 이름은 "myNumber"이다.
```javascript
let myNumber = 23;
```
이 코드가 실행될 때 JS는 <br>
1. 변수에 대한 고유 식별자를 생성한다.(고유 식별자(변수의 이름)은 "myNumber")<br>
2. 메모리 주소를 할당한다.(이 부분은 rumtime 때 실행)<br>
3. 할당된 메모리 공간에 변수의 값(23)을 저장한다.<br>
![1](https://user-images.githubusercontent.com/58014147/78316476-bcbae600-759a-11ea-9a48-23083fa17085.png)<br>

만약에 우리가 새로운 변수를 생성하고 그것을 "newVar"는 식별자를 붙여주고 "myNumber"의 값을 할당해준다면... "myNumber"는 기술적으로 "0012CCGWH80"와 같으므로 "newVar"도 값 23을 저장하고 있는 메모리 주소인 "0012CCGWH80"과 같습니다. 궁긍적으로 이는 "newVar"은 이제 23과 같다"라는 구어체 효과를 갖는다.<br>

만약 이렇게 아래 코드를 실행한다면,
```javascript
myNumber = myNumber + 1;
```
위에 코드를 실행하게 되면, "myNumber"는 확실하게 24라는 값을 가지게 된다. 그렇다면 myNumber와 같은 메모리 주소를 가리키고 있는 "newVar"의 값도 24가 되는 것일까?<br>
그것은 아니다. JS의 원시적 데이터 타입은 불변성(immutable)이다. "myNumber + 1"이 실행되고 결과 값 "24"가 나왔을 때, JS는 새로운 메모리 주소를 할당하고, 저장한다 "24"라는 정수를 값으로, 그리고 "myNumber"는 새로운 주소를 가리키게 된다.<br>
![2](https://user-images.githubusercontent.com/58014147/78317185-94cc8200-759c-11ea-95be-02f479c5a499.png)

또 다른 예제를 살펴보자.
```javascript
let myString = 'abc';
my String = myString + 'd';
```
JS의 초심자들은 위에 코드를 보고 문자 'd'가 단순하게 'abc' 문자열에 appended 됐다라고 이야기 할 것이다. 'abc'문자열이 존재하는 메모리 공간에 말이다. 하지만 이것은 기술적으로 잘못됐다.<br>
'abc'문자열에 'd' 문자를 concat(연결)할 때, String data type 또한 primitive data type이다, 새로운 메모리 공간을 할당하고 연결된 문자열 'abcd'을 그곳에 '값으로(By value)' 저장한다. 그리고 "myString"은 새로운 메모리 주소공간을 가리키게(points to this new memory address) 된다.<br>
![3](https://user-images.githubusercontent.com/58014147/78317602-b5490c00-759d-11ea-8586-7fa11e787c41.png)

# 3. callStack과 Heap
callStack과 Heap 메모리 이다. 공통점은 "데이터를 저장할 수 있다."라는 점이다. 차이점은 서로 저장하는 데이터의 타입(Data Type)이 다르다라는 것이다. 
callStack은 primitive data type과 호출된 function가 저장된다. Heap에는 non-primitive 데이터가 저장된다. Heap은 정렬되지 않는 데이터를 동적으로 커질 수 있는 배열과 객체를 완벽하게 저장할 수 있는 저장 공간이다.<br>
나도 이렇게 글을 번역하면서 다시보고 있지만, 글로는 잘 이해가 되지 않는다. 문자열의 데이터든 객체의 데이터든 어쨋든 변수에 저장해서 사용하니깐 callstack에 저장되는 거 아니야? 라고 생각할 수 있다. 하지만 반은 틀린 생각이였다. 원시적 데이터 타입은 변수에 저장하면 callstack 메모리 공간에 "할당한 값"이 저장된다. 위에 예제처럼. 하지만 배열과 객체는 "값"이 저장되는 것이 아니라, Heap의 메모리 주소가 저장된다. 예제 코드를 한번 보자.
```javascript
let myArray = [];
```
myArray이라는 변수를 선언했고, non-primitive data type을 할당했다. 배열의 빈 값을 표현하는 '\[\]' 값을. 그렇다면 메모리에서 어떤 동작을 할까?
1. myArray라는 고유 식별자를 생성한다.
2. 메모리 주소 공간을 할당한다.
3. Heap 메모리 공간에 할당된 공간의 주소(Heap 메모리주소)를 저장한다.
4. Heap 메모리 공간에 "값(By value)"을 저장한다. value는 "\[\]" 이다.
![4](https://user-images.githubusercontent.com/58014147/78318412-9cd9f100-759f-11ea-829a-e879c8c3226a.png)

# 4. Let vs const
그럼 이제 이 글의 목적인 그렇다면 과연 Let과 Const keyword의 차이점은 무엇인가???!!<br>
일반적으로 const keyword를 사용해서 변수를 선언하면 할당된 값을 change 할 수 없고, let으로 변경을 선언하게 되면 할당되 값을 change 할 수 있다라고 한다.
그럼 "change"라는 의미에 대해서 명백하게 한번 살펴보자.
```javascript
let sum = 0;
sum = 1+2+3+4+5;

let numbers = [];
numbers.push(1);
numbers.push(2);
numbers.push(3);
numbers.push(4);
numbers.push(5);
```
위 코드를 작성한 프로그래머는 명확하게 sum은 let을 사용해서 선언했다. 왜냐하면 값을 변경해야 하기 때문에 let으로 선언 해야한다고 이야기한다. 그러나 이 프로그래머는  let를 사용해서 numbers를 틀리게 선언했다. 왜냐하면 그들은 배열에 어떠한 것을 값으로써 change 하기 위해 넣어야(push) 하기 때문에라고 설명한다.<br>
하지만 "change"의 명확한 설명은 "값(Value)"을 변경하는 것이 아니라, __"memory address"를 변경하는 것이다.__ __let은 메모리 주소를 바꾸는 것을 허락한다.__ 하지만 __const는 메모리 주소를 변경하는 것을 허락하지 않는다.__ 그렇기 때문에 아래 코드는 에러 없이 정상적으로 동작한다.
```javascript
const array = [];
array.push(1);
array.push(2);
array.push(3);
array.push(4);

const myObject = {};
myObject[['newKey'] = 'someValue'; // This will not throw an Error!
```
배열의 값을 할당하거나, 객체의 멤버를 추가하는 액션은 __"할당 받은 메모리 공간"에 값을 넣는 것이다.__













