## TID - 1
* __backend 구조 셋팅(dependency, DB erd)__
* __회원가입 기능 & Swagger__

## TID - 2
* __이메일 인증 구현__
  * 회원가입 후 토큰생성(token save) 함수 호출 --> 토큰ID와 함께 * 이메일 인증 링크 전송 -->
  * 만료전(5분) 링크 접속시 인증완료 --> 인증된 이메일로 처리
  * 회원가입은 일단 되고 메일인증을 받으면 마이페이지에서 인증됨 표시 * 처리 로그인할 때 object넘기므로 그때 getverified()로 구분
  
* __JPA는 DB와 sync를 자동으로 맞춰준다__
  * 이메일 인증을 위해 새로운 토큰 model.java를 만들고 서버를 켰을 * 뿐인데 db에 schema가 생겼다????
  * 하지만 null 조건 등 손봐야함
  * 수정
    * 그래서 Update를 따로 쿼리를 만들지 않고 값을 바꾼다음에 findAll() * 을 통해 hibernate가 select 쿼리 전에 Sync를 맞춰서 update를 * 해준다
  * 삭제
    * deleteBy속성이름
    * 적용되는 쿼리는 id= ? 이지만 잘 적용됨

## TID - 3
* __계정 설정 페이지__
  * 비번 변경, 찾기 & 계정 정보 수정
    * JPA로 편리한 RUD로 해결 (R은 login시 responsebody로 넘김)
  * 프로필 이미지 올리기
    * 프로필 table 생성
    * profile/email로 요청오면
    * getProfiledata()로 리턴
    * DB에 이미지 데이터 올리려고 BLOB으로 byte[] 저장
    * 하지만 프론트단에서 그 byte[]를 보여주는 거 복잡
  * 이메일 인증시 Front 페이지로 연결되도록 전송링크변경
  * controller
    * requestParam말고 경로자체에 들어온 걸 파라미터로 받는  @PathVariable로 변경

