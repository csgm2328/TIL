## 실습
+ yarn package Manager 대신 깔려있는 npm으로 진행
```
npm run serve

npm install gh-pages-deploy -g

npm run build

gh-pages-deploy
```

### ⚠️ Trouble Shooting
> 빌드된 웹페이지에서 App.vue가 아니라 README가 뜸
+ actions에 deploy.yml 만들었을 때 workflow/ 폴더 밑으로 안생겨서 제대로 안된거 였음
+ 혹시나 해서 npm run build하고 다시 배포하고
+ 새로운 액션 다시 만들어서 진행했더니 제대로 뜸

### ❈ yml에서 npm 과 yarn
- deploy.yml이나 모든 명령어를 npm으로 했지만
- test 부분명령어는 yarn으로 했는데도 문제가 없었다
- github 환경에서 빌드되는거라 둘다 상관없는 것 같다



## Today I Learned (TIL)
- 목표
> vue cli로 만든 정적 웹페이지를 github pages에서 호스팅하고 자동 CI/CD 환경 실습하기
- DevOps
> 서비스를 사용할 고객에게 안정적인 서비스 지원에 필요한 조직문화와 도구의 모음
- github.io
> 코드 푸시하면 자동으로 actions workflow가 테스트해서 배포 <br> branch에 pull/push하고 성공하면 github page에 자동으로 배포하는 
- Jamstack
> js + apis + markup으로 간단하게 구성하는 서버리스 웹 프론트


- GitHub Actions
> 개발 workflow에서 작업을 자동화하기 위한 패키지 스크립트 <br>
 push pull 이벤트 반응하는 트리거 설정가능 <br>
 vue 코드 작성 → 자동 테스트 → 빌드 → Pages 에 배포


## 산출물
- 결과페이지

<img src="https://user-images.githubusercontent.com/39210160/125282360-42a70e80-e352-11eb-929c-1259da9d7617.JPG"></img>
- [Actions]("https://github.com/csgm2328/vue-devops/actions")