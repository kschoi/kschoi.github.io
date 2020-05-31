---
title: "babel.config.js 파일과 .babelrc 차이점"
date: 2020-05-17 15:40:00 -0400
categories: cs
---

바벨을 설정할 때, babel.config.js 파일과 .babelrc 파일의 차이점이 궁금해서 찾아보았습니다. 

## 바벨 설정 파일 포맷
바벨은 두 가지 설정 파일 포맷을 가집니다.
두 가지 파일 포맷은 단독으로 쓰일 수도 있고, 함께 쓰일 수도 있습니다.

### 1. 프로젝트 전체 구성
- babel.config.json (다른 확장자 포함)


### 2. 상대파일 구성 
- .babelrc.json (다른 확장자 포함)
- package.json 파일 안에 정의된 "babel" key

바벨은 컴파일 되는 "filename" 부터 폴더 구조를 따라 올라가며 .babelrc 파일(또는 지원되는 다른 확장자 파일)을 로드합니다.

## 어떻게 구분해서 사용해야할까?

### .babelrc
서브셋 디렉토리 또는 파일에서 특정한 플러그인이나, 변형 할 때 유용합니다. 예를 들어, 프로젝트 내에 서드파티 라이브러리가 바벨에 의해 트랜스폼되기 원하지 않을 수 있습니다.

### babel.config.json
여러 패키지 디렉토리를 가진 프로젝트에서 하나의 바벨 설정을 가져갈 때 유용합니다. 보다 보편적으로 사용됩니다.

## .js vs .json
바벨 설정 파일을 .js와 .json 두 가지 확장자 중 어떤 것을 사용하는 게 나은지 궁금했습니다.

```.js```를 사용하면 바벨 구성 api가 노출됩니다.
(https://babeljs.io/docs/en/config-files#config-function-api)  

이 경우 캐싱과 관련하여 복잡성이 증가하므로, 대부분의 케이스에서 ```.json```를 사용하는게 더 낫다고 합니다.


## 참고
- https://babeljs.io/docs/en/config-files#project-wide-configuration
- https://stackoverflow.com/questions/60288375/when-to-use-babel-config-js-and-babelrc


<style type="text/css">
@media (min-width: 64em) {
  .archive pre {
		font-size: 0.9em;
	}
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