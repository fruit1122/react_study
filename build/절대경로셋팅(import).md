#### 내용
Node에서 소스를 import 하기 위해선 아래와 같이 상대경로를 통해 가져온다. 
```javascript
import A from '../../A'
const A = require('../../A')
```

React에서 상대경로를 절대경로로 변경하는 작업은 webpack config 통해 작업 가능하지만, 해당 기능을 사용하기 위해선 eject를 하여 webpack config를 빼야한다. 
[절대경로 셋팅 by webpack](https://eomtttttt-develop.tistory.com/231)   

 이를 하지않고 절대경로를 셋팅할 수 있는 방법은 crago 모듈을 사용하면 가능하다. 
 
##  Crago 모듈 이용한 CRA에 절대경로 셋팅
1. Crago 모듈 설치 
```
npm i --save-dev @craco/craco craco-alias
```

2. Config 설정   
  2-1. tsconfig.path.json 셋팅
  CRA에서 tsconfig.json을 사용하지만 Craco 모듈을 사용이 불가능하여 path 정보만 가진 tsconfig 단독 파일이 필요하여 새로 path파일을 하나 생성한다. 

<strong>issue) 현재 위의 baseUrl이 안 먹힘.. 찾아봐야함</strong>
```json
{
    "compilerOptions": {
      "baseUrl": "./",
      "paths": {
        "@page/*": ["src/pages/*"],
        "@type/*": ["src/type/*"],
        "@component/*": ["src/common/*"],
        "@store/*": ["src/store/*"]
      }
    }
}
```


    2-2. tsconfig.json에 tsconfig.path.json Extend 시키기
    tsconfig.json 속성에 분리된 tsconfig 속성을 적용하기 위해 extends 옵션을 사용하여 확장시킨다. 
 ```json
{
    "extends": "./tsconfig.paths.json",
    "compilerOptions": { ... }
}
```


  2-3. craco config 셋팅
```javascript
  const CracoAlias = require('craco-alias');

module.exports = {
    plugins: [
        {
            plugin: CracoAlias,
            options: {
                source: 'tsconfig',
                //내부적으로 crago에서 tsconfig 수정하는데 CRA의 tsconfig.js는 수정 못함으로 따로 생성해서 설정해야 함.
                tsConfigPath: 'tsconfig.paths.json',
            }
        }
    ]
};
```
  
  2-4. package.json 파일에서 react-script를 craco로 변경   
```
    "start": "craco start", 
    "build": "craco build",
    "test": "craco test",
    // "start": "react-scripts start",
    // "build": "react-scripts build",
    // "test": "react-scripts test",
```
