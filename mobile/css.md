##### IO Safari Mobile css 
1. hover 에러   
mobil은 기본적으로 touch 개념으로 hover 개념이 존재하지 않음.   
hover 사용 시, hover 잔재가 계속 남아있음
**해결방법 :** hover -> active psudo로 변경 

2. touch scroll [scroll 관성 관련 에세이](https://velog.io/@chading/iOS-%EC%82%AC%ED%8C%8C%EB%A6%AC-%EC%8A%A4%ED%81%AC%EB%A1%A4-%ED%9A%A8%EA%B3%BC%EB%A5%BC-%EC%A1%B0%EC%A0%95%ED%95%B4%EB%B3%B4%EC%9E%90)   
IOS에서 scroll 관성을 사용할 수 있도록 지원해주는 CSS Property    
```css
-webkit-overflow-scrolling: touch;
```
[mobile scroll 관련 블로그 글](https://blog.outsider.ne.kr/971)
