---
layout: post
title: "useEffect Cleanup"
featured-img: images/2021/08-02/useeffect_lg
categories: [react, hooks]
---

오늘은 useEffect `클린업(Cleanup`에 대해 다뤄 볼 예정이다.

우선 `클린업(Cleanup`을 다루기 전에 `useEffect를 어떻게 사용하는지` 잠깐 알아보자.

<br />
<br />

# useEffect의 사용법

크게 useEffect를 사용하는 방식은 `총 4가지`의 경우가 존재한다.

### 사용법은 이렇다!

**첫 번째 매개변수**로 `동작 함수`가 주어지고, **두 번째 매개변수**로 `의존성 배열`이 주어진다.

```jsx
import React, { useEffect } from 'react'

  useEffect(
    (이펙트 동작 함수)
    return () => (클린업 함수)
  }, [의존성 배열]);

// ... JSX 코드
```

<br />

### 1. 의존성 배열이 존재하지 않을 경우

```jsx
import React, { useEffect } from "react";

const UseEffect = () => {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log("의존값이 존재하지 않는 경우");
  });

  return (
    <div>
      <p>{`버튼 클릭한 횟수: ${count}`}</p>
      <button onClick={() => setCount((prev) => prev + 1)}>BUTTON</button>
    </div>
  );
};

export default UseEffect;
```

의존성 배열이 존재하지 않을 경우, 이 이펙트 함수는 `컴포넌트가 렌더링 될 때마다 매번 호출`된다.

### 🔎 실행 결과

<div align="center">
    <img src="./../assets/img/posts/2021/08-02/첫번째%20방법.gif" alt="첫번째 방법" />
</div>
<br />
<br />

## 2. 의존성 배열이 비어 있는 경우

```jsx
import React, { useState, useEffect } from "react";

const UseEffect = () => {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log("의존성 배열이 비어 있는 경우");
  }, []);

  return (
    <div>
      <p>{`버튼 클릭한 횟수: ${count}`}</p>
      <button onClick={() => setCount((prev) => prev + 1)}>BUTTON</button>
    </div>
  );
};

export default UseEffect;
```

의존성 배열이 비어있으면, 컴포넌트가 최초 마운트 되는 순간 `딱 한번만 함수가 호출`된다.

### 🔎 실행 결과

<div align="center">
    <img src="./../assets/img/posts/2021/08-02/두번째%20방법.gif" alt="두번째 방법" />
</div>
<br />
<br />

## 3. 의존성 배열에 값이 있는 경우

의존성 배열에 값이 존재하는 경우, `컴포넌트가 처음 렌더링(마운트) 될 때도 함수가 실행`되며,

그 이후에 `의존성 배열에 있는 값의 상태가 변경이 일어나는 경우` 렌더링이 발생한다.

다만, 의존성 배열에는 `컴포넌트의 상태 값`이나 `속성값`, 또는 `해당 컴포넌트 내부에서 정의된 지역 변수`나 `지역 함수` 같은 것들은 모두 의존성 배열에 작성해야 한다.

```jsx
import React, { useState, useEffect } from "react";

const UseEffect = () => {
  const [value, setValue] = useState("");
  const [char, setChar] = useState("");

  useEffect(() => {
    console.log("의존성 배열에 값이 있는 경우", `, 입력한 값 ${char}`);
  }, [char]);

  const onChangeValue = (event) => {
    const {
      target: { value },
    } = event;
    setValue(value);
  };

  return (
    <div>
      <p>{`입력한 문자: ${char}`}</p>
      <div>
        <input type="text" onChange={onChangeValue} value={value} />
        <button onClick={() => setChar(value)}>BUTTON</button>
      </div>
    </div>
  );
};

export default UseEffect;
```

### 🔎 실행 결과

<div align="center">
    <img src="./../assets/img/posts/2021/08-02/세번째%20방법.gif" alt="세번째 방법" />
</div>
<br />
<br />

## 4. return 문이 존재할 때,

useEffec t안에 return 문이 있다면 `이펙트 함수가 호출되기 전`과 `컴포넌트가 언마운트될 때 호출`되면서 이전 이펙트 함수로 인한 결과들을 정리하는 역할을 한다.

`컴포넌트가 언마운트될 때 호출`하는 동작을 `클린업(Cleanup)`이라고 한다.

단, 맨 처음 렌더링이 될 경우 클린업은 실행되지 않는다.