## TID - 4
* __프로필 이미지 기능__
  * git conflict
    * UserService 수정부분이 충돌남 왜???
    * 아마 develop에 merge 후 다시 내 브랜치에서 develop 내용들을 merge하지 않아서 발생된 듯함
    * 해결: 웹에서 원격 merge 후에 local 에서 develop* pull 이후에 local로 머지
      ```git
      $ git 
      $ git merge --no-ff "feature/~"
      ```
    * 원격 머지 & 로컬 머지를 추천해줘서 둘다해서 잘 마무리되었지만
    * 왜 충돌났는지는 모르겠다
  * imgdata 대신 imgpath
    * 팀원 요청으로 DB에 말고 로컬에 저장후 url을 db에 저장
    * lombok 기능인 Builder로 setter 대신 사용
  * ImageHandler()
    * 
  * __팔로우 기능__
    * 팔로우 버튼 누르면 save(AI-code, from(A), to(B)
    * 팔로우 상태에서 또 누르면 언팔로우
    * findByfromAndTo해서 객체 있으면 삭제
    * 팔로우 상태표시
      * findByfromAndTo(나, 너) 객체 있으면 팔로우중
      * result.data = succes: 팔로우 여부
      * 양방향 팔로우상태일땐 fromAndto(from,to), (to,from)으로 체크
    * Controller Swagger에 표현하는 description 바꾸려면 @ApiParam* (value = “email”)로
      * name은 파라미터 변수이름으로 들어감
      * @requestParam(value = “”)이것도 쓰고 parameter도 쓰면 값이 두번 * 들어감
    * @GeneratedValue(Auto Increment)
      * Identity는 DB가 맡아서 AI 
      * Sequence는 hibernate_sequence table만들어서 이 테이블에 AI * 테이블 두고 hibernate가 연계해서 AI
  * ⚠️트러블 슈팅
    * PessimisticLockException: could not execute statement
      * 디비 커밋을 안해주면 생기는 오류
      * workbench에서 강제로 지운게 반영이 안되나했는데
      * 테스트해보니 그건 또 아니다
      * 임의적으로 발생하는건가?
    * JPA @Entity 의 언더바 사용 주의
      * DB에서 언더바 쓰고 Entity에서도 언더바 쓰니까 못알아들음
      * @Column(name = "from_email")
      * 	String fromemail;
      * 한쪽에서만 쓰고 명시
      * DB 반영안될때  있어서 autocommit 설정

## TID - 5
* __팔로우 기능__
  * 유저 검증하기 위해 profileController에서 userService.findbyId() 사용
* __프로필 페이지 기능__
  * 이메일(PK), 팔로잉, 팔로우 수, 피드 수, 프로필이미지 url, 계정 * 설정에서 한 코멘트
* __팔로워, 팔로잉 목록 기능__
  * findALL()로 객체 가져온다음 프론트에서 쓰기 쉽게 문자열 리스트로 변환해서 전달



## TID - 6
* __알림 기능__
  * 새로운 팔로우나 좋아요, 스크랩 발생시 해당 소유자에게 알림 발송
  * WebSocket을 통해 서버에서 발생한 요청을 실시간으로 프론트에서 표시해야함
  * __WebSocket이란?__
    * 보통은 프론트에서 요청하고 백에서 response를 되돌려주는 통신을 하지만 서버 자체에서 요청을 만들어줘야 할 때 사용한다.
    * EX)
      * 채팅: 프론트에서 친 채팅을 서버가 받은 후 요청을 하지 않던 다른 유저(프론트)의 화면에서도 볼수 있도록 하기 위함
      * 알림: 요청이 들어오면 서버가 Target 유저에게 알림표시를 해줌
    * 이런 동작들을 새로고침 없이 가능케 함
  * __Spring Boot 와 Vue에 적용하기__
  * 구성
    * dependency 추가
    * WebSocketConfig.java
      * 연결: 엔드포인트(url)를 설정해서 프론트단에서 연결 수립 함수
      * 라우팅: 클라이언트에서 클라이언트로 메시지 라우팅하기 위한 메시지 브로커 구성
    * 이렇게 구성을 하면 위의 라우팅으로 연결된 유저들은 해당 라우터를 **구독**중인 상태가 되고 서버는 그 구독하고 있는 대상들에게 메세지를 broadcast할 수 있음 
    * ⚠️주의: backend랑 fronted랑 PORT 다르므로 연결할때 CORS 셋팅 해줘야함
       ```js
        //이때 "*"로 모든 경로 열어줄것이므로 setAllowedOrigins() 대신
        addEndpoint("/ws").setAllowedOriginPatterns("*")
        ```
  * 구현
    * 연결할 서버 url로 connect 요청
    * 첫 성공시
      * subscribe() 구독할 라우팅 설정
      * ex) send(addUser())같은 함수도 동작시켜서 서버에 누가 입장했는지 알릴 수 있음
    * 그 후 send(sendMessage())로 구독중인 유저들에게 broadcast
    * Controller
      * addUser나sendMessage같은 함수 처리하기 위해 MessageMapping() 처리
    * WebSocketEventListner를 통해 서버내에 연결 수립과 끊김 이벤트시 동작 만들 수 있음

  ## TID - 7
  * 새로운 스프린트 프로젝트 시작
    * git remote set-url ""

  * __배포__
    * server 환경 설정
    * mariaDB > source ./sql
      * index 오류 있어서 지워줌
    * 첫 spring boot:run 5분이상 소요
    * mvn package로 빌드 후(변경 시 필요)
    * jar servlet.context=/api 경로 지정 실행으로 boot 실행
    * __Nginx 설정__
      * sites 설정에서
      * root에 프론트 dist 경로넣고
      * server location / 는 프론트 경로
      * location /api는 백엔드 경로로 지정
    * VS Code SSH 설정
      * ~/.ssh/ 아래로 .pem 옮기고
      * 포트번호 aws는 포트번호 쓰면 안됨
    * 배포서버에 이미지 경로 수정
      * window 환경 --> linux

    * __보안설정__
      * .pem키를 gitlab에서 누구나 다운받을 수 있음
      * 그러므로 누구든 접속 가능.
      * 접속을 하면 일단 서버 코드를 볼 수 있어서
      * applcation.* 보기 권한 설정하기
        * 🛠️해결 
        * sudo 비번 설정
        * application.* 파일들 소유,파일 권한 root로 변경
          * 권한 변경만 했을 때는 
        * 이젠 빌드를 **sudo 권한**으로해야한다
          * 근데 sudo로 빌드하면 왜 다시 처음부터 다운받지???
      * sudo mysql 막기
        * 🛠️해결 
        * /usr/bin/mysql_secure_installation 실행
        * root 계정 password 설정
        * root로의 원격접속 막기 설정
          * 이렇게 하면 root@%(모든 원격 허용했던) 계정이 사라짐
          * 그러므로 root@serverIP 추가해줘야함
      * IP 차단을 위해서는 ufw를 사용해야하는데 ssh까지 차단될 위험이 있어서 보류
      * time_zone 설정 (datasource셋팅해도 안바뀜)
        ```sql
        SET GLOBAL time_zone='Asia/Seoul';
        set time_zone='Asia/Seoul';
        ``` 
    * ⚠️ EC2 에서 Google EmailSender 보안 문제
      * https://myaccount.google.com/lesssecureapps?pli=1&rapt=AEjHL4MeKKMmmil0-s9aZNPDKI_KYzblyQkN-i5OF7jqLiemjfoYucG6srbUHJeDBT7Rz2Redt-a1G9nFBw-loTlJozpCkaFBw
      * 기본적으로 허용 후에도 EC2로 접근시 막힐 때
      * https://accounts.google.com/b/0/DisplayUnlockCaptcha
      * 이것도 허용
      
  * ⚠️ MySQL WorkBench 오류
    * user table의 email을 모두가 FK로 쓰는데 여러 테이블이 참조가 불가능한 상황 발생
    * mariadb server에서는 index를 지우고 하니까 되고
    * workbench에서는 아예 테이블을 새로만들어서 하니까 됨

