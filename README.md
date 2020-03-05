
Jest and React-testing-library test coverage

```
 PASS  src/component/counter.test.js
 PASS  src/component/profile.test.js
 PASS  src/component/todolist/todoApp.test.tsx
 PASS  src/component/userProfile.test.tsx
 PASS  src/component/delayedToggle.test.tsx (7.018s)
------------------------|----------|----------|----------|----------|-------------------|
File                    |  % Stmts | % Branch |  % Funcs |  % Lines | Uncovered Line #s |
------------------------|----------|----------|----------|----------|-------------------|
All files               |    92.86 |    88.89 |     96.3 |    93.75 |                   |
 src                    |        0 |      100 |        0 |        0 |                   |
  App.tsx               |        0 |      100 |        0 |        0 |                11 |
  index.tsx             |        0 |      100 |      100 |        0 |              7,12 |
  react-app-env.d.ts    |        0 |        0 |        0 |        0 |                   |
 src/component          |    94.44 |     87.5 |      100 |    96.97 |                   |
  counter.tsx           |      100 |      100 |      100 |      100 |                   |
  delayedToggle.tsx     |      100 |      100 |      100 |      100 |                   |
  profile.tsx           |      100 |      100 |      100 |      100 |                   |
  simpleButton.tsx      |      100 |      100 |      100 |      100 |                   |
  userProfile.tsx       |    88.89 |       75 |      100 |    93.75 |                18 |
 src/component/todolist |      100 |       90 |      100 |      100 |                   |
  todoApp.tsx           |      100 |      100 |      100 |      100 |                   |
  todoForm.tsx          |      100 |       50 |      100 |      100 |                14 |
  todoItem.tsx          |      100 |      100 |      100 |      100 |                   |
  todoList.tsx          |      100 |      100 |      100 |      100 |                   |
------------------------|----------|----------|----------|----------|-------------------|

Test Suites: 8 passed, 8 total
Tests:       27 passed, 27 total
Snapshots:   2 passed, 2 total
Time:        9.644s
Ran all test suites.
Done in 10.79s.

```

  

Cypress test coverage

```
Configure is not all set.
Move to: coverage/lcov-report/stc/component

=============================== Coverage summary ===============================
Statements   : Unknown% ( 0/0 )
Branches     : Unknown% ( 0/0 )
Functions    : Unknown% ( 0/0 )
Lines        : Unknown% ( 0/0 )
================================================================================
```

  

# 테스트 라이브러리/프레임워크 조사

