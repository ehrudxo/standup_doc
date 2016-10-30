# Day3

## User Story 2

```
 김개발이 사이트를 방문해서 자신이 어제 유심하게 읽은 글을 올릴 수 있다. 이렇게 하면 다른 사람들이 볼 수 있다.
  * 에디터 창은 하나만 있고 거기서 글을 작성하고 업로드 하면 글이 외부 클라우드 공간에 저장이 된다.
```

User Story2 에 맞춰 에디터를 작성해 보았습니다. 이제는 User Story의 약간 디테일한 부분을 살펴보고
그 부분에 대한 것들이 어떻게 더 풀어져 나가는지 살펴 보도록 하겠습니다.

```
에디터 창은 하나만 있고 거기서 글을 작성하고 업로드 하면 글이 외부 클라우드 공간에 저장이 된다.
```

왜 에디터 창은 하나만 있고라는 표현을 썼을까요? 대부분의 소셜미디어들은 즉각적으로 남기고 싶은 말들을 남기
고 업로드를 누르면 알아서 필요한 정보들을 이쁘게 만들어서 내어 줍니다. 그것과 유사하게 우리도 카드라는 형
태를 만들어 볼 것인데, 일단 뒷쪽에 표현된 *업로드 하면 글이 외부 클라우드 공간에 저장이 된다.* 라는 데에
조금 더 관심을 기울여 만들어 보도록 하겠습니다.

### Firebase

Firebase를 제가 처음 본 것은 Angular를 한참 공부하던 시절에 Todo App 기본 예제가 Firebase로 되
어 있었습니다. 꽤 괜찮구나 하고 생각했는데 어느새 구글이 인수, 재작년 부터는 Google I/O의 주력 상품이
되어 있더군요.

일단 사이트를 방문해 볼까요?

[Firebase 홈페이지](https://firebase.google.com/)

![Firebase 홈페이지](./img/Serverless_firebase01.png)

IBM의 cloudant, mongolab 같은 일종의 DBAAS(Database Aa A Service)라고 보시면 되는데 제공되
는 혜택이나 서비스들이 워낙 막강해서 개발하면서 고민해야 하는 많은 부분들을 줄여줄 수 있습니다.

가장 큰 장점이라고 하는 부분들(자랑하고 있는 부분)은 분석과 개발 툴입니다. 확실히 대시보드는 깔끔하고 개발
할때 언제나 필요했던 요소들을 sdk 로 제공해 주는데 대표적인 예로는 클라우드 메세징, 인증, 실시간 데이타베
이스, 저장소, 호스팅, 원격구성, Test Lab, 오류 보고 입니다.

이 중에 우리가 사용할 녀석은 아무래도 인증과 실시간 데이타베이스 이 두가지 입니다. 서버리스 아키텍처를 지향
하니까요~

<iframe width="560" height="315" src="https://www.youtube.com/embed/xAsvwy1-oxE" frameborder="0" allowfullscreen></iframe>

구글 I/O의 처음부터 앱 만들기를 보시면 상당히 도움이 되실거 같구요. 하지만 이와 별개로 저도 처음부터 시작해
보도록 하겠습니다.

#### 가입

가입은 Google ID가 있으면 가능합니다. 오른쪽 상단의 로그인 버튼을 누르고 로그인을 한뒤 로그인 버튼 옆에
있는 콘솔로 이동 버튼을 누릅니다.
![Go Console](./img/Serverless_firebase02.png)

그럼 Console의 대문에 들어가게 되는데

![Go Console](./img/Serverless_firebase03.png)

거기서 새 프로젝트 만들기를 눌러서 새 프로젝트를 만들 수 있습니다.

#### 프로젝트 만들기

다음과 같은 팝업창이 뜬 것을 확인할 수 있을 겁니다.

![Go Console](./img/Serverless_firebase04.png)

프로젝트 이름을 StandUp, 국가/지역을 대한민국으로 설정하고 프로젝트 만들기 버튼을 클릭합니다.
그러고나면 몇초 후에 다음과 같은 아름다운 대시보드 화면을 볼 수 있습니다.

![Go Console](./img/Serverless_firebase05.png)

네! 이제 끝났습니다~ 개발에 필요한 부분이 나올 때에 Firebase 의 기능은 조금 더 설명하기로 하고 가장 중
요한 한가지만 하고 작업을 진행하도록 하겠습니다.

*웹 앱에 Firebase 추가* 버튼을 클릭하면 다음과 같은 창이 뜨는데 거기에 나온 config 값을 사용합니다.

![Go Console](./img/Serverless_firebase06.png)

## 환경 설정
일단 이런 config 파일들을 사용할 js 파일을 한번 만들어 보도록 하겠습니다.

config.js 파일을 다음과 같이 만들어 줍니다.

```
var config = {
 apiKey: process.env.REACT_APP_FIREBASE_KEY,
 databaseURL: process.env.REACT_APP_DB_URL,
 storageBucket: process.env.REACT_APP_STRG_BKT,
 messagingSenderId: process.env.REACT_APP_MSG_SENDER_ID,
 embedlyKey : process.env.REACT_APP_EMBEDLY_KEY
}
export default config;
```
여기서 두가지를 설명하고 가야할 것 같습니다. 첫번째는 process.env.으로 시작하는 값들입니다. create-react-app
설명을 찾아보시면 node에서 process.env에 들어가는 값을 관리하는 것을 이용해서 process.env.REACT_APP_
로 시작하게 되는 값들은 환경변수로 사용할 수 있습니다.

그렇다면 당연히 여러분이 같이 만드신 프로젝트 루트 디렉토리에 .env 파일을 만들고
```properties
apiKey = "AIzaSyCt6rkIrXNKr9xV0l-7Ei_2m3vY6RlxdJ0"
authDomain =  "standup-4125e.firebaseapp.com"
databaseURL= "https://standup-4125e.firebaseio.com"
storageBucket= "standup-4125e.appspot.com"
messagingSenderId= "605830974241"
```
와 같이 값을 만드시면 사용할 수 있습니다.

물론 API key 값들은 어느 정도 일정 시간이 지나면 제가 바꿀 것이기는 합니다. 값은 참조하시라고 남겨두었
습니다.

이렇게 설정하고 나면 잘 가지고 오는지를 확인해 봐야겠지요?
App.js 같은 곳에 import 와 console 로그를 출력하는 소스를 집어 넣고 확인해 보도록 하겠습니다.

```Javascript
import config from './config'
console.log(config.apiKey);
```
![Go Console](./img/Serverless_firebase06.png)

훌륭하게 출력이 되는 군요. 주의할 점은 .env 에 등록한 변수는 꼭 node 서버를 재기동 시켜주
셔야 한다는 것입니다.