컴포넌트가 처음 렌더링이 된 이후에 클린업이 실행된다.

```jsx
import React, { useState, useEffect } from "react";

function UseEffect() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log("count increased!");
    return () => {
      console.log("Run Cleanup");
    };
  }, [count]);

  return (
    <>
      <div>
        <button onClick={() => setCount((prev) => prev + 1)}>+</button>
        <div>{count}</div>
      </div>
    </>
  );
}

export default UseEffect;
```

### 🔎 실행 결과

<div align="center">
    <img src="./../assets/img/posts/2021/08-02/네번째%20방법.gif" alt="네번째 방법" />
</div>
<br />
<br />

결과를 확인해보면, 맨 처음에 마운트 후 함수가 호출되고, 그 이후 return 문에 있는 함수가 먼저 호출되고 마운트 되는 것을 확인할 수 있다.

여기까지 useEffect가 리액트에서 어떻게 동작하는지 잠깐 알아보았다.

<br />
<br />

## 🚀 오늘의 핵심!

### **useEffect Cleanup를 알아보자.**

이번에 고피자 프로젝트를 리팩토링을 하는 과정에서 알수 없는 에러 메시지를 보게 되었다.

<br />
<div align="center">
    <img src="./../assets/img/posts/2021/08-02/클린업%20콘솔%20에러.png" alt="1차 테스트 안내" />
</div>
<br />

### 🔎 실행 화면

<br />
<div align="center">
    <video autoPlay loop muted src="./../assets/img/posts/2021/08-02/클린업-에러.mp4" type="video/mp4" style="width: 550px; height: 450px;"/>
</div>
<br />
<br />

### 🚫 Error 메시지 의미.

```
경고 : 마운트 해제된 구성 요소에서 반응 상태 업데이트를 수행할 수 없습니다.
이것은 작동하지 않지만 응용 프로그램의 메모리 누수를 나타냅니다. 수정하려면 useEffect
정리 기능에서 모든 구독 및 비동기 작업을 취소하십시오.
```

마운트 해제된 구성 요소에서 반응 상태 업데이트를 수행할 수 없다.

즉, `제거된 컴포넌트에서는 상태 업데이트를 할 수 없다는 뜻`이다.

<br />
<br />

## 에러를 분석해본 결과,

해당 프로젝트의 모든 페이지에서는 Header 컴포넌트를 사용하고 있으며, 이처럼 스크롤 이벤트를 사용하고 있었다.

```jsx
// 모든 페이지에서 사용하고 있는 Header 컴포넌트
useEffect(() => {
  window.addEventListener("scroll", handleChangeNav);
}, []);
```

여기서 문제는 스크롤을 하다가 갑자기 페이지를 이동하게 되면 에러 메시지가 경고 창에 뜬다.

<br />
<div align="center">
    <img src="./../assets/img/posts/2021/08-02/고민.jpeg" alt="1차 테스트 안내" />
</div>
<br />

그래서 뭐가 문제인지 이 에러를 분석해 보기로 했다.

오랫동안 구글링에서 찾은 여러 자료들과 console.log()의 강도 높은 디버깅 막노동으로부터 분석해본 결과...

이 에러는 아마도 `브라우저의 동작 원리와 연관`이 있을 것이라 판단하게 되었다.

<br />

### 그렇다면 useEffect안에 있는 `addEventListener` 때문인가?

`YES`, 아마도 맞을 것이다.

<br />

### **addEventListener는 무엇인가?**

`addEventListener`는 `비동기 동작을 하는 Web API`를 의미한다.

브라우저에서 `비동기 동작 함수`는 이렇게 동작한다.

1. 웹 애플리케이션에서 비동기 함수의 이벤트가 호출 시 바로 `Call Stack`에 쌓이지 않고, `Event Queue`로 이동하여 `Event Queue`에 순서대로 쌓인다.
2. 그러다 `Call Stack`에 있는 함수들이 순서대로 실행이 되고 더 이상 실행될 함수가 존재하지 않는다면 이때, `Event Loop`가 `Event Queue`에서 있는 비동기 동작 함수들을 하나씩 꺼내서 순서대로 `Call Stack`에 집어넣어 준다.
3. 그렇게 비동기 함수들은 순서대로 실행될 것이다.

<br />
<br />

## 그럼 다시 발생 원인을 살펴보면,

<br />

### 에러 발생 원인

