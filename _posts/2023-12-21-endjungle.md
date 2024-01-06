---
title: "정글 수료"
excerpt: ""
categories:
    - Blog
tags:
    - [Blog, Github, Git, Github.io, KAIST, 정글, SW사관학교 정글]
toc : true
toc_sticky: true
date: 2023-12-21
last_modified_at: 2023-12-21
---
# 정글 수료
막상 쓰려니까 잘 안써진다.... 집에 가서 세팅 다 하고 써야겠습니다. 
<!-- ## 🌱 주요 기능 및 서비스 이미지

<details>
<summary>화상 채팅 + 음성 채팅 + 일반 채팅 + 화면공유가 가능한 <b>모각코 룸</b></summary>
<br/>
<div align=center>
<img src='https://github.com/godjooyoung/final_fe/assets/58963027/ca3eb39b-846b-49e0-9654-96c6f52b0fd1' width='800px'>
</div>
</details>
<details>
<summary>간단한 코드를 작성하고 싶다면? 모각코 룸 내에서 <b>코드에디터</b> 제공</summary>
<br/>
<div align=center>
<img src='https://github.com/godjooyoung/final_fe/assets/58963027/d587bd5c-8a87-4462-a28f-528f308c83dc' width='800px'>
</div>
</details>
<details>
<summary>모각코 룸에서 만났는데 불량유저라면? 신고기능 혹은 헤어지기 아쉽다면, 친구 신청 </summary>
<br/>
<div align=center>
<img src='https://github.com/godjooyoung/final_fe/assets/58963027/e00d340d-727e-4337-8bf0-7c4220ef95a1' width='800px'>
</div>
</details>
<details>
<summary>내 <b>위치를 기반</b>으로 근처에 있는 개발자들과 모임 가능!</summary>
<br/>
<div align=left>
현재 접속 위치 기반 12km내의 생성된 모각코에 참여 가능합니다!
</div>
</details>
<details>
<summary>내가 얼마나 공부했더라? 마이 페이지에서 볼 수 있는 나의 통계 데이터</summary>
<br/>
<div align=center>
<img src='https://github.com/godjooyoung/final_fe/assets/58963027/186ba5e0-9a4e-4a91-831f-09caafe535d2' width='800px'>
</div>
</details>
<details>
<summary>친구와 <b>쪽지</b>를 주고 받으며 함께 모각코 인연을 이어갈 수 있도록 지원</summary>
<br/>
<div align=center>
<img src='https://github.com/godjooyoung/final_fe/assets/58963027/1a9279d5-c294-4ff2-a168-f15b5fa666ba' width='800px'/>
</div>
</details>
<details>
<summary>모각코룸에서 만나지 않았던 유저라도 <b>닉네임이나 친구코드로 친구신청</b>을 할 수 있어요!</summary>
<br/>
<div align=center>
<img src='https://github.com/godjooyoung/final_fe/assets/58963027/c6529fe6-ccc8-4be2-b5bb-639d7c260b72' width='800px'>
</div>
</details>
<details>
<summary>다른 사람은 얼마나 하나? 오늘의 Best 유저 조회 기능</summary>
<br/>
최근 일주일 공부시간과 코딩온도(ON°)로 베스트 유저 8인을 보여줘요! 모각코온 베스트유저에 도전하세요.
<br/>
<br/>
<div align=center>
<img src='https://github.com/godjooyoung/final_fe/assets/58963027/e0642da8-1578-44a3-8d5f-22a34642c30b' width='800px'>
</div>
</details>
<details>
<summary><b>코딩 온도(ON°)</b> 시스템으로 모각코에 성실히 참여한 사람인지 판단할 수 있도록 지표 제공</summary>
<br/>
<div align=left>
다른 유저의 코딩 온도(ON°)를 확인하여 얼마나 성실히 모각코를 수행한 사람인지 판단할 수 있어요!
</div>
</details>
<br/>

## 🖥️ 기술 아키텍쳐

<div align = center>
<img width="900" alt="architecture" src="https://github.com/smi-23/doldolmeet/assets/134453584/a2c18f2c-5e43-41f9-9291-2001f9db1e99">

</div>
<br/>

## ⚠️ Trouble Shooting

 <ul>
 <li>
 <table width='800px'>
  <tr>
    <th colspan="2" align="center" height="50">채팅방에 입장 후 화면 공유 시 접속한 사람은 스크린으로 공유 되는 것으로 보이나 다른 유저들에겐 공유가 되지 않는 문제</th>
  </tr>
  <tr>
