---
title: "webpack 기본 이해하기"
date: 2020-05-04 15:40:00 -0400
categories: webpack
---

webpack은 서로 연관 관계가 있는 웹 자원들을 js, css, img와 같은 스태틱한 자원으로 변환해주는 모듈 번들러입니다.
자원들을 최적화해서 압축 등 웹페이지의 성능을 끌어올려줍니다.

## webpack 철학

1. 모든것이 모듈이다. - 모든 웹 자원(js, css, html)이 모듈 형태로 로딩 가능

```js
require("base.css");
require("main.js");
```

2. 불필요한 것을 로딩하지 않는다. - 필요할 때 필요한 것만 로딩!

## CLI

#### install

```js
npm i webpack webpack-cli -g
npm i webpack webpack-cli
```

#### build

```js
webpack
webpack src/index.js -o dist/bundle.js
webpack src/index.js --output dist/bundle.js --mode=development --display-modules
webpack --display-modules // 숨겨진 모듈 표시
webpack --watch // webpack에서 파일의 변경이 일어나면 자동으로 번들링을 다시 진행
webpack -d      // sourcemap 포함하여 빌드
webpack --display-error-details // error 발생 시 디버깅 정보를 상세히 출력
```

# webpack.config.js

```js
// default
var path = require("path");

module.exports = {
	mode: "", // 빌드 모드. development || production || none
	entry: {}, // 시작점
	output: {}, // 결과물
	module: {}, // loader. 특정 파일 형식을 인식
	plugins: [], // Output 시점에 관여하는 커스텀 기능
	resolve: {}, // 모듈, chunk 해석 방식을 정의
};
```

## entry

- 시작점

```js
// 여러가지 Entry 유형
var config = {
    // #1 - 간단한 entry 설정
    entry: './src/index.js'
    // #2 - 앱 로직용, 외부 라이브러리용
    entry: {
        app: './src/app.js',
        vendors: './src/vendors/js' //third-party libarary
    }
    // #3 - 페이지당 불러오는 js 설정
    entry: {
        pageOne: './src/pageOne/index.js',
        pageTwo: './src/pageTwo/index.js',
        pageThree: './src/pageThree/index.js',
    }
}
```

## output

- 결과물

```js
// entry에서 설정하고 묶은 파일의 결과값을 설정
var path = require("path");
module.exports = {
	entry: {
		//...
	},
	output: {
		path: path.resolve(__dirname, "dist"), //__dirname은 현재 모듈의 현재 폴더 위치
		filename: "bundle.js",
		// filename: '[name].js'
	},
};
```

#### path.join() & path.resolve()

```js
path.join("/foo", "bar", "baz/asdf"); // 해당 OS의 파일구분자를 이용하여 위치 조합
// Returns: '/foo/bar/baz/asdf'

// resolve는 오른쪽 인자부터 시작해서 절대 경로가 만들어질 때까지 명시된 경로를 합쳐나감
// 만약 결과값이 유효하지 않으면 "현재 디렉토리"가 사용. 반환되는 위치값은 항상 absolute URL.
// 사용하는 이유 : 여러 로더들이 복잡하게 되면, 유효하지 않은 위치가 반환될때 해결해주고, 검증된 url을 반환.
path.resolve("/foo/bar", "./baz");
// Returns: '/foo/bar/baz'

path.resolve("/foo/bar", "/tmp/file/");
// Returns: '/tmp/file'

path.resolve("wwwroot", "static_files/png/", "../gif/image.gif");
// if the current working directory is /home/myself/node,
// this returns '/home/myself/node/wwwroot/static_files/gif/image.gif'
```

https://nodejs.org/api/path.html

#### Output filename options

```js
// 복수개 entry point에 대한 [name] 예시
module.exports = {
	entry: {
		Profile: "./profile.js",
		Feed: "./feed.js",
	},
	output: {
		path: "build",
		filename: "[name].js", // 위에 지정한 entry 키의 이름에 맞춰서 결과 산출
		filename: "[hash].js", //특정 빌드에 따른 output 파일명 생성
		filename: "[chunkHash].js", //chunk에 따른 output 파일명 생성 (공식사이트 권고 - 특정 빌드에 연연하지 않아도 되고, chunk에 따라 추적이 쉬워짐)
	},
};
// 번들파일 Profile.js를 <script src="build/Profile.js"></script>로 html 삽입한다.
```

## loader

- 웹팩은 자바스크립트 파일만 처리가 가능하도록 되어 있다.
- 다른 형태의 웹 자원들 (img, css, ...) js로 변환하여 로딩하기 위해 loader가 필요하다.

```js
module.exports = {
	mode: "",
	entry: {},
	output: {},
	module: {
		rules: [
			{
				test: /\.css$/,
				use: ["style-loader", "css-loader"],
			},
			{
				test: /backbone/,
				use: [
					"expose-loader?Backbone",
					"imports-loader?_=underscore,jquery",
					// 순서대로 (1) jquery, (2) underscore 로딩
				],
			},
			{
				test: /\.js$/,
				use: [
					{
						loader: "bable-loader",
						options: {
							presets: [
								["es2015", "react", { modules: false }], // Tree shaking : 쓰지 않는 모듈은 추가하지 않음
							],
						},
					},
				],
			},
			{
				test: /\.css$/,
				use: [{ loader: MiniCssExtractPlugin.loader }, "css-loader"],
			},
		],
	},
};
```

## plugins

- Output 시점에 관여하는 커스텀 기능

