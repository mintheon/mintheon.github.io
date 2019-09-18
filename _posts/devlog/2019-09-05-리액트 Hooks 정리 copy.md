---
layout: post
title: "리액트 Hooks 정리"
subtitle: ""
date: 2019-09-05 16:36:48
categories: devlog
tags: web bit
---

[리액트의 Hooks 완벽 정복하기](https://velog.io/@velopert/react-hooks)

---

리액트가 버전업이 되면서 Hooks 라는게 생겼다더라. 공부 겸 정리 글이다. 나중에 이번에 만들었던 캘린더 리팩토링도 진행해야 할 것 같다.

---

<br/>
## Hooks란?

---

- 리액트 v16.8에 새로 도입된 기능으로써, 함수형 컴포넌트에서도 상태 관리를 할 수 있는 `useState`, 렌더링 직 후 작업을 설정하는 `useEffect` 등의 기능들을 제공
- 기존의 함수형 컴포넌트에서 할 수 없었던 다양한 작업을 할 수 있게 해준다.

---

<br />
#### Hook

##### **`useState`**

- **[예제]**
  ```JSX
  import React, { useState } from 'react';
  (...)
  const [value, setValue] = useState(0);
  ```
  - 배열 비구조화 할당 문법을 사용해 들고 오는 것이다. 아래는 이해하기 쉬운 예제이다.
    ```javascript
    const array = ["dog", "cat", "sheep"]
    const [first, second] = array
    console.log(first, second) // dog cat
    ```
- 함수형 컴포넌트에서 가변적인 상태를 지니고 있을 수 있게 해주는 Hook.
- 함수형 컴포넌트에서 상태를 관리해야 하는 일이 발생한다면 사용
- 해당 함수의 파라미터에는 상태의 기본 값을 넣어준다.
- 해당 함수는 호출 후 배열을 반환한다. **첫번째 원소는 상태 값이고, 두번째 원소는 상태를 설정하는 함수이다.**

##### **`useEffect`**

- 리액트 컴포넌트가 렌더링 될 때마다 특정 작업을 수행하도록 설정하는 Hook.
- 클래스형 컴포넌트의 `componentDidMount`와 `componentDidUpdate`를 합친 형태로 보아도 무방.
- 두번째 파라미터에 아무것도 적어주지 않는다면 렌더 될 때마다 실행된다.

**1. 마운트 될 때만 실행하고 싶을 때**

```javascript
useEffect(() => {
  console.log("마운트 될 때만 실행됩니다.")
}, [])
```

- 함수의 두번째 파라미터로 비어있는 배열을 넣어주면 된다.

**2. 특정 값이 업데이트 될 때만 실행하고 싶을 때**

```javascript
useEffect(() => {
  console.log(name)
}, [name])
```

    - 함수의 두번째 파라미터로 전달되는 배열 안에 검사하고 싶은 값을 넣어 주면 된다.

**3. 뒷정리 하기**

- 컴포넌트가 언마운트되기 전이나, 업데이트 되기 직전에 어떠한 작업을 수행하고 싶다면 뒷정리(cleanup) 함수를 반환해 주어야 한다.
  ```javascript
  useEffect(() => {
    console.log("effect")
    console.log(name)
    return () => {
      console.log("cleanup")
      console.log(name)
    }
  })
  ```
      (컴포넌트가 나타날 때 콘솔에 effect가 보이고, 사라질 때 cleanup 이 보여지게 된다.)
- 오직 언마운트 될 때만 뒷정리 함수를 호출하고 싶다면 `useEffect` 함수의 두번째 파라미터에 비어있는 배열을 넣어준다.

##### **`useContext`**

**[예시]**

```javascript
import React, { createContext, useContext } from "react"

const ThemeContext = createContext("black")
const ContextSample = () => {
  const theme = useContext(ThemeContext)
  const style = {
    width: "24px",
    height: "24px",
    background: theme,
  }
  return <div style={style} />
}

export default ContextSample
```

- 함수형 컴포넌트에서 `Context`를 보다 더 쉽게 사용할 수 있도록 해준다.

##### **`useReducer`**

```JSX
import React, { useReducer } from 'react';

function reducer(state, action) {
  // action.type 에 따라 다른 작업 수행
  switch (action.type) {
    case 'INCREMENT':
      return { value: state.value + 1 };
    case 'DECREMENT':
      return { value: state.value - 1 };
    default:
      // 아무것도 해당되지 않을 때 기존 상태 반환
      return state;
  }
}

const Counter = () => {
  const [state, dispatch] = useReducer(reducer, { value: 0 });

  return (
    <div>
      <p>
        현재 카운터 값은 <b>{state.value}</b> 입니다.
      </p>
      <button onClick={() => dispatch({ type: 'INCREMENT' })}>+1</button>
      <button onClick={() => dispatch({ type: 'DECREMENT' })}>-1</button>
    </div>
  );
};

export default Counter;
```

- `useState`보다 컴포넌트에서 더 다양한 상황에 따라 다양한 상태를 다른 값으로 업데이트 해주고 싶을때 사용하는 Hook.
- `Redux`에서 액션객체에선 어떤 액션인지 알려주는 type 필드가 꼭 있어야 했지만, 해당 Hook에서 사용하는 액션 객체는 꼭 type을 지니고 있을 필요가 없다. 심지어 객체가 아니라 문자열이나, 숫자여도 상관이 없다.
  **[예시]**

  ```javascript
  import React, { useReducer } from "react"

  function reducer(state, action) {
    return {
      ...state,
      [action.name]: action.value,
    }
  }

  const Info = () => {
    const [state, dispatch] = useReducer(reducer, {
      name: "",
      nickname: "",
    })
    const { name, nickname } = state
    const onChange = (e) => {
      // 이 부분에서 이벤트 객체가 지니고 있는 e.target 값 자체를 액션 값으로 사용했다.
      dispatch(e.target)
    }

    return (
      <div>
        <div>
          <input name="name" value={name} onChange={onChange} />
          <input name="nickname" value={nickname} onChange={onChange} />
        </div>
        <div>
          <div>
            <b>이름:</b> {name}
          </div>
          <div>
            <b>닉네임: </b>
            {nickname}
          </div>
        </div>
      </div>
    )
  }

  export default Info
  ```

- 첫번째 파라미터는 리듀서 함수, 두번째 파라미터는 리듀서의 기본 state값을 넣어준다.
- 반환 값은 state 값과 dispatch 함수이다.
- 사용 시 가장 큰 장점은 컴포넌트 업데이트 로직을 컴포넌트 바깥으로 빼낼 수 있다.

##### **`useMemo`**

- 함수형 컴포넌트 내부에서 발생하는 연산을 최적화 할 수 있다.

**[예시]**

```javascript
(...)
const avg = useMemo(() => getAverage(list), [list]);
(...)
```

(list의 값이 바뀔때만 렌더링 되도록 설정)

**`useCallback`**

```

```

- useMemo와 상당히 비슷한 함수이다. 해당 Hook은 이벤트 핸들러 함수를 필요할 때만 생성 할 수 있다.
- 예를 들어 onChange와 onInsert 라는 함수를 해당 Hook을 사용하지 않고 선언을 해 주었을 때, 컴포넌트가 리렌더링 될 때마다 이 함수들이 새로 생성된다. 해당 렌더링을 최적화 하기 위해 사용한다.
  **[예시]**
  ```javascript
  const onInsert = useCallback(
    (e) => {
      const nextList = list.concat(parseInt(number))
      setList(nextList)
      setNumber("")
    },
    [number, list],
  ) // number 혹은 list가 바뀌었을 때만 함수 생성
  ```
- `useCallback`의 첫번째 파라미터에는 우리가 생성해주고 싶은 함수를 넣어주고, 두번째 파라미터에는 배열을 넣어주면 된다. (해당 배열에는 어떤값이 바뀌었을 때 함수를 새로 생성해 주어야 할지 명시)
- 함수 내부에서 기존 상태 값을 의존해야 할 경우에는 두번재 파라미터 안에 포함시켜 줘야 한다.
  (예를 들어 onChange의 경우엔 기존의 값을 조회하지 않고 바로 설정하기 때문에 문제없지만, onInsert는 기존의 number와 list를 조회하여 nextList를 생성하기 때문에 배열 안에 number와 list를 꼭 넣어 주어야 한다.)

##### `useCallback`과 `useMemo`

```javascript
useCallback(() => {
  console.log("hello world!")
}, [])

useMemo(() => {
  const fn = () => {
    console.log("hello world!")
  }
  return fn
}, [])
```

- 상단의 두 코드는 같은 코드이다.
- 숫자, 문자열, 객체 처럼 일반 값을 재사용 하기 위해서는 `useMemo`를 사용하고, 함수를 재사용 하기 위해서는 `useCallback`을 사용하자.

##### `useRef`

- 함수형 컴포넌트에서 ref를 쉽게 사용할 수 있게 한다.

1. 엘리먼트 객체로 사용하기

- `useRef`를 사용하여 `ref`를 설정하면, `useRef`를 통해 만든 객체 안의 current 값이 실제 엘리먼트를 가르키게 된다.
  **[예시]**

  ```JSX
  const inputEl = useRef(null);

  const onInsert = useCallback(
    e => {
      const nextList = list.concat(parseInt(number));
      setList(nextList);
      setNumber('');
      inputEl.current.focus();
    },
    [number, list]
  );

  <input value={number} onChange={onChange} ref={inputEl} />
  ```

2. 로컬 변수로 사용하기

- 컴포넌트 로컬 변수를 사용해야 할 때도 useRef를 활용할 수 있다.
  **[예시]**

  ```javascript
  const id = useRef(1)
  id.current = id.current + 1
  ```

- 주의할 점 : ref 안의 값은 바뀌어도 컴포넌트는 렌더링 되지 않는다.

---

<br />
#### 커스텀 Hooks

---

- 만약 여러 컴포넌트에서 비슷한 기능을 공유하게 되는 일이 발생한다면 이를 우리들만의 Hook을 작성하여 로직을 재사용 할 수 있다.

##### `usePromise`

###### usePromise.js

```javascript
import { useState, useEffect } from "react"

export default function usePromise(promiseCreator, deps) {
  const [resolved, setResolved] = useState(null)
  const [loading, setLoading] = useState(false)
  const [error, setError] = useState(null)

  const process = async () => {
    setLoading(true)
    try {
      const result = await promiseCreator
      setResolved(result)
    } catch (e) {
      setError(e)
    }
    setLoading(false)
  }

  useEffect(() => {
    process()
  }, deps)

  return [loading, resolved, error]
}
```

###### UsePromiseSample.js

```JSX
import React from 'react';
import usePromise from './usePromise';

const wait = () => {
  // 3초 후에 끝나는 프로미스를 반환
  return new Promise(resolve =>
    setTimeout(() => resolve('Hello hooks!'), 3000)
  );
};
const UsePromiseSample = () => {
  const [loading, resolved, error] = usePromise(wait, []);

  if (loading) return <div>로딩중..!</div>;
  if (error) return <div>에러 발생!</div>;
  if (!resolved) return null;

  return <div>{resolved}</div>;
};

export default UsePromiseSample;
```

- `useEffect`를 사용할 때에는 useEffect에 파라미터로 전달해주는 함수에서 async를 사용하면 안된다. 아래는 해당 예시이다.
  ```javascript
  useEffect(async () => {})
  ```
  `useEffect`에서는 뒷정리 함수를 반환해야 하는데 async를 사용하게 되면 함수가 아닌 Promise를 반환하기 때문에 오류가 발생한다.
- 다른 개발자들이 만든 Hooks도 라이브 러리로 설치하여 사용이 가능하다.

---

<br />
#### 정리

리액트에서 Hooks 패턴을 사용하면 클래스형 컴포넌트를 작성하지 않고도 대부분의 기능을 구현할 수 있다.  
이전의 방식을 모두 바꿀 필요는 없으나 새로 컴포넌트를 만들 때 Hooks를 사용하여 해결할 수 있다면 상황에 따라 유지보수성과 가독성 측면에서 더 좋을 수 있다.

---
