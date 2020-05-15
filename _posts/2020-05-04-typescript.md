---
title: "typescript에서 타입 선언하기"
date: 2020-03-05 15:40:00 -0400
categories: typescript
---
타입스크립트에서 타입을 선언하는 기본 방법을 정리했습니다.

## 타입 선언 (Type Declaration)

타입은 소문자, 대문자를 구별하므로 주의가 필요하다. TypeScript가 "기본 제공하는 타입"은 모두 소문자이다.

**boolean**

```tsx
let isLoggedIn: boolean = false;
```

**number**

```tsx
let num: number = 10;
```

**string**

```tsx
let str: string = 'hi';
let greeting: string = `Hello, my name is ${ myName }.`; // ES6 템플릿 대입문
```

**null**

```tsx
let n: null = null;
```

**undefined**

```tsx
let u: undefined = undefined;
```

**object**

```tsx
const obj: object = {};
```

**array**

```tsx
let arr: number[] = [1,2,3];

// 제네릭 배열 타입
let arr: Array<number> = [1,2,3]; 
```

**tuple**

고정된 요소수 만큼의 타입을 미리 선언후 순서에 맞게 배열을 표현

```tsx
let arr: [string, number] = ['hi', 10];
let tuple: [string, number];
tuple = ['hello', 10]; // OK
tuple = [10, 'hello']; // Error
tuple = ['hello', 10, 'world', 100]; // Error
tuple.push(true); // Error
```

**enum**

특정 값(상수)들의 집합을 의미하는 자료형

```tsx
enum Color1 {Red, Green, Blue};
let c1: Color1 = Color1.Green;

// export하거나 값을 줄 수 있다.
export enum MatchResult {
  HomeWin = 'H',
  AwayWin = 'A',
  Draw = 'D'
}

// 인덱스 번호로도 접근 가능
enum Avengers { Capt, IronMan, Thor }
let hero: Avengers = Avengers[0];

// 초기 값을 주면 초기 값부터 차례로 1씩 증가
enum Avengers { Capt = 2, IronMan, Thor }
let hero: Avengers = Avengers[2]; // Capt
let hero: Avengers = Avengers[4]; // Thor
```

**any**

타입 추론(type inference)할 수 없거나 타입 체크가 필요 없는 변수에 사용

```tsx
let notSure: any = 4;
notSure = 'maybe a string instead';
notSure = false; // okay, definitely a boolean
```

**void**

일반적으로 함수에서 반환값이 없을 경우 사용

```tsx
function warnUser(): void {
  console.log("This is my warning message");
}
```

**never**

결코 발생하지 않는 값. 함수의 끝에 절대 도달하지 않는다는 의미를 지닌 타입

```tsx
// 이 함수는 절대 함수의 끝까지 실행되지 않는다는 의미
function infiniteLoop(): never {
  while (true) {}
}
function error(message: string): never {
  throw new Error(message);
}
```

## 타입 어썰션(Type Assertions)

개발자가 해당 타입에 대해 확신이 있을 때 사용하는 타입 지정 방식

```tsx
let shouldString: any = 'hi';
let stringLength: number = (<string>shouldString).length;
```

```tsx
// as 문법 활용
let shouldString: any = 'hi';
let stringLength: number = (shouldString as string).length;
```

## 함수(function) 타입 정의하기

문법

```tsx
function sum(a: 인자타입, b: 인자타입): 반환값타입 {
  return a + b;
}
```

예시

```tsx
// 함수 기본
function sum(a: number, b: number): number {
  return a + b;
}
sum(10, 20); // 30
sum(10, 20, 30); // error, too many parameters
sum(10); // error, too few parameters

// rest 문법 적용할 때
function sum(a: number, ...nums: number[]): number {
  const totalOfNums = 0;
  for (let key in nums) {
    totalOfNums += nums[key];
  }
  return a + totalOfNums;
}

// this 사용하기
interface UIElement {
  // 아래 함수의 `this: void` 코드는 함수에 `this` 타입을 선언할 필요가 없다는 의미입니다.
  addClickListener(onclick: (this: void, e: Event) => void): void;
}

class Handler {
    info: string;
    onClick(this: Handler, e: Event) {
        // 위의 `UIElement` 인터페이스의 스펙에 `this`가 필요없다고 했지만 사용했기 때문에 에러가 발생합니다.
        this.info = e.message;
    }
}
let handler = new Handler();
uiElement.addClickListener(handler.onClick); // error!
```

## 인터페이스(interface)

문법

```tsx
interface 인터페이스_이름 {
	속성: 타입;                                      // 기본형식
  속성?: 타입;                                     // 선택적 프로퍼티. 반드시 구현하지 않아도 될 속성명 뒤에 ? 추가
	readonly 속성: 타입;                             // 읽기 전용 속성 앞에 readonly 추가
  [propName: string]: any;                        // 인터페이스 정의하지 않은 속성들을 추가로 사용하고 싶을 때 (가급적 사용하지 말아야 할듯)
	(username: string, password: string): boolean;  // 함수. 함수의 인자의 타입과 반환 값의 타입을 정한다
}
```

예시

