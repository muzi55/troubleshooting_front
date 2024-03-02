# 재사용성이 가능한 유틸함수를 만들때 주의해야할점

프론트단에서 페이지네이션 작업을 하던도중 반복되는 계산이 있어 유틸함수 따로 빼가지고 사용을했다
<br />
<br />

## 유틸함수 코드

```js
export const accountTableIndex = (index, currentPage) => {
  let itemIndex = 0;

  if (currentPage === 1) {
    itemIndex = index + 1;
  } else {
    itemIndex = (currentPage - 1) * 10 + (index + 1);
  }

  return itemIndex;
};
```

아이템의 인덱스와 현재 페이지의 번호를 불러와 계산을 한 후, 값을 리턴하는 유틸함수를 만들었다.

문제는 유틸함수의 기능을 추가하고 나서 부터이다.
<br />
<br />
<br />

## 변경된 유틸함수 코드

```js
export const accountTableIndex = (index, currentPage, topLength) => {
  let itemIndex = 0;

  if (currentPage === 1) {
    itemIndex = index + 1;
  } else {
    itemIndex = (currentPage - 1) * 10 + (index + 1);
  }

  return itemIndex - topLength;
};
```

새로운 파라미터로 `topLength` 가 추가되었고 이 값은 페이지네이션 되어있는 값들중 앞에서부터 먼저 보여줘야할 값들이 적힌 숫자값이다.

여기서 문제가 생겼다.

<br />
<br />
<br />

## 문제가되는 부분

기존의 유틸함수를 사용하던 함수들은 `topLength` 를 사용하지않아 마지막

```js
return itemIndex - topLength;
// return 1 - undefined
```

숫자값에서 undefined를 빼기 시작한것이다.

값은 NaN 이 나왔으며 이문제를 해결하기위해 방법을 생각해봤다.

<br />
<br />
<br />

### 1. 유틸함수의 기본 매개변수(Default Parameters) 정의

```js
export const accountTableIndex = (index = 0, currentPage = 1, topLength = 0) => {
  let itemIndex = 0;

  if (currentPage === 1) {
    itemIndex = index + 1;
  } else {
    itemIndex = (currentPage - 1) * 10 + (index + 1);
  }

  return itemIndex - topLength;
};
```

매개변수의 기본값을 정해줘서 함수를 실행시킬때 매개변수를 넣지 않아도 값이 할당되도록 변경해주었다.

<br />
<br />
<br />

### 2. 함수 합성(Composition)

```js
export const accountTableIndex = (index, currentPage) => {
  let itemIndex = 0;

  if (currentPage === 1) {
    itemIndex = index + 1;
  } else {
    itemIndex = (currentPage - 1) * 10 + (index + 1);
  }

  return itemIndex;
};

export const topAccountTableIndex = (index, currentPage, topLength) => {
  return accountTableIndex(index, currentPage) - topLength;
};
```

새로만든 함수는 기존함수의 결과값을 사용하며, 추가적인 작업을하는데 이를 통해 각 함수는 독립적으로 사용되었다

<br>
<br>
<br>

## 오늘의 깨달음

기존의 함수에서 로직을 고치는건 위험하다는걸 깨달았다.

프로젝트에는 1번의 로직을 사용했지만 1번처럼 기본 매개변수를 설정하고, 2번처럼 기존의 로직은 건들지 않고, 새로운 함수에서 호출해 독립적으로 사용해야한다고 다시금 생각하게되었다.

되도록 타입 스크립트를 사용했으면 일어나지 않았겠지만, 이런 문제가있고, 앞으로 이런문제에 대해 더욱 유연하게 대처할 수 있어 만족스럽다.
