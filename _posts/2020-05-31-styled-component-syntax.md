---
title: "Styled Component Syntax 이해하기"
date: 2020-05-31 11:30:00 -0400
categories: cs
---

SSG.COM 모바일 UI 컴포넌트를 emotion.js 라이브러를 사용하여 스타일링하고 있습니다. 스타일드 컴포넌트를 작성하는 몇 개월동안, 저는 `Tagged Template` Literals syntax에 대한 이해가 없었습니다. 이 포스팅은 저처럼 "이게 되는건 알겠는데, 어떻게 되는거지?" 궁금하신 분들을 위한 글입니다. 

ES6에 완전히 익숙하지 않다면, 아래 코드가 낯설게 느껴지실 겁니다.

```jsx
const Title = styled.h1`
font-size: 1.5em;
color: ${theme.primary};
text-align: center;
{(props) => props.border && 'border-top: 1px solid #000;' }
`;
```

위 코드를 이해하려면 ES6의  `Tagged Template` Literals를 살펴봐야 합니다. 
`Tagged Template`은 백틱으로 작성한 템플릿 리터럴을 파싱한 뒤 실행되는 "함수"입니다.
아래는 이해에 도움이 될만한 간단한 샘플 코드입니다.

```jsx
// Tagged Template 샘플
// @stringArray: ${표현식} 기준으로 split된 string 배열
// @variable: 첫번째 ${표현식}
function taggedTemplate(stringArray, variable, variable2) {
  console.log(stringArray)
  // [ '첫 번째 string', '두 번째 string' ]

  console.log(variable)
  //  - 표현식1 - 

  return `${stringArray[0]}${variable}${stringArray[1]}`
}

const stringVar = " - 표현식1 - ";

const customSentence = taggedTemplate`
첫 번째 string
${stringVar} 
두 번째 string
`;

console.log(customSentence)
// 첫 번째 string - 표현식1 - 두 번째 string
```

taggedTemplate 이름을 가진 함수는 첫 번째 파라미터로 string array를 두번째 파라미터로 변수 하나를 받는 일반적인 함수입니다. 함수를 정의할 때는 특별할 것이 없습니다. 중요한 차이는 호출할 때 일어납니다.

`Tagged Template` 은 아래의 전통적인 방법 대신,
```jsx
taggedTemplate()
```

아래와 같은 포맷으로 호출합니다.
```jsx
taggedTemplate``
```

이렇게 호출하면 백틱 안쪽에 작성한 템플릿 리터럴이 파싱되는데, 내부에 작성된 `${표현식}`을 구분자로 하여 string이 split됩니다.
`Tagged Template` 의 첫 번째 파라미터로 스플리팅된 string array가 전달되고,  각 `${표현식}`의 평가값들이 두번째 파라미터부터 순서대로 전달됩니다.
전달받은 파라미터를 활용하여 원하는 어떤 방식으로든 가공하여 결과값을 리턴하면 됩니다.

### Styled Components 기본 작동 방식 흉내내기

자, 이제 Tagged Template의 기본을 이해했습니다.
조금 더 복잡한 예시를 보겠습니다.

```jsx
const theme = {
  spacing: {
    min: '2px',
    max: '24px',
  },
  colors: {
    primary: 'coral',
    secondary: 'peachpuff',
  },
}

function styled(css, ...variables) {
  const computedCss = css
    .map((chunk, index) => `${chunk}${variables[index] || ''}`)
    .join('')
  return computedCss
}

const Button = styled`
  background: ${theme.colors.secondary};
  margin-bottom: ${theme.spacing.min};
  span {
    padding: 0.25em 1em;
    color: ${theme.colors.primary};
  }
`
/* Output:
  background: peachpuff; 
  margin-bottom: 2px; 
   
  span { 
    padding: 0.25em 1em; 
    color: coral; 
  } 
*/
```

`Tagged Template`인 `styled`와 이전 샘플 코드와 차이는 `...variables` 부분입니다.
이전 샘플 코드에서는 넘어올 ${표현식}을 각각의 파라미터로 받고 가공하여 결과값을 리턴했는데, 이 코드는 전달된 모든 `${표현식}`을 스프레드 연산자를 통해  variables이라는 하나의 변수로 받고 있습니다.
이 방식은 매우 유용한데, 단순히 아래와 같이 처리가 가능하기 때문입니다.

```jsx
function styled(css, ...variables) {
  const computedCss = css
    .map((chunk, index) => `${chunk}${variables[index] || ''}`)
    .join('')
  return computedCss
}
```

### props에 접근가능한 콜백 추가하기

실제 스타일드 컴포넌트를 사용할 때는 `props`를 받아서 `Tagged Template`에 콜백을 전달할 일이 많습니다. 
Tagged Template에서 어떻게 `props`에 접근 가능할까요?
아래는 실제 이모션이나 스타일드 컴포넌트 작동 방식과는 다른 예시일 뿐입니다. 
그러나 어떤식으로 `props` 접근하는지 이해하는데 도움이 되기를 바랍니다.

```jsx
function styled(css, ...variables) {
  const theme = {
    spacing: {
      min: '2px',
      max: '24px',
    },
    colors: {
      primary: 'coral',
      secondary: 'peachpuff',
    },
  }

  const props = {
    theme,
    primary: true,
    bigSpacing: true,
  }

  const computedCss = css
    .map(
      (chunk, index) =>
        `${chunk}${variables[index] ? variables[index](props) : ''}`
    )
    .join('')
  return computedCss
}

const Button = styled`
  background: ${({ primary, theme }) =>
    primary ? theme.colors.primary : theme.colors.secondary};
  margin-bottom: ${({ bigSpacing, theme }) =>
    bigSpacing ? theme.spacing.max : theme.spacing.min};
  span {
    padding: 0.25em 1em;
    color: ${({ primary, theme }) =>
      primary ? theme.colors.secondary : '#fff'};
  }
`
/* Output:
  background: coral; 
  margin-bottom: 24px; 
 
  span { 
    padding: 0.25em 1em; 
    color: peachpuff; 
  } 
*/
```

읽는 분에 따라서 설명이 과할 수도, 부족할 수도 있을 것 같습니다.
위 내용을 통해 저는 스타일을 작성할 때 낯선 문법에서 오는 불안감이 어느 정도 해소되었고, 읽으시는 분도 그러기를 바랍니다. 또한 `Tagged Template` 이 어떤 템플릿이든지 가공이 필요할 때 강력한 기능을 제공할 수 있다는 것을 기억해두시면 좋을 것 같습니다.

## 출처

- [https://blog.jimmydc.com/tagged-templates-and-styled-components/](https://blog.jimmydc.com/tagged-templates-and-styled-components/)


<style type="text/css">
@media (min-width: 64em) {
  .archive pre { font-size: 0.85em; }
	.archive li,
	.archive p {
		font-size: 0.84em;
	}
}
@media (min-width: 80em) {
	.archive li,
	.archive p {
		font-size: 0.72em;
	}
}
</style>