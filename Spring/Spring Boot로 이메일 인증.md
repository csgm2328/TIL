## Spring Boot로 이메일 인증
* __흐름__
  * 회원가입 후 토큰생성(token save) 함수 호출 --> 토큰ID와 함께 이메일 인증 링크 전송 --> 만료전(5분) 링크 접속시 인증완료 --> 인증된 이메일로 처리(재인증도 구현)
* __셋팅__
  * __.yml__(properties도 가능)로 SMTP 서비스 사용할 계정 설정
  * DB: __Token ID(PK)__, 만료시간(생성후 5분), 생성시간, 유저 ID(FK), 만료여부
* __Service__
  * @Service & @Autowired 필수
  * 새롭게 Email package만들어서 tokenservice, model, email전송service 만듬
  * Controller/회원가입에서 user.save() --> createToken()
  * __createToken()__
    * token객체만들고 save 후 토큰과 함꼐 이메일 전송
  * 이제 링크를 누르면 confirmToken()으로
    * 유저가 만료되지않은 토큰을 가지고 있는지 jpa 함수로 얻어냄
* __재인증__
  * 유저이메일(PK)를 받아와서 그 유저의 token을 다 DELETE하고 새로운 토큰 생성 후 confirm