<th width="70">원인</th>
    <td>스크린 publish는 잘 생성되나, 접속한 다른 사람들에게 모두 공유되는 subscribers 배열의 내부 publish는 기존 publish를 가지고 있고 업데이트를 하지 않았기 때문에 발생한 문제.</td>
  </tr>
  <tr>
    <th>시도</th>
    <td>스크린 publish가 생기면 useEffect를 통해 subscribers를 최신 상태로 업데이트 함.</td>
  </tr>
  <tr>
    <th>해결</th>
    <td>간혈적으로 스크린 publish가 성공했다가, 실패하는 현상이 발생함. subscribers를 업데이트 하는 useState의 setSubscribers를 함수형으로 처리하여서 배치처리를 막고 동기처리 되도록 수정하여 해결.</td>
  </tr>
</table>
</li>


 <li>
 <table width='800px'>
  <tr>
    <th colspan="2" align="center" height="50">신규 실시간 알림 표시가 랜더링되고 유저가 확인하면 사라지지만, 컴포넌트가 재랜더링 되면 다시 신규 실시간 알림 표시가 나타나는 문제.</th>
  </tr>
  <tr>
<th width="70">원인</th>
    <td>유저가 접속해서 서비스를 사용하는 동안 신규 알림이 발생할때마다 전역 상태의 배열에 추가해서 관리하고 있음. 컴포넌트 내부 상태값으로 알람 건수를 가지고 있는데 전역 상태에 있는 알림 건수를 초기값으로 세팅해주고 있었기 때문에 유저가 알람을 확인해서 내부 알람 건수 상태값을 0으로 초기화 하더라도 컴폰너트가 재 랜더링 되면 다시 내부 상태값을 전역에서 읽어오기 때문에 신규 알람이 발생한것 처럼 알람 표시가 나타남.</td>
  </tr>
  <tr>
    <th>시도</th>
    <td>유저가 알람 컴포넌트에서 알람을 확인하면  내부 알람 건수 스테이트를 0으로 초기화하고 전역상태의 알람 건도 초기화 하였음. 이렇게 하자 컴포넌트가 재 랜더링 되어도 전역의 상태값도 바뀌어있기 때문에 신규알림이 발생한것처럼 표시가 되는 문제는 해결되었지만 알람을 확인하기 위해 알람 창을 열자마자 알람 내역이 사라지는 문제가 발생함.</td>
  </tr>
  <tr>
    <th>해결</th>
    <td>유저가 알람 컴포넌트에서 알람을 확인하고 나면 내부 알람 건수 스테이트를 0으로 초기화하여서 신규 알림 표시 랜더링을 없애고 유저가 알람 확인창을 닫으면 전역에 저장되어있던 알람 건수도 초기화해줌으로서 해결.</td>
  </tr>
</table>
</li>
 <li>
 <table width='800px'>
  <tr>
    <th colspan="2" align="center" height="50">채팅 메세지를 보낼 경우 방을 생성한 방장이 보낸 메세지는 정상적으로 보여지나, 기존에 생성된 방에 입장한 게스트가 보낸 메세지는 두번씩 보이는 문제.</th>
  </tr>
  <tr>
<th width="70">원인</th>
    <td>게스트로 입장한 사람은 실시간 채팅 구독이 두번씩 되는 것이 원인이었음.</td>
  </tr>
  <tr>
    <th>시도</th>
    <td>기존 코드에서는 의존성 배열에 ‘화상채팅방 세션 ID’값을 둔 useEffect에서 실시간 채팅 구독을 시도하고있었다. 게스트로 입장한 유저의 경우 최초 랜더링 될때와 화상채팅방이 연결되면서 화상채팅방 세션 ID이 생성될 때 총 두 번 useEffect가 실행되는 것으로 파악하고 의존성 배열의 값을 비운채로 구독을 시도하는 것으로 변경하였다. useEffect의 의존성 배열때문에 두번 구독되는 것이라고 생각하였는데 수정을 하여도 문제는 계속 되었다.</td>
  </tr>
  <tr>
    <th>해결</th>
    <td>openVidu 화상 채팅 ID 값과 동일한 값으로 실시간 채팅을 구독하는 형태로 로직을 구현해야했었기 때문에 화상 채팅을 위한 ID 상태값 (mySessionId)와 그 값을 담아 실시간 채팅을 구독하기 위한 ID 상태값(openviduSessionId)을 두어 두개의 상태로 관리하였다.
