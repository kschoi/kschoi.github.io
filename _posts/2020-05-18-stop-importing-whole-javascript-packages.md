---
title: "자바스크립트 패키지 전체를 import하지 않는 방법"
date: 2020-05-18 06:53:00 -0400
categories: cs
---

웹팩 빌드 환경에서 개발할 때 외부 패키지를 import할 일이 많습니다.
그런데 import하는 방식에 따라 빌드되는 번들 파일 사이즈가 다를 수 있습니다. 
관련하여 테스트한 내용을 미디엄에서 일부 발췌해 정리합니다.

lodash는 보편적으로 사용되는 유틸리티성 자바스크립트 라이브러리입니다.
lodash의 200개가 넘는 함수 중에서 4~5개의 피쳐만 사용하는 경우도 매우 흔합니다.

예를 들어 lodash에는 ```get```이라는 함수가 있습니다.
내포된 객체를 안전하게 접근하게 해주고, 기본값도 제공해줄 수 있습니다.

```js


const _ = require('lodash');

let employee1 = {
  name : "Jon",
  address : {
    street : "North Avenue",
    area : "DAC",
    zip : "87344",
    contact : [ 12444554, 9384847 ]
  },
  designation : "Manager"
};

let employee2 = {
  name : "Jake",
  designation : "Senior Manager"
};

function getHomeContact(employee) {
  return employee.address.contact;
}

getHomeContact(employee1); // [12444554, 9384847]
getHomeContact(employee2); // Uncaught TypeError: Cannot read property 'contact' of undefined

function getHomeContactWithLodash(employee) {
  return  _.get(employee, "address.contact", []);
}

getHomeContactWithLodash(employee1); // [12444554, 9384847]
getHomeContactWithLodash(employee2); // []
```

Lodash를 사용하면, 버그도 회피하면서 코드를 훨씬 깔끔하게 작성할 수 있습니다.
이 하나의 함수를 사용하기 위해 Lodash를 import하는 시나리오를 생각해봅시다.

### Before
![image1](https://miro.medium.com/max/700/1*mWo80yDO2FFXGiBN1nx3vg.png)

### After

1. 전통적인 방법

```import _ from ‘lodash’;```

or

```const _ = require('lodash');```

![image2](https://miro.medium.com/max/700/1*DyhARPupbP7hsoBnf6jPfw.png)

2. ES6 방법

```import { get} from 'lodash';```

or

```const { get } = require('lodash');```

![image3](https://miro.medium.com/max/700/1*7wbsdPBOpUz3NqCo6-hVog.png)


보다시피 두 가지 방법 모두 Lodash가 제공하는 200+ 함수를 모두 포함하여 번들 사이즈가 23KB가 증가하는 것을 볼 수 있습니다.

세번째 방법은 다른 결과를 보여줍니다.

3. ```get.js``` 만 import 하기

```import get from 'lodash/get';```

or

```const get = require('lodash/get');```

![image4](https://miro.medium.com/max/700/1*Wm5ILeI9hLKSpQnzkoC8bQ.png)

필요한 함수만 가져오므로써 번들 사이즈를 20kb 이상 줄였습니다.



출처 : https://medium.com/better-programming/stop-importing-whole-javascript-packages-39a5f3d4c8


<style type="text/css">
@media (min-width: 64em) {
  .archive pre,
	.archive p {
		font-size: 0.84em;
	}
}
@media (min-width: 80em) {
  .archive pre,
	.archive p {
		font-size: 0.72em;
	}
}
</style>