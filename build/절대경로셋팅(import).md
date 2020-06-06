#### 내용
Node에서 소스를 import 하기 위해선 아래와 같이 상대경로를 통해 가져온다. 
```javascript
import A from '../../A'
const A = require('../../A')
```

React에서 상대경로를 절대경로로 변경하는 작업은 webpack config 통해 작업 가능하지만, 해당 기능을 사용하기 위해선 eject를 하여 webpack config를 빼야한다. 
[절대경로 셋팅 by webpack](https://eomtttttt-develop.tistory.com/231)   

 이를 하지않고 절대경로를 셋팅할 수 있는 방법은 craco 모듈을 사용하면 가능하다. 
 
##  Craco 모듈 이용한 CRA에 절대경로 셋팅
1. Craco 모듈 설치 
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


##  Craco 모듈을 storybook에 적용 
storybook에 craco를 적용하지 않으면 @XX path의 존재를 알수 없기 때문에 오류가 발생한다. 그렇기에 storybook config에 craco를 적용해야 한다.
다만 storybook에 craco를 적용할 수 있기에, craco가 제공하는 webpack config를 셋팅 할 수 있는 createWebpackDevConfig API를 작성하여
storybook에 적용하면 된다. 

 1-1. Craco config를 webpack config로 변환 작업    
 [Craco](https://www.npmjs.com/package/@craco/craco#api) API에 따르면 createWebpackDevConfig&creatwebpackProdConfig 를 사용하여 craco config가 적용이 된, webpack config를 호출 할 수 있다. 
 
 ```javascript
 const { createWebpackDevConfig } = require("@craco/craco");
 const cracoConfig= require('../craco.config');
 const webpackConfig = createWebpackDevConfig(cracoConfig);
 ```
 
 1-2. webpack Config를 Stroybook에 적용   
 [Storybook](https://storybook.js.org/docs/configurations/custom-webpack-config/) API에 따르면 storybook에 Custom Webpack config를 적용 할 수 있도록 지원하고 있다. 
 webpack를 Custom해주는 함수 webpackFinal 함수에 위에서 호출한 webpackConfig 중 절대경로를 지원하는 webpack의 resolve.alias를 적용해 주면
 내가 쓰고 싶은 절대경로 @XXX를 storybook에서 사용 가능하다. 
 ```javascript
 module.exports = {
  stories: ['../src/stories/**/*.stories.js', '../src/stories/**/*.stories.tsx'],
  addons: [
    '@storybook/preset-create-react-app',
    '@storybook/addon-actions',
    '@storybook/addon-links',
  ],
  webpackFinal: async config => {
    config.module.rules.push({
      test: /\.(ts|tsx)$/,
      loader: require.resolve('babel-loader'),
      options: {
        presets: [['react-app', { flow: false, typescript: true }]],
      },
    });
    config.resolve.extensions.push('.ts', '.tsx');
    // storybook에서 절대경로(@###)을 사용하기 위해 중요 포인트이고, 해당 alias에 craco에서 받은 alias를 
    // 반영해주면 된다. 
    // 해당 셋팅을 위해선 webpack을 알아야하고, 이 설정이 webpack의 resolve>alias를 바꾸는 작업이다. 
    Object.assign(config.resolve.alias, webpackConfig.resolve.alias);
    //config.resolve.alias.extensions.push(webpackConfig.alias);
    return config;
  },
};

 ```