둘 중 하나의 값이 존재할 경우 해당 값으로 실시간 채팅을 구독하도록 로직을 작성하였으나,
방장의 경우 화상채팅 ID 값만 존재하여 한번만 실시간 채팅을 구독하지만 방이 생성된 뒤에 입장하는 게스트의 경우 방에 입장하기위해 사용한 화상채팅 ID값과 이를 복사하여 가지고있는 실시간 채팅을 구독하기 위한 ID 상태값이 모두 존재하여 실시간 채팅이 두번 구독되는 문제가 발생하였다.
이를 해결하기 위해 방을 만든 유저인지, 게스트로 입장한 유저인지 구분하는 상태값을 두어 각각의 경우에도 모두 한번만 실시간 채팅을 구독하도록 변경하여 해결하였다.</td>
  </tr>
</table>
</li>

</ul>

<details>
<summary>프론트앤드 트러블 슈팅 더보기</summary>
<br/>
<div>
 <ul>
 <li>
 <table width='800px'>
  <tr>
    <th colspan="2" align="center" height="50">SSE 실시간 알림 기능을 구현하기 위해 EventSource를 사용해서 구독 요청을 보냈지만 구독이 되지 않는 문제</th>
  </tr>
  <tr>
<th width="70">원인</th>
    <td>구독하기 위해 토큰값을 서버에 함께 넘겨주어야 하는데 토큰이 제대로 넘어가지 않고 있었다.</td>
  </tr>
  <tr>
    <th>시도</th>
    <td>헤더에 토큰을 담는 키 값이 잘못된 것인지 확인, 헤더에 토큰을 담기 위해 필요한 옵션값을 주었지만 문제는 여전히 발생하였다.
토큰의 문제인지 명확히 확인하기 위해서 헤더가 아니라 url 쿼리파라미터로 토큰을 넘기는 형ㅇ태로 구현하니 구독이 잘 되었다.
하지만 쿼리파라미터로 토큰을 넘기는 것에 대한 거부감으로 인해 헤더에 담아서 넘기는 것으로 구현을 하고자 하였다.</td>
  </tr>
  <tr>
    <th>해결</th>
    <td>EventSource 객체 자체에는 헤더에 접근할수있는 메소드가 존재하지 않기 때문에 헤더에 접근이 가능한 라이브러리인
    EventSourcePolyfill를 적용해서 해결함.</td>
  </tr>
</table>
</li>


 <li>
 <table width='800px'>
  <tr>
    <th colspan="2" align="center" height="50">채팅방에 입장한 뒤 화면을 공유하거나, 다시 카메라를 공유하는등 publicsher를 변경하면 간혹 공유가 아예 안되고 에러가 발생하는 문제가 발생함.</th>
  </tr>
  <tr>
    <th width="70">원인</th>
    <td>작은 화면으로 사용자들에게 다른 유저의 화면을 공유하는 컴포넌트에 props로 변경된 화면을 내려주고 표시해주는데 이때 내려받은 프롭스를 찾을수 없어서 생기는 문제.</td>
  </tr>
  <tr>
    <th>시도</th>
    <td>커넥션을 먼저하고 난 다음에 세션을 찾아야 한다는 에러가 발생해서 커넥션을 먼저하기 위해서 스테이트 배치동작으 막고 동적으로 처리할수 있도록 함수형으로 스테이트를 변경해주어도 랜덤으로 동일한 문제가 발생함.</td>
  </tr>
  <tr>
    <th>해결</th>
    <td>다른 유저의 화면을 공유하는 컴포넌트에 props로 내려주는 작업을 할때 && 연산자로 우선 publicsher가 있는지 확인하고 내려줌으로서 해결함. </td>
  </tr>
</table>
</li>


</ul>
</div>
</details>


## ⛳ 기술적 의사결정
- `monaco-editor`
    - 개발 공부를 하는 서비스 이기 때문에 코드 에디터의 필요성을 느껴서 도입
    - Monaco Edito,를 사용하게된 이유는 CodeMirror, Ace Editor와 같이 다양한 코드 에디터들이 있지만 우리 프로젝트는 많은 언어와 호환이되고 자동완성 기능도 적용이 되는 Monaco Editor를 선택
- `char.js`
    - 사용자의 마이페이지에서 모각코를 함에 있어서 동기부여를 해줄 수 있는 정보들을 시각화 해서 보여주기 위해 도입
    - 매일매일 기록되는 모각코 참여시간을 차트의 형태로 한눈에 쉽게 볼 수 있도록 하도록 결정.
    - 다양한 차트 라이브러리 중에서 최근까지 계속해서 업데이트 되고, 많은 사용 레퍼런스가 있는 chart.js가 빠른 개발 기간 내에 적용하기 좋을 것이라고 판단하여 도입함.
- `react-toastify`
    - 커스텀 모달을 사용해서 사용자에게 alert 와 confirm 메세지를 안내하나 사용자의 액션이 별도로 필요없는 메세지를 보여줄때는 스낵바 형태의 메세지가 필요하기 때문에 도입.