```js
var MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
	mode: "",
	entry: {},
	output: {},
	module: {},
	plugins: [
		// CSS 파일을 따로 분리하여 번들링
		new MiniCssExtractPlugin({
			filename: "style.css",
		}),
		// 모든 모듈에서 사용할 수 있도록 해당 모듈을 변수로 변환
		new webpack.ProvidePlugin({
			$: "jquery",
		}),
		// 번들링 시작 시점에 사용 가능한 상수들을 정의
		// 일반적으로 개발계 & 테스트계에 따라 다른 설정을 적용할 때 유용
		new webpack.DefinePlugin({
			PRODUCTION: JSON.stringify(true),
			VERSION: JSON.stringify("5fa3b9"),
			BROWSER_SUPPORTS_HTML5: true,
			TWO: "1+1",
			"typeof window": JSON.stringify("object"),
		}),
		// 번들링시 생성되는 코드 (라이브러리)에 대한 정보를 json 파일로 저장하여 관리
		new ManifestPlugin({
			fileName: "manifest.json",
			basePath: "./dist/",
		}),
		// ...
	],
};
```

## resolve

- 모듈 번들링 관점에서 봤을 때, 모듈 간의 의존성을 고려하여 로딩해야 함.
- 따라서, 모듈을 어떤 위치에서 어떻게 로딩할지에 관해 정의하는 것

```js
// webpack.config.js
module.exports = {
	mode: "",
	entry: {},
	output: {},
	module: {},
	plugins: [],
	resolve: {
		// 별칭 지정
		alias: {
			Utilities: path.resolve(__dirname, "src/path/utilities/"),
		},
		// require(), import '' 등의 모듈 로딩 시에 어느 폴더를 기준할 것인지 정하는 옵션
		modules: ["node_modules"], // defaults
		modules: [path.resolve(__dirname, "src"), "node_modules"], // src/node_modules
	},
};
```

```js
// index.js
// 일반
import Utility from "../../src/path/utilities/utility";
// alias 사용 시
import Utility from "Utilities/utility";
```

## webpack-dev-server

- 페이지 자동고침을 제공하는 webpack 개발용 node.js 서버
- 노드 인스턴스를 하나 띄운 다음에 서버 프로토타이핑이 가능 (개인 프로젝트 권고)

#### 특징

1. 프로토타입이 정말 빠르다.
2. 서버 인메모리 컴파일 : 물리적인 파일패스로 접근할 수 없다. (가시적으로 볼 수 있는 파일 결과물이 없다.)

#### 설치

```js
npm i --save-dev webpack-dev-server
```

#### 실행

```js
webpack-dev-server --open
// 또는 package.json scripts에 명령어 등록하여 간편 실행. cli 커스텀 명령어.
"scripts": { "start": "webpack-dev-server --open --watch-content-base" }
```

```js
// webpack.config.js
module.exports = {
    ...
    devServer: {
        contentBase: path.resolve(__dirname),
        publicPath: "/dist/",
        watchContentBase: true,
        port: 9000
    },
}
```

#### path vs public path

webpack dev server의 path와 publicPath를 반드시 구분하여 파악해야 함

- output.path : 번들링한 결과가 위치할 번들링 파일의 절대 경로
- output.publicPath : 브라우저가 참고할 번들링 결과 파일의 url 주소를 지정. (CDN을 사용하는 경우 CDN 호스트 지정)

```js
// publicPath 예제 #1
output: {
    path: "/home/proj/public/assets",
    publicPath: "/assets/"
}

// publicPath 예제 #2
output: {
    path: "/home/proj/public/assets/[hash]",
    publicPath: "http://cdn.example.com/assets/[hash]/"
}

// Development: both server and the image are on localhost
.image {
    background-image: url('./test.png');
}
// Production: Server is on XXX but the image is on CDN
.image {
    background-image: url('https://someCDN/test.png');
}
```

## devtool

#### sourcemap 활용

```js
module.exports = {
    ...
    devtool: '#inline-source-map'
}

// 크롬 브라우저 : 개발자도구 -> 설정 -> preferences -> 소스 -> 소스맵 활성화
```

## gulp 연동

```js
// gulp와 webpack 모두 node.js 기반이기 때문에 통합해서 사용하기 쉽다
var gulp = require("gulp");
var webpack = require("webpack-stream");
var webpackConfig = require("./webpack.config.js");

gulp.task("default", function () {
	return gulp.src("src/entry.js").pipe(webpack(webpackConfig)).pipe(gulp.dest("dist/"));
});
```

## webpack-dev-middleware

- 기존에 구성한 서버에 webpack에서 컴파일한 파일을 전달하는 middleware wrapper
- webpack에 설정한 파일을 변경시, 파일에 직접 변경 내역을 저장하지 않고 메모리 공간을 활용한다.
- 따라서, 변경된 파일 내역을 파일 디렉토리 구조안에서는 확인이 불가능하다.
- 이미 노드를 쓰고 있을때, 웹팩을 붙이는 방법

```js
// Options

// webpack 번들링한 파일들이 위치하는곳. 디폴트값은 /
publicPath: "/assets/",
// 항상 '/'를 앞뒤에 붙여야 한다.

// 서버가 로딩할 static 파일 경로를 지정. default 값은 working directory
// 절대 경로를 사용할 것
contentBase: path.join(__dirname, "publick"),
// 비활성화
contentBase: false,

//gzip 압축방식을 이용하여 웹 자원의 사이즈를 줄인다.
compress: true
```

[추가옵션](https://webpack.js.org/configuration/dev-server/)

<style type="text/css">
@media (min-width: 64em) {
  .archive pre { font-size: 0.8em; }
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