1. 메인 페이지에 있는 Header 컴포넌트에서 addEventListener를 활용해 스크롤 이벤트를 사용하고 있다.
2. 스크롤을 하다가 다른 페이지로 이동했을 때 에러가 발생한다.

<br />

### 에러가 발생한 이유는?

결과부터 말하자면,

해당 페이지의 addEventListener 이벤트 함수가 해당 페이지에서 실행된 것이 아니라 `페이지 이동 후 이전 페이지의 호출했던 함수가 실행되어 이 에러가 발생한 것!`

다시 한번 동작 원리를 살펴보면,

addEventListener은 비동기 함수이기 때문에 해당 이벤트는 콜백 큐에 계속 쌓일 것이다.

즉, 해당 페이지에서 발생한 이벤트가 콜백 큐에는 계속 쌓여져 있는 상태에서 쌓여있던 이벤트가 실행하기도 전에 페이지가 이동되어 이동된 페이지에서 쌓여 있던 이벤트 리스너가 동작했을 때 이 에러가 발생한 것이다.

<br />

### 더 쉽게 일반적인 예시로 설명하자면,

아래 코드는 value 가 변경될 때마다 addEventListener로 새로운 함수를 입력하고 있다.

value 가 1 => 2 => 3 순서로 변경됐다고 가정하면,

스크롤 시 우리가 원하는 결과는 `콘솔 로그에 3이 출력`되는 것이다.

하지만 removeEventListener를 호출하지 않아서 1, 2, 3 이 각각 출력되는 것을 확인할 수 있다.

```jsx
useEffect(() => {
  addEventListener("scroll", () => console.log(value));
}, [value]);
```

이 부분이 뭐가 문제인가 생각할 수 있겠지만, 브라우저가 호출 해야하는 함수의 개수가 쓸데없이 늘어나게 되면 자연스럽게 `브라우저의 성능`은 떨어질 수가 있다.

그렇기 때문에 페이지 이동하기 전에 해당 페이지에서 실행한 비동기 함수를 제거한 후에 이동해야 한다.

<br />

### 이럴 때 useEffect의 `클린업(Cleanup`을 사용해주면 된다.

```jsx
// 모든 페이지에서 사용하고 있는 Header 컴포넌트
useEffect(() => {
  window.addEventListener("scroll", handleChangeNav);
  return () => {
    window.removeEventListener("scroll", handleChangeNav);
  };
}, []);
```

페이지 이동 시 컴포넌트는 언마운트 되고 이동한 페이지에서 다시 마운트 되어 실행된다.

이 과정에서, 컴포넌트가 언마운트 될 때 return 문안에 `removeEventListener`을 사용한 콜백 함수를 반환하여 해당 페이지에서 실행한 비동기 함수를 제거하게 된다.

제거 후 페이지 이동을 하면 에러가 사라진 것을 확인해 볼 수 있다.

### 🔎 실행 화면

<div align="center">
    <video autoPlay loop muted src="./../assets/img/posts/2021/08-02/클린업-에러-해결.mp4" type="video/mp4" style="width: 550px; height: 450px;" />
</div>
<br />
<br />

## 👨🏻‍💻 에러를 해결한 후 느낀점

이번에 useEffect의 클린업 이슈에 대해서 스스로 분석해보고 해결하는 경험을 하게 되었다.

이로 인해 미처 몰랐던 몇 가지의 리액트에서 useEffect가 어떻게 동작하는지를 익히게 되었고, 브라우저 동작 원리에 대해서도 스스로 생각해 보는 계기가 되었다.

<br />
<br />

## ✔️ 참고

[리엑트 후크(React Hook) useEffect cleanup function](https://lts0606.tistory.com/487)

[useEffect 훅에 대하여](https://merrily-code.tistory.com/6)

[ErrorNote) Warning: Can't perform a React state update on an unmounted component. This is a no-op, but it indicates a memory leak in your application. To fix, cancel all subscriptions and asynchronous tasks in a useEffect cleanup function.](https://velog.io/@ksh4820/ErrorNote-Warning-Cant-perform-a-React-state-update-on-an-unmounted-component.-This-is-a-no-op-but-it-indicates-a-memory-leak-in-your-application.-To-fix-cancel-all-subscriptions-and-asynchronous-tasks-in-a-useEffect-cleanup-function)

[Full React Tutorial #24 - useEffect Cleanup](https://www.youtube.com/watch?v=aKOQtGLT-Yk)

[](https://jacobko.info/javascript/ES6_11/)