- `Geolocation Api, KakaoLocal, KakaoMap Api`
    - Geolocation api 를 사용해 사용자의 접속위치를 받아올 수 있기 때문에 도입.
    - 생성된 방 정보들을 지도에 표시해주기 위해 카카오 로컬과 카카오 맵 api 도입.
- `redux-thunk`
    - 사용자가 전역의 상태값을 바꿀때마다 해당 좌표로 접속한 동네명을 받아오는 외부 api 요청 처리가 필요했다. 매번 요청을 하기전에 다른 요청을 하는 것보다 청크로 중간에 외부 api 요청을 하는 단계를 둘 필요성을 느껴 도입.
- `react-query`
    - 쿼리 캐싱 처리 및 클라이언트 단에서 관리해야 할 상태값을 줄이기 위해 도입
    - 데이터 로딩, 에러 처리 등이 용이하여 유지보수가 편하여 도입
- `styled-component`
    - 스타일을 컴포넌트화 하여 프롭스로 값을 내려주어 조건부 스타일을 하기에 용이하여 도입.
- `react-router-dom`
    - React Router dom을 통해 페이지 이동을 구현하였고 outlet을 통해 전체 레이아웃을 쉽게 설정할 수 있어 도입.
- `react-redux-toolkit`
    - 리액트 전역 상태관리 라이브러리로  프롭스 드릴링 현상을 대비하고 전역으로 상태를 관리하기 위해 도입.
    - 리덕스 보다 설정이 간단하고, 리코일을 새로 배우는거보다 팀원 전체가 러닝커브가 짧을것 같아서 도입
- `vercel`
    - git 커밋을 트리거로 자동으로 빌드되고 배포되기 때문에 AWS S3 보다 배포 과정이 빠르고 용이해서 적용하였음.
    - .env등 환경설정파일을 쉽게 등록하고 관리할 수 있다.

## 🔎 기술적 도전
1. webRTC - OpenVidu
2. 실시간 프로토콜 - 웹소캣, SSE
3. 코드 최적화 - redux-thunk
4. 웹사이트 최적화 - Lighthouse
5. 상태 관리 라이브러리 - redux toolkit

### 라이트 하우스 적용 과정
 <table width='800px'>
  <tr>
    <th>최적화 전</th>
    <th>최적화 후</th>
  </tr>
  <tr>
    <td>
      <img width="530" alt="라이트하우스_레포트_52" src="https://github.com/godjooyoung/final_fe/assets/58963027/12bf6733-09c2-4f3f-867b-30003a599b92"/>
    </td>
    <td>
      <img width="530" alt="라이트하우스_레포트_85" src="https://github.com/godjooyoung/final_fe/assets/58963027/80391997-dfca-4097-ab47-b4948ace4e05"/>
    </td>
  </tr>
</table>

- 웹사이트 구현 후 라이트 하우스로 웹페이지 성능을 측정 해보았다.
- Perfomance는 웹페이지의 로딩 과정에서 발생하는 성능 지표이다.
- 사용자가 느끼기에 가장 성능의 차이가 확실하게 드러날것 같은 부분이라 빠른 기간 내에 성능 개선을 위해 Perfomance 개선을 집중하였다.
- First Contentful Paint(FCP)
    - 페이지가 로드될 때 브라우저가 DOM 첫번째 부분을 랜더링 하는데 걸리는 시간
    - 최적화 전 : 2.2초
    - 최적화 후 : 0.9초
- Speed Index (SI)
    - 페이지 로드 중 컨텐츠가 시각적으로 나타나는 속도
    - 전체적으로 더욱 빨리 로드 될 수록 개선됨
- Largest Contentfull Paint(LCP)
    - 화면 내에 있는 가장 큰 이미지나, 텍스트 요소가 랜더링되기까지 걸리는 시간 지표
    - 전체 점수에서 25%의 가중치를 가지는 중요한 지표
    - 최적화 전 : 19.8초
    - 최적화 후 : 2.5초
    - 최적화 전 헤더 백그라운드 이미지는 PNG 확장자로 용량이 비교적 큼
    - 폰트 역시 ttf로 적용되어 있어서 메인의 가장 큰 텍스트 요소의 랜더링 속도가 다소 느렸음
    - 최적화 후 헤더 백그라운 이미지를 webp 확장자로 변경하였음
    - 폰트도 woff로 변경하였으나 개선이 미비하여 다시 subset font를 적용하였다.


## 👧🏻 유저 피드백 반영 사항
- [x] 쪽지 기능 강화 (삭제 추가)
- [ ] 회원가입 인증 이메일 추가
- [ ] 디자인 라이트모드 -->