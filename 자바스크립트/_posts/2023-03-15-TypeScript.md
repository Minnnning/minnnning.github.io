타입 스크립트란 자바스크립트의 문법을 그대로 이용 가능한데 타입 부분을 업그레이드 해서 쓸 수 있다는 차이점이 있다 크기가 큰 프로젝트를 할 때는 주로 타입 스크립트를 사용한다 일종의 자바스크립트의 부가 기능으로 봐도 된다

&nbsp;

 ### 사용하는 이유

1. 자바스크립트는 `1 - '3'`을 해도 자동 형변환을 통해서 계산이 되지만 이 부분을 제한 할 수 있다 (코드를 길게 작성한다면 자유도와 유연성은 별로 좋지 않다) 타입 스크립트는 에러를 출력한다
2. 에러 메세지가 정확해진다 (원래는 추상적으로 나온다)



&nbsp;

&nbsp;

### 타입 스크립트 설치 및 사용

1. node.js 설치

2. vscode 준비

3. 터미널 `npm install -g typescript `

4. tsconfig.json 파일 생성 (ts ->js 컴파일에 관한 정보 저장)
   ``` json
   {   
     "compilerOptions" : {     
       "target": "es5",     
       "module": "commonjs",  
     }
   }
   ```

5. 이름.ts로 파일을 만들고 사용

6. 브라우저는 js파일만 인식하기 때문에 ts -> js변환 과정이 필요
   tsc -w를 입력하면 자동으로 변환시켜준다

&nbsp;

&nbsp;

### 타입 스크립트 문법

``` typescript
let 이름 :string = 'minnnning';
```

이름이라는 변수에는 string 형태만 가능하다

사용할 수 있는 자료형은 **(string, number, boolean, null, undefined, bigint, [], {}등)**

&nbsp;

```typescript
let name :string[] = ['kim', 'min'];
```

배열중에서 string 형태만 올 수 있다

&nbsp;

``` typescript
let aaa :{ name : string } = { name : 'kim'};
let bbb :{ name? : string } = { };
```

object 형태의 선언이고 속성 name뒤에 ?가 붙어서 속성이 안들어와도 오류를 출력 안시킬 수 있다

&nbsp;

한 변수에 다양한 타입이 들어올 수 있게 **Union type**

``` typescript
let name :string | number = 123;
```

&nbsp;

타입을 변수에 담아서 쓸수 있다 Type alias

``` typescript
type Type1 = string | number;
let name :Type1 = 123;
```

&nbsp;

함수에서 타입지정

``` typescript
function func(x: number) :bigint {
  return x+1;
}
```

파라미터 x의 타입지정number 리턴값의 타입 지정bigint

&nbsp;

만약 object타입에서 속성들이 매우 많다면 모두 작성하지 않아도 된다

``` typescript
type Member = {
  [key :string] : string,
};
let person :Member = {name :'minnnning', age: '16'};
```

&nbsp;

class 타입지정

``` typescript
class User {
  name :string;
  constructor(name :string){
    this.name = name;
  }
}
```

&nbsp;

&nbsp;

---

[코딩애플 타입스크립트](https://www.youtube.com/watch?v=xkpcNolC270){:target='_blank'} 참고