```tsx
// 인터페이스 기본 예시
interface CraftBeer {
  name: string;
  hop?: number;
	readonly brand: string;
}

let myBeer = {
  name: 'Saporo',
  brand: 'Belgian Monk'
};
myBeer.brand = 'Korean Carpenter'; // error!
function brewBeer(beer: CraftBeer) {
  console.log(beer.name); // Saporo
}
brewBeer(myBeer);

// 인터페이스 함수 정의
interface login {
  (username: string, password: string): boolean;
}

let loginUser: login;
loginUser = function(id: string, pw: string) {
  console.log('로그인 했습니다');
  return true;
}

// 인터페이스 상속
interface Person {
  name: string;
}
interface Developer extends Person {
  skill: string;
}
let fe = {} as Developer;
fe.name = 'josh';
fe.skill = 'TypeScript';
```

## 타입 앨리어스(type alias)

새로운 타입을 정의한다. 타입으로 사용할 수 있다는 점에서 타입 앨리어스는 인터페이스와 유사하다.

하지만 타입 앨리어스는 원시값, 유니온 타입, 튜플 등도 타입으로 지정할 수 있다.

```tsx
// 문자열 리터럴로 타입 지정
type Str = 'Lee';

// 유니온 타입으로 타입 지정
type Union = string | null;

// 문자열 유니온 타입으로 타입 지정
type Name = 'Lee' | 'Kim';

// 숫자 리터럴 유니온 타입으로 타입 지정
type Num = 1 | 2 | 3 | 4 | 5;

// 객체 리터럴 유니온 타입으로 타입 지정
type Obj = {a: 1} | {b: 2};

// 함수 유니온 타입으로 타입 지정
type Func = (() => string) | (() => void);

// 인터페이스 유니온 타입으로 타입 지정
type Shape = Square | Rectangle | Circle;

// 튜플로 타입 지정
type Tuple = [string, boolean];
const t: Tuple = ['', '']; // Error
```

## 타입 앨리어스 or 인터페이스?

일반적인 리액트 use case에서 type과 interface는 매우 유사한 방식으로 사용된다.

아래가 도움이 될만한 규칙이다. ([출처](https://github.com/typescript-cheatsheets/react-typescript-cheatsheet/blob/master/README.md#types-or-interfaces))

- `interface` : 라이브러리 제작할 때 등 API 명세를 작성할 때는 꼭 인터페이스를 사용. 상속을 통해 확장이 필요할 때 더 유리하다.
- `type`: 좀더 제한적이므로 리액트 컴포넌트 props와 state를 사용할 때 고려하세요.  유니온(e.g. `type MyType = TypeA | TypeB`) 또는 튜플일 때 더 유리하다.

![https://camo.githubusercontent.com/f00cd1e1d40c197e5cdb82c383952241d7e0dc10/68747470733a2f2f7062732e7477696d672e636f6d2f6d656469612f4477562d6f4f7358634149637432712e6a7067](https://camo.githubusercontent.com/f00cd1e1d40c197e5cdb82c383952241d7e0dc10/68747470733a2f2f7062732e7477696d672e636f6d2f6d656469612f4477562d6f4f7358634149637432712e6a7067)

## 제네릭(generics)

재사용성이 높은 컴포넌트를 만들 때 자주 활용. 특히, 한가지 타입보다 여러 가지 타입에서 동작하는 컴포넌트를 생성하는데 사용됨.

```tsx
function logText<T>(text: T): T {
  return text;
}

// # 사용1
const text = logText<string>("Hello Generic");
// # 사용2
const text = logText("Hello Generic");
```

### 제네릭 타입

```tsx
// 배열1
function logText<T>(text: T[]): T[] {
  console.log(text.length); // 제네릭 타입이 배열이기 때문에 `length`를 허용합니다.
  return text;
}
```

```tsx
// 배열2
function logText<T>(text: Array<T>): Array<T> {
  console.log(text.length);
  return text;
}
```

```tsx
// 함수
function logText<T>(text: T): T {
  return text;
}
// #1
let str: <T>(text: T) => T = logText;
// #2
let str: {<T>(text: T): T} = logText;
```

### 제네릭 인터페이스

```tsx
interface GenericLogTextFn {
  <T>(text: T): T;
}
function logText<T>(text: T): T {
  return text;
}
let myString: GenericLogTextFn = logText; // Okay
```

```tsx
// OR
interface GenericLogTextFn<T> {
  (text: T): T;
}
function logText<T>(text: T): T {
  return text;
}
let myString: GenericLogTextFn<string> = logText;
```

### 제네릭 제약 조건

```tsx
// 타입에 대한 강제는 아니지만 length에 대해 동작하는 인자만 넘겨받을 수 있게 됨
interface LengthWise {
  length: number;
}

function logText<T extends LengthWise>(text: T): T {
  console.log(text.length);
  return text;
}
```

```tsx
// 객체 속성 비교 예제
function getProperty<T, O extends keyof T>(obj: T, key: O) {
  return obj[key];  
}
let obj = { a: 1, b: 2, c: 3 };

getProperty(obj, "a"); // okay
getProperty(obj, "z"); // error: "z"는 "a", "b", "c" 속성에 해당하지 않습니다.
```

배열을 선언할 때 ReadonlyArray<T> 타입을 사용하면 읽기 전용 배열 생성 가능하다

```tsx
let arr: ReadonlyArray<number> = [1,2,3];
arr.splice(0,1); // error
arr.push(4); // error
arr[0] = 100; // error
arr = [10, 20, 30]; // error
```
