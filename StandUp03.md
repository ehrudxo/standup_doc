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

*왜 에디터 창은 하나만 있고* 라는 표현을 썼을까요? 대부분의 소셜미디어들은 즉각적으로 남기고 싶은 말들을 남기
고 업로드를 누르면 알아서 필요한 정보들을 이쁘게 만들어서 내어 줍니다. 그것과 유사하게 우리도 카드라는 형
태를 만들어 볼 것인데, 일단 뒷쪽에 표현된 *업로드 하면 글이 외부 클라우드 공간에 저장이 된다.* 라는 데에
조금 더 관심을 기울여 만들어 보도록 하겠습니다.

### Firebase

Firebase를 제가 처음 본 것은 Angular를 한참 공부하던 시절에 Todo App 기본 예제가 Firebase로 되
어 있었습니다. 꽤 괜찮구나 하고 생각했는데 어느새 구글이 인수, 재작년 부터는 Google I/O의 주력 상품이
되어 있더군요.

일단 사이트를 방문해 볼까요?

[Firebase 홈페이지](https://firebase.google.com/)

![Firebase 홈페이지](../img/Serverless_firebase01.png)

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
![Go Console](../img/Serverless_firebase02.png)

그럼 Console의 대문에 들어가게 되는데

![Go Console](../img/Serverless_firebase03.png)

거기서 새 프로젝트 만들기를 눌러서 새 프로젝트를 만들 수 있습니다.

#### 프로젝트 만들기

다음과 같은 팝업창이 뜬 것을 확인할 수 있을 겁니다.

![Go Console](../img/Serverless_firebase04.png)

프로젝트 이름을 StandUp, 국가/지역을 대한민국으로 설정하고 프로젝트 만들기 버튼을 클릭합니다.
그러고나면 몇초 후에 다음과 같은 아름다운 대시보드 화면을 볼 수 있습니다.

![Go Console](../img/Serverless_firebase05.png)

네! 이제 끝났습니다~ 개발에 필요한 부분이 나올 때에 Firebase 의 기능은 조금 더 설명하기로 하고 가장 중
요한 한가지만 하고 작업을 진행하도록 하겠습니다.

*웹 앱에 Firebase 추가* 버튼을 클릭하면 다음과 같은 창이 뜨는데 거기에 나온 config 값을 사용합니다.

![Go Console](../img/Serverless_firebase06.png)

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
![Go Console](../img/Serverless_firebase07.jpg)

훌륭하게 출력이 되는 군요. 주의할 점 두가지는 create-react-app 의 react-scripts 버전이 0.5.0
이상이어야 하며 .env 에 등록한 변수는 꼭 node 서버를 재기동 시켜주셔야 인식 한다는 것입니다.

## HTML 편집기 작성
이제 기본적으로 HTML 편집기 에 들어갈 몇가지 유저스토리를 추가해서 작성해 보도록 하겠습니다.
```
 김개발이 사이트를 방문해서 자신이 어제 유심하게 읽은 글을 올릴 수 있다. 이렇게 하면 다른 사람들이 볼 수 있다.
  * 에디터 창은 하나만 있고 거기서 글을 작성하고 업로드 하면 글이 외부 클라우드 공간에 저장이 된다.
  * 아무런 내용도 김개발이 입력하지 않으면 업로드하지 않는다.( 버튼이 눌러지지 않는다. )
```

유저 스토리에서 *에디터 창은 하나만* 있지만 개발을 하면서 데이타를 저렇게 저장할 수는 없을테니 어떻게 구
조화 할지 고민을 일단 한번 해 보도록 합시다. 소셜미디어데 들어가는 데이타를 다시한번 생각해 보면 아래와
같은 형태로 구조화 되어 있지 않을까 확인할 수 있습니다.

```
글 내용
작성자
URL
  - 링크 타이틀
  - 링크 요약
  - 링크 이미지
```

글 내용은 사용자가 작성한 내용을 그대로 넣으면 되고, 작성자는 일단 Anonymous(겐지)로 지정하면 되는데
URL의 아래 내용은 어떻게 할까요? 관련 내용은 다음번에 조금더 기술하도록 하고 오늘은 일단 URL만 글 내용
에서 분리하는 작업을 해 보겠습니다.

Editor.js 파일의 innerEdit 클래스에 이벤트를 한번 추가해 보도록 하겠습니다.
```html
<div className="innerEdit"
            contentEditable="true"
            placeholder="글쓰기..."
            onPaste={this.onPaste}
            onKeyUp={this.editorChange}></div>
```
onKeyUp 이벤트는 URL을 입력하고 엔터나 스페이스를 쳤을 때 패턴 매칭을 통해 URL을 뽑아내고 onPaste
이벤트는 복사 붙여넣기 할때의 이벤트를 잡아내는 역할을 합니다.
```JavaScript
onPaste(event){
  event.clipboardData.items[0].getAsString(text=>{
    if(this.detectURL(text)){
      this.setState({embedlyUrl:text,content:this.state.content}});
    }
  })
}
editorChange(event){
  let checkText = this.detectURL(event.currentTarget.textContent);
  if(!this.state.embedlyUrl&&
      (event.keyCode===32||event.keyCode===13)&&
      checkText){
    this.setState({embedlyUrl:checkText,content:event.currentTarget.textContent});
  }else{
    this.setState({content:event.currentTarget.textContent});
  }
}
```
onPaste와 editorChange는 모두 event 에서 값을 받지만 text 값을 얻어내는 방법이 조금씩 다릅니다.
onPaste는 클립보드의 아이템에서 값을 getAsString이라는 함수를 통해 얻어내는데 콜백함수의 매개 변수로
화살함수를 사용했습니다.
(여러번 ES2015 문법에 대해서 설명을 드렸지만 화살함수는 this를 의도적으로 지정할 수 있고 실제 함수를 풀
어 쓰면 다음과 같은 형태의 함수로 바꿀 수 있습니다.)
```JavaScript
onPaste(event){
  var that = this;
  event.clipboardData.items[0].getAsString(function(text){  
      if(that.detectURL(text)){
        that.setState({embedlyUrl:text});
      }
  }) 
}
```
그리고 onPaste와 editorChange 모두 편집기의 텍스트 값에 들어가는 URL을 받아서 state 값에 저장을
하는 역할을 하고 있습니다. 하지만 content에 들어가는 값을 얻어내는 과정은 두가지가 조금 다릅니다.
먼저 editorChange 함수는 엔터와 스페이스바를 입력했을때
```
event.keyCode===32||event.keyCode===13
```
편집기의 값을 확인하는 한편 onPaste는 event의 클립보드에서 텍스트를 잡아내는 거라 데이타를 완성하는
부분이 다릅니다.

지금까지의 Editor.js 소스는 다음과 같습니다.
```JavaScript
class Editor extends Component {
  /*사용되는 메쏘드들을 모두 this 로 사용할 수 있도록 바인딩 해 준다.*/
  constructor(props){
    super(props);
    this.handleSubmit = this.handleSubmit.bind(this);
    this.onPaste = this.onPaste.bind(this);
    this.editorChange = this.editorChange.bind(this);
    this.getCard = this.getCard.bind(this);
    this.hasValue = this.hasValue.bind(this);
    //embedlyUrl과 content로 분리해 준다. 사용자 추가 전
    this.state ={
      embedlyUrl : undefined,
      content : undefined
    }
  }
  // 복사 붙여넣기에 사용되는 이벤트를 다뤄준다.
  onPaste(event){
    //클립보드 아이템의 첫번째 배열에서 text 를 받는다.
    event.clipboardData.items[0].getAsString(text=>{
      //원래는 text가 String 형태인지 확인해 봐야하지만 getAsString이라 에러보다는 비정상 작동
      //이 이루어질 수 있다.
      // detectURL 이라는 dummy function 이 필요하다.
      if(detectURL(text)){
        //content 의 state는 이미 붙여진 상태 이후기 때문에 state를 그대로 가져와도 됨
        this.setState({embedlyUrl:text});
      }
    })
  }
  editorChange(event){
    // detectURL 이라는 dummy function 이 필요하다.
    let checkText = detectURL(event.currentTarget.textContent);
    if(!this.state.embedlyUrl&&
        (event.keyCode===32||event.keyCode===13)&&
        checkText){
      this.setState({embedlyUrl:checkText,content:event.currentTarget.textContent});
    }else{
      this.setState({content:event.currentTarget.textContent});
    }
  }
  getCard(embedlyUrl){
    if(embedlyUrl){
      return(
        <div>{embedlyUrl}</div>
      );
    }else{
      return(<div/>);
    }
  }
  hasValue(value){
    return (!value)?false:(value.trim()===""?false:true);
  }
  handleSubmit(event){
    this.props.submit();
  }
  //일단은 Dummy 함수가 필요하다.
  detectURL(text){
    return undefined;
  }
  render() {
    return (
      <div className="wrapEditor">
        <Profile isAnonymous={this.props.isAnonymous}/>
        <div className="textEditor">
          <div className="innerEdit"
            contentEditable="true"
            placeholder="글쓰기..."
            onPaste={this.onPaste}
            onKeyUp={this.editorChange}></div>
          {this.getCard(this.state.embedlyUrl)}
        </div>
        <div className="actionBar">
          <button className="upload"
            disabled={!this.hasValue(this.state.content)}
            onClick={this.handleSubmit}><span>스탠드업!</span></button>
        </div>
      </div>
    );
  }
}
```
하지만 공통적으로 어떤 URL을 찾아내는 작업을 한번
//테스트 코드 시작.
detectURL은 어떤 작업을 통해 URL을 뽑아내는 역할을 하는 함수라고 미리 가정을 해 보고 이렇게 한번 작성을 해 보았습니다.