저자: [qkreltms](https://github.com/qkreltms)

  

## 개요

점점 커지는 프로젝트를 진행하면서 유닛 테스트의 중요성이 주목받고 있다. 매 순간 코드를 수정할 때 예상치 못한 사이드 이펙트와 버그가 무엇이 있으며 이것을 알아보기 위해 결과물을 확인한다. 이것의 비용은 프로젝트의 규모에 비례해 증가하고 있으며 어느 순간부터는 개발 속도가 급격히 느려진다.  [참고](https://stackoverflow.com/questions/67299/is-unit-testing-worth-the-effort)
이것의 한 가지 강력한 대비책은 테스트 자동화를 통해 개발에 집중할 수 있는 환경을 만드는 것이다.

이 글에서는 테스트 라이브러리에서 사용되는 용어를 알아본 후 추세의 선두에 있는 테스트 라이브러리 Mocha, Enzyme, Jest, React-testing-library(이하 RTL로 지칭), Cypress를 알아본다.

또한, 이 중 두 조합 Jest + RTL, Cypress를 예제를 통해 진행하며 비교한 후 최종적으로 어떤 테스트 라이브러리를 사용하면 좋은지 결과를 도출한다

## 용어설명:

1. **Out of box**: 별도의 설정, 설치 없이 바로 설정 가능
테스트 프레임워크 Mocha는 별도의 Mocking, Spy library를 골라 설치해 사용해 사용자가 자유롭게 선택할 수 있다는 장점이 있는 반면 번거로운 환경 설정과 개발 환경 파편화는 사용자를 골치 아프게 한다.
반면 Jest의 경우 따로 설치할 필요 없이 모든 라이브러리가 내장되어있다(Out of box).
또한, Jest와 RTL의 경우 CRA에 내장되어있어 별도의 설치, 환경 설정 없이 바로 진행할 수 있다.

2. **Snapshot testing**: 사전에 특정 컴포넌트, 페이지 등의 랜더링된 결과물을 찍어 놓고 이후에 새로운 랜더링 결과물과 차이가 있는지 비교한다.
```js
// src/component/counter.test.js
it("matches snapshot", () => {
  const component = render(<Counter />);
  expect(component).toMatchSnapshot();
});
```

위의 코드가 최초 작성 시에 Counter 컴포넌트의 Snapshot을 특정 폴더에 자동으로 저장해 놓고 테스트가 실행될 때마다 미리 저장된 Snapshot과 다른 점이 있는지 비교한다.

만약 Counter 컴포넌트가 변경되었고 Snapshot을 업데이트하고 싶다면 특정 명령어를 입력해 업데이트한다.
Jest의 경우에는 다음의 명령어로 가능하다: ```jest --updateSnapshot```

추가로 RTL의 창시자인 [Kent C. Dodds](https://kentcdodds.com/blog/why-i-never-use-shallow-rendering)에 따르면 매번 컴포넌트를 하다 보면 Snapshot이 달라지기 때문에 사람들이 snapshot 업데이트를 걱정 없이 하므로 Snapshot 테스트를 거의 사용하지 않는다고 한다.

3. **Shallow rendering**: Child component에 대한 걱정 없이 그 컴포넌트만 테스트가 가능하다.
```js
// https://ko.reactjs.org/docs/shallow-renderer.html
describe("<MyComponent />", () => {
  it("check type and props", () => {
    const renderer = new ShallowRenderer();
    renderer.render(<MyComponent />);
    const result = renderer.getRenderOutput();

    expect(result.type).toBe('div');
    expect(result.props.children).toEqual([
      <span className="heading">Title</span>,
      <Subcomponent foo="bar" />
    ]);
  });
});
```

Component를 렌더링 하지 않으며 Child component를 호출하지 않고 Target component의 오브젝트 값만(리엑트의 경우에는 jsx) 불러오기 때문에 어떤 예상치 못한 사이드이펙트를 걱정하지 않아도 된다. [참조](https://enzymejs.github.io/enzyme/docs/api/shallow.html)

추가적으로 Shallow rendering의 경우 실제 DOM을 테스트하는 RTL에서는 권장하지 않는 방법이다. [RTL에서 Shallow rendering을 권장하지 않는이유](https://kentcdodds.com/blog/why-i-never-use-shallow-rendering)

  

4. **Full Rendering**: 실제 DOM을 갖고 테스트한다. DOM API와 버튼 클릭 등과 같은 상호 작용이 있거나 해당 컴포넌트에 적용된 HOC이 필요한 컴포넌트에 이상적이다.[참고](https://github.com/enzymejs/enzyme/blob/master/docs/api/mount.md)
  

5. **Mocking**: 모의 객체라는 뜻이 있으며 실제 사용하는 모듈을 사용하지 않고 그것을 흉내내는 가짜 모듈을 작성하여 테스트의 효용성을 높이는데 사용한다. [참고](https://ko.wikipedia.org/wiki/%EB%AA%A8%EC%9D%98_%EA%B0%9D%EC%B2%B4)

함수에 사용한다면 항상 원하는 값을 반환하도록 할 수 있으며 API call에 사용된다면 함수와 동일하게 특정한 URL에 대해서 항상 동일한 결과물을 반환하도록 할 수 있다.
또한, **어떤 일들이 발생했는지를 기억할 수 있기 때문에 내부적으로 어떻게 사용되는지 검증할 수 있다.** [참고](https://www.daleseo.com/jest-fn-spy-on/)

```js
// https://jestjs.io/docs/en/mock-functions.html

// mockImplementation을 어디에 사용하면 좋을지 곰곰히 생각해본 결과
// 만약 어떤 함수에 인자로 콜백함수가 있고 그 콜백함수를 추적 및 원하는 값을 반환하도록 하고 싶을때 

// foo is a mock function
foo.mockImplementation(() => 42);
foo();
```

```js
// https://github.com/ctimmerm/axios-mock-adapter
// 만약 어떤 함수 또는 컴포넌트가 있고 api 요청을 통해 값을 받는다면 그 요청을 mock이 대신할 수 있다.
// 이것의 장점은 실제 api를 사용하지 않고, 항상 예측가능한, 일관된 데이터를 반환한다는 점.

var mock = new MockAdapter(axios);
// Mock any GET request to /users
// arguments for reply are (status, data, headers)
mock.onGet('/users').reply(200, {
users: [
    { id: 1, name: 'John Smith' }
  ]
});
axios.get('/users')
  .then(function(response) {
    console.log(response.data);
});
```

6. **Spying on**: 엿듣다라는 뜻으로 오브젝트안의 함수 Mocking이 가능하게 함.

주로 모듈이나 library에서 반환하는 함수, 클래스 등을 추적하고 싶을 때 사용함.

```js
// https://jestjs.io/docs/en/es6-class-mocks#spying-on-the-constructor
import SoundPlayer from './sound-player';
jest.mock('./sound-player');
// 이제 ./sound-player 를 추적할 수 있다.
```
Mocking을 더 알고 싶다면: https://www.daleseo.com/jest-mock-modules/


### 인기있는 테스트 프레임워크/라이브러리

#### 1. Mocha

⭐19.1k(20-03-05 기준)

과거 수년 간 가장 인기 많았던 테스트 프레임워크 모카는 사용자가 원하는 Assertion, Snapshot, Mocking, Spy library를 선택할 수 있다는 유연함이 있다는 반면 초심자에게는 까다로울 수 있는 환경 설정, 다양한 라이브러리의 사용으로 인해 유저 마다 다른 라이브러리 선택으로 인한 파편화의 단점이 있다.

설계 특성상 각각의 테스트 케이스는 동기적(Synchronouse)으로 작동하고 독립적인 환경을 구성하지 않고 있으며 이에 따라 원치않는 사이드이펙트 문제가 발생할 수도 있다.
```js
// https://heropy.blog/2018/03/16/mocha/
const should = require('chai').should();
const app = require('../app');

describe('Testing 1', function () {
  it('should return hello', function () {
    app.sayHello().should.equal('hello');
  });
});
```

  

#### 2. Jest

⭐ 29.9k

기본적으로 Out of box이며 Mocha와 다르게 각 테스트 케이스는 병렬적으로 작동하므로 어느정도 속도의 향상을 가져올 수 있다.

또한 각 테스트 케이스를 독립적으로 돌리기 위해서 Virtual machine을 사용한다. 독립적이라는 장점을 가져올 수 있는 반면 테스트 매 사용시마다 모듈을 새로 Import 다시 하므로 테스트 속도가 느려질 수 있는 것으로 보이지만 Jest에서 추가적으로 제공하는 기능(setupTests.js에서 모듈을 불러오면 Global로 사용함)을 통해 해결할 수 있는 것으로 보인다.

```js
describe('Testing 1', () => {
  it('Test case title: HelloWorld', () => {
    expect(str).toBe('Hello World');
  });
});
```

  

#### 3. Enzyme

⭐ 18.5k

가상의 환경 JDOM에서 컴포넌트를 렌더링할 수 있는 기능을 제공하며 DOM과 상호작용이 정상적인지 테스트가 가능하며 주로 컴포넌트에서 어떤 값이 오가는지 판별할 때 사용되는 것으로 보인다.

```js
// https://enzymejs.github.io/enzyme/
// 컴포넌트의 props에 접근해 값을 판별한다.
describe('<Foo />', () => {
  it('allows us to set props', () => {
    const wrapper = mount(<Foo bar="baz" />);
    expect(wrapper.props().bar).to.equal('baz');
    wrapper.setProps({ bar: 'foo' });
    expect(wrapper.props().bar).to.equal('foo');
  });
});
```

  

#### 4. RTL

⭐ 10.9k

유저 관점의 보여지는 결과에 초점을 맞춤.

일반적으로 Enzyme보다 쉽다고 여겨짐.

> RTL 에서는 Enzyme 과 달리 모든 테스트를 DOM 위주로 진행합니다. 그리고, 컴포넌트의 props 나 state 를 조회하는 일은 없습니다. 컴포넌트를 리팩토링하게 될 때에는, 주로 내부 구조 및 네이밍은
> 많이 바뀔 수 있어도 실제 작동 방식은 크게 바뀌지 않습니다. 
RTL는 이 점을 중요시 여겨서, 컴포넌트의 기능이 똑같이 작동한다면 컴포넌트의 내부 구현 방식이 많이 바뀌어도 테스트가 실패하지 않도록 설계되었습니다.
 추가적으로, Enzyme 은 엄청나게 다양한 기능을 제공하는 반면, RTL 에는 정말 필요한 기능들만 지원을 해줘서 매우 가볍고, 개발자들이 일관성 있고 좋은 관습을 따르는 테스트 코드를
> 작성 할 수 있도록 유도해줍니다. [참고](https://velog.io/@velopert/react-testing-library)
```js
// https://velog.io/@velopert/react-testing-library
Profile = ({ username, name }) => {
  return (
    <div>
    <b>{username}</b>&nbsp;
    <span>({name})</span>
    </div>
  );
};
////////////////////////////////////
import React from 'react';
import { render } from 'react-testing-library';
import Profile from './Profile';

describe('<Profile />', () => {
  it('matches snapshot', () => {
    const utils = render(<Profile username="velopert" name="김민준" />);
    expect(utils.container).toMatchSnapshot();
  });

  it('shows the props correctly', () => {
    const utils = render(<Profile username="velopert" name="김민준" />);
    utils.getByText('velopert'); // velopert 라는 텍스트를 가진 엘리먼트가 있는지 확인
    utils.getByText('(김민준)'); // (김민준) 이라는 텍스트를 가진 엘리먼트가 있는지 확인
    utils.getByText(/김/); // 정규식 /김/ 을 통과하는 엘리먼트가 있는지 확인
  });
});
```

#### 5. Cypress

⭐ 18.8k

기본적으로 RTL과 비슷하며 이것 또한 사용자의 입장에서 테스트 진행.
가장 좋은 점은 각각의 테스트 케이스가 어떻게 진행됐는지 눈으로 확인이 가능하다.
한 파일 안의 테스트 케이스들은 동기적으로 실행되지만 각 페이지 별로 실행함으로써 병렬적으로 실행 가능하다.

기본적으로 DOM을 get할 때 4초간 기다림 및 자동적으로 Retry 하며 Mocha를 사용한다.
Typescript를 완벽하게 지원하지 않는다.
쉽게 익힐 수 있고 사용이 가능하다는 장점이 있는 반면 Typescript, React, Test coverage를 지원하기 위해서는 별도의 설정이 필요하다는 번거로움이 있다.

```js
describe("<Button />", function() {
  it("click", function() {
    cy.get("button").click();
  })

  it("get Text", function() {
    cy.get("button").should("have.text", "클릭!");
  })
})
```

## 실사용 예제

![trand1](./trand%20horizontal.png)

![trand2](./trand%20quadrant.png)

**우선순위**

1. 테스트가 메인은 아님으로 쉬우면서 빠르게 작성가능 하면 좋겠다.

2. Stackoverflow에 질문하면 답변이 잘 되는, 대부분이 사용하는 테스트 라이브러리이면 좋겠다.

3. Props, State의 값을 확인하기 보다 실제 DOM이 어떻게 작동되는지 테스트 하고 싶다.

### 조합 1. Cypress

**선택의 이유**

1. DOM 위주의 테스트

2. 최근 뜨겁게 떠오르고 있음

4. 단점에도 불구하고 위의 나열된 테스트 프레임워크/라이브러리 중 가장 쉽게 익힐수 있으며 일반적으로 가장 짧은 코드로 사용이 가능하다.

5. cy.get(...).should(...).and(...).should(...)와 같이 체이닝 패턴으로 쉽게 이해가 가능하며 각각의 케이스는 동기적으로 실행되 코드를 읽고 쉽게 이해가 가능하다.

6. 어떤 DOM을 선택하고 이벤트가 이뤄졌는지 쉽게 확인이 가능하다.

Cypress를 설치하면 자동적으로 폴더가 생성되는데 이 폴더의 구조를 설명한다.

```

/cypress

/fixtures - Asset을 저장하는 공간. 여기에 저장할 경우 cy.fixture(filePath)와 같은 형식으로 불러올수 있음

- example.json

  

/integration - 실제 테스트 코드가 위치하는 공간

/examples

- actions.spec.js

- aliasing.spec.jsx

- assertions.spec.tsx

//Some more files...

  

/plugins

- index.js

  

/support - 각 테스트 코드가 실행되기전 거치는 공간. Global로 무언가를 실행 시키고 싶을때 사용.

- commands.js

- index.js

```
**예제를 보려면 cypress 폴더로 이동**
**실행**: ```yarn cypress or yarn cypress:runCli```

### 조합 2. Jest + RTL

**선택의 이유**

1. DOM위주의 테스트 진행시 가장 일반적으로 사용되는 조합.

2. Enzyme 보다는 RTL이 일반적으로 더 쉽다고 여겨지므로.

  
**예제를 보려면 src/component 로 이동**
**실행**: yarn test


## 결론

**Cypress**

실제로 TodoApp 테스트 케이스 작성시에도 발견할 수 있는 (버그)[https://github.com/cypress-io/cypress/issues/6636]가 존재 하고 완벽하지 않은 Typescript 지원, Jest + RTL에 비해 많지 않은 예제, 환경 설정의 까다로움이 존재 하지만

우선순위의 첫 번째인

1. 테스트가 메인은 아님으로 쉬우면서 빠르게 작성가능 하면 좋겠다.

에 가장 알맞다고 생각됨.
그 외에 테스트 케이스를 작성하면서 재미도 있었음.