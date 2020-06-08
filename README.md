# todo_project

> Todo List Project


#설치 전 사전 작업 (이미 되어있으면 안해도 무방)
* node.js 12.x 버전 설치. (npm도 같이 설치 됨)  
  
<br />


##설치 (최초 1회 실행)
1. cmd(Mac은 Terminal) 실행.
3. 프로젝트의 `package.json` 파일이 존재하는 최상단 디렉토리로 이동.
3. 아래 명령어를 입력하여 `package.json`에 정의된 node.js 의존성 라이브러리 설치.  
`npm install` 
<br />

##스크립트 명령: 로컬 개발서버 실행 (dev)
cmd에서 `package.json`이 존재하는 디렉토리로 이동 후 아래 명령어를 입력.

`npm run dev`

##소스에 문제가 없는데 개발서버 or 배포 스크립트 실행이 안되는 경우
1. `/node_modules` 디렉토리 삭제.
2. 위의 설치 과정(`npm install`)을 실행.
3. 다시 작업 시도


## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report
```

For a detailed explanation on how things work, check out the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).
