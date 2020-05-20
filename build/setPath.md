##### build PATH가 /아닐 경우 셋팅 방법 (CRA 환경)[CRA Deploy 방법](https://create-react-app.dev/docs/deployment/)
- 공식 create-react-app 사이트에서는 package.json에 homepage에 해당 path를 설정하라고 지정함   
```json
"homepage": "http://mywebsite.com/relativepath",
```

- React Router 사용 시, 
Router path가 해당 build path가 아니기 때문에 BrowserRouter base path를 넣어줘야 함   
```javascript
<BrowserRouter basename="/calendar"/>
<Link to="/today"/> // renders <a href="/calendar/today">
```
