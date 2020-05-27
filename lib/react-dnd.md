##### React-DND 모듈 사용 시, 에러 발생   
- **에러** : Expected to find a valid target.
- **원인** : Drag 이벤트 발생하는 도중에 Drag List를 rerendring 하면서 react-dnd의 targetID를 잃어버리면서 생기는 오류   
- **해결방안** : Drag 이벤트 시, 계산 후, rerendering 해야하는 로직으로 event 종료 후, property의 이벤트 발생시키기 위해 setTimeout를 넣어 callStack의 
마지막에 해당 이벤트가 호출 할 수 있도록 함   

ex)
```javascript
setTimeout(()=>{
      onMoveNode && onMoveNode(type, node, dropResult.target);
  }, 0)
  ```
