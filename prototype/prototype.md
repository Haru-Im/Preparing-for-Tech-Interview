# Prototype에 대하여

JavaScript의 `prototype`은 JavaScript가 객체 지향 프로그래밍의 **상속을 구현하는 메커니즘**이다. JavaScript는 전통적인 클래스 기반 객체 지향 프로그래밍 언어가 아니라 프로토타입 기반 언어로, 클래스 없이도 객체를 직접 다른 객체로부터 상속할 수 있다. 이러한 상속은 'prototype' 객체를 통해 이루어진다.

## 1. 프로토타입 객체란 무엇인가?

- 모든 JavaScript 객체는 자신의 프로토타입 객체를 가리키는 [[Prototype]]이라는 내부 링크(일종의 참조)를 갖고 있다. 이 링크는 자바스크립트 내부에서만 접근 가능한 속성이다.
- 어떤 객체를 생성할 때, 객체의 원형이 되는 기반이 되는 템플릿이 바로 프로토타입 객체이다. 즉, 새로운 객체는 원형 객체의 속성 및 메서드를 상속받는다.
- 이 프로토타입 객체 역시 자신의 프로토타입을 갖고 있어, 프로토타입 체인이 형성된다. 이 체인은 원칙적으로 null이 나올 때까지 계속된다. null은 프로토타입 체인의 종점이다.

### 1.1. 예시

```javascript
function Animal(type) {
  this.type = type;
}

// Animal의 프로토타입에 메서드를 추가한다. 이 메서드는 Animal의 모든 인스턴스에서 사용할 수 있다.
Animal.prototype.identify = function () {
  return "이 동물은 " + this.type + "이다.";
};

// 'Animal' 함수를 이용해 새로운 인스턴스를 생성한다.
var dog = new Animal("개");

console.log(dog.identify()); // "이 동물은 개이다."
```

위 예시에서 dog 객체는 Animal의 프로토타입에서 identify 메서드를 상속받는다. 이것이 가능한 이유는 dog의 내부 [[Prototype]] 링크가 Animal.prototype을 참조하기 때문이다.

<br/>

## 2. 프로토타입의 동작 방식:

- 객체에서 어떤 속성이나 메서드에 접근하려 할 때, JavaScript 엔진은 해당 객체에 해당 속성이 있는지 확인한다. 만약 객체가 해당 속성을 갖고 있지 않다면, 엔진은 객체의 [[Prototype]] 링크를 따라가 연결된 프로토타입 객체에서 해당 속성을 찾는다.
- 이러한 프로세스는 속성이 발견되거나, 프로토타입 체인의 종점에 도달할 때까지 계속된다.
- 이 메커니즘 덕분에, 한 객체에서 정의된 메서드나 속성을 다른 객체에서 사용할 수 있다. 이것이 JavaScript의 상속 및 코드 재사용성을 가능케 하는 메커니즘이다.

### 2.1. Object.create()

Object.create() 메서드는 지정된 프로토타입 객체 및 속성을 갖는 새 객체를 생성한다. 이 메서드를 사용하면 다른 객체를 기반으로 새 객체를 만들면서 프로토타입 체인을 구성할 수 있다.

Object.create()의 기본 사용법은 다음과 같다:

```javascript
const prototypeObject = {
  someMethod: function () {
    // 메서드 코드
  },
};

const newObject = Object.create(prototypeObject);

newObject.someMethod(); // prototypeObject에 정의된 메서드를 사용할 수 있다.
```

이 예시에서 newObject는 prototypeObject를 프로토타입으로 사용하는 새 객체가 된다. 이는 newObject가 prototypeObject에 정의된 메서드와 속성을 상속받는다는 것을 의미한다.

이 메서드는 특히 다른 객체로부터 직접 상속을 받아야 할 때 유용하다. 기존의 생성자 함수와 new 키워드를 사용하는 방식과 달리, Object.create()는 새 객체를 생성할 때 프로토타입을 더 유연하고 직접적으로 지정할 수 있게 해준다. 이 점은 객체 지향 상속을 보다 세밀하게 제어할 수 있게 해주는 장점이 있다.

### 2.2. 예시

```javascript
function Vehicle() {
  this.hasEngine = true;
}

// Vehicle의 모든 인스턴스가 사용할 수 있는 메서드를 정의한다.
Vehicle.prototype.startEngine = function () {
  if (this.hasEngine) {
    return "엔진이 시동됨.";
  }
};

function Car(brand) {
  this.brand = brand;
}

// Vehicle의 프로토타입을 상속한다.
Car.prototype = Object.create(Vehicle.prototype);

var myCar = new Car("현대");

console.log(myCar.startEngine()); // "엔진이 시동됨."
```

이 예제에서, Car의 인스턴스 myCar는 startEngine 메서드를 직접 갖고 있지 않지만, 프로토타입 체인을 통해 Vehicle의 startEngine 메서드에 접근한다. Car.prototype이 Vehicle.prototype의 메서드를 상속받기 때문에 이러한 동작이 가능하다.

<br/>

## 3. 프로토타입의 활용:

- 객체 생성: 개발자는 함수 생성자를 사용하여 특정 프로토타입을 가지는 새로운 객체를 생성할 수 있다. 이는 new 연산자를 통해 이루어진다.
- 확장 및 재정의: 프로토타입 객체에 새로운 속성이나 메서드를 추가하여 모든 하위 객체가 이를 상속받게 할 수 있다. 또한, 상속받은 메서드를 자식 객체에서 재정의(오버라이딩)하여 사용할 수도 있다.
- JavaScript의 프로토타입 체계는 상속과 코드 재사용성 면에서 강력한 도구이지만, 프로토타입 체인이 복잡해질수록 성능 문제가 발생할 수 있으며, 이해하고 사용하기 어려울 수 있다는 단점도 있다. 따라서, 복잡한 상속 구조를 가진 대규모 프로젝트에서는 클래스와 모듈 시스템을 사용하는 것이 더 효율적일 수 있다.

### 3.1. 확장 및 재정의 예시

```javascript
function Computer(type) {
  this.type = type;
}

Computer.prototype.describe = function () {
  return "이 컴퓨터는 " + this.type + " 타입이다.";
};

var myLaptop = new Computer("노트북");

// 이미 존재하는 메서드를 재정의하거나 새 메서드를 추가할 수 있다.
myLaptop.describe = function () {
  return "이것은 내가 좋아하는 " + this.type + "이다!";
};

console.log(myLaptop.describe()); // "이것은 내가 좋아하는 노트북이다!"
```

위 예시에서 myLaptop 인스턴스는 Computer의 프로토타입으로부터 describe 메서드를 상속받았지만, 해당 인스턴스에 대해 이 메서드를 재정의했다. 이로써, 같은 메서드명이지만 다른 동작을 하는 새로운 메서드를 myLaptop 인스턴스에 제공할 수 있다.
