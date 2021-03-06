# [Hobby Zoa](http://i5c102.p.ssafy.io)

## TID - 1
* __backend 구조 셋팅(dependency, DB erd)__
* __회원가입 기능 & Swagger__

## TID - 2
### __이메일 인증 구현__
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
  
* __JPA는 DB와 sync를 자동으로 맞춰준다__
  * 이메일 인증을 위해 새로운 토큰 model.java를 만들고 서버를 켰을 * 뿐인데 db에 schema가 생겼다????
  * 하지만 null 조건 등 손봐야함
  * 수정
    * 그래서 Update를 따로 쿼리를 만들지 않고 값을 바꾼다음에 findAll() * 을 통해 hibernate가 select 쿼리 전에 Sync를 맞춰서 update를 * 해준다
    * findby()는 안됨
    * save()로 자동수정도 가능
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
    * JPA로 entity관리할때 생성자 no, all 필수

## TID - 5
* __팔로우 기능__
  * 유저 검증하기 위해 profileController에서 userService.findbyId() 사용
* __프로필 페이지 기능__
  * 이메일(PK), 팔로잉, 팔로우 수, 피드 수, 프로필이미지 path, 
  * 계정 수정
* __팔로워, 팔로잉 목록 기능__
  * findALL()로 객체 가져온다음 프론트에서 쓰기 쉽게 String 리스트로 변환해서 전달



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
      * 백엔드에서 소켓 서버를 만들고 프론트에서 소켓 서버에 연결요청을 보낸 후 구독을 하는 방식
    * ⚠️주의: backend랑 fronted랑 PORT 다르므로 연결할때 CORS 셋팅 해줘야함
       ```js
        //이때 "*"로 모든 경로 열어줄것이므로 setAllowedOrigins() 대신
        addEndpoint("/ws").setAllowedOriginPatterns("*")
        ```
    * Controller
      * MessageMapping() 웹소켓 연결된 프론트에서 오는 메시지 요청에 대한 처리 결과를 웹소켓 연결로 리턴함
    * WebSocketEventListner
      * 통해 서버내에 연결 수립과 끊김 이벤트시 logger 설정가능
  * 구현
    * 참고 사이트
      * Backend
        * https://ratseno.tistory.com/71
        * https://blog.naver.com/PostView.nhn?blogId=gmlwo308&logNo=222210869950&parentCategoryNo=&categoryNo=35&viewDate=&isShowPopularPosts=true&from=search
      * Frontend
        * https://ratseno.tistory.com/72?category=773803
    * 연결할 서버 url로 webSocket 연결
      * 해당 페이지를 열고 있을 때 소켓연결을 유지하는 거라 연결 타이밍을 결정하는게 아니다
      * 모든 페이지에 항상 있는 헤더에다가 연결코드를 넣어야함
    * 성공시
      * subscribe() 구독할 라우팅 설정
        * ex) send(addUser())같은 함수도 동작시켜서 서버에 누가 입장했는지 알릴 수 있음
      * /queue/{로그인한 email}으로 구독할 때 자신에게 라우팅된 알림만 받기
        ```java
        Controller를 사용해서 @MessageMapping과
        @sendToUser() + return Message 방식 대신에

        //알림이 필요한 기능 동작 후에 알림메시지를 연계 동작으로 생성해주는 방식으로
        void + messageTemplate.ConvertandSend("/queue/" + email)
        ```
    * __핵심: 서비스 함수들에 알림함수를 추가해 서비스 동작 후 알림이 발생하도록 연결__
      * profileController.follow() 가 아니고,   
      * alarmService.follow() 도 아니고
      * alarmService.createAlarm(함수하나로 alarmtype으로 구분해서 모든 알림 처리)
      * 알림을 따로 Controller로 라우팅하지 않고 타입별로 메세지를 만들어낸다
      * 만들어진 메세지는 DB에 추가하고 구독중인 곳에 send()로 구독중인 유저들에게 broadcast
  
    * __상세 기능__
      * 각 serviceImpl에서 서비스 처리후 **AlarmService.createAlarm() 호출**
        * createAlarm으로 웹소켓 메시지 리턴할 것은 상세 알림 정보가 아니라 알림 bell에 표시할 새로운 알림의 개수다.
        * countAlarm() 호출
      * alarmType, From, to이 같은 알림은 하나만 생성
      * from == to 알림은 생성안함
      * 해당 유저에 대한 알림 목록**요청**은 **AlarmController**로 처리
        * 안읽은 게 먼저오도록
        * 읽은거는 한달 내 생성된것만
      * 알림 읽음 처리 후 countAlarm() 호출
      * 웰컴 알림을 관리자가 보내주므로 DB 초기화후에 admin은 만들고 알림 켜야함

    * __FrontEnd__
      * 웹소켓 연결이 해당페이지를 벗어나면 끊기므로
      * 해당 유저가 어떤 페이지에있던 공통적으로 사용하는 Header에다가 웹소켓을 연결한다
      * bell icon에 Vuetify badges 적용하면 알림 개수 보임
  ## TID - 7
  * 새로운 스프린트 프로젝트 시작
    ```git
      $ git remote set-url "새로운 프로젝트"
    ```
  * __배포__
    * AWS server 환경 설정
    * mariaDB > source ./sql
      * index 오류 있어서 지워줌
    * 첫 spring boot:run 5분이상 소요
    * mvn package로 빌드 후(변경 시 필요)
    * jar servlet.context=/api 경로 지정 실행으로 boot 실행
    * __Nginx 설정__
      * Nginx란?
        * 비동기방식이라 매우 높은 성능
        * load balancer나 API gateway로 사용가능
        * 프론트엔드(정적파일) 서비스할때 뛰어난 성능
        * 크롬, firefox도 이걸로만듬
        * nginx로 들어오는 건 http 아니면 https
      * sites 설정에서
      * root에 프론트 dist 경로넣고
      * server location / 는 프론트 경로(web server 역할)
      * location /api는 백엔드 경로로 지정(API gateway 역할)
      * 그러면 이제 nginx가 웹서버역할을 하면서 빌드 파일 위치만 알려주면 node를 실행할 필요가 없어진다
      * __업로드 이미지 크기 설정__
        ```shell
        #conf
        http {
          client_max_body_size 10M;
        }
        #default
        server {
          client_max_body_size 10M;
        }
        ```
        ```properties
        spring.servlet.multipart.maxFileSize=5MB
        spring.servlet.multipart.maxRequestSize=10MB
        ```
      * 
    * 도커?
      * 왜 도커를 써야하는지
        * vm, autoscaling은 폭발적인 트래픽을 해결하지못한다.
        * 이미지를 만들어 놓기만 한다면 운영체제를 부팅하지 않는 도커는 빠르게 필요한 서버를 증설할 수 있다.
        * 만들어 놓은 이미지대로 배포하는 편의성
        * 그러므로 프론트/ 백은 도커화해야함
        * DB, Jenkins, nginx의 도커화는 선택
          * DB를 배포하는 경우는 거의 없고
          * 빌드서버가 많을 필요는 없으니

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
        # 일시적 세팅
        SET GLOBAL time_zone='Asia/Seoul';
        set time_zone='Asia/Seoul';
        ```
        ```sh
        # 글로벌 세팅 restart 필요
        vi /etc/mysql/my.cnf
        [mysqld]
        default-time-zone='Asia/Seoul'
        ``` 
    * SSL
      * 로그인같은 경우 회원정보를 암호화해서 보내야함
      * 매번 이렇게 암호화하기 번거로ㅋ우니 TLS(Transport Layer Securityfh를 사용해서 암호화)
    * Cert Bot
      * 상용 프로그램 제작할 때 구매해서 사용하는 Root 인증서
      * 무료 인증서도 있으니 FE & BE에 설정
    * ubuntu , root 말고 사용자 계정 따로 만들어야 해킹 타겟을 피할 수 있음
    * WebSocket HandShake error(Connection Header error)
      * <img src="..\assets\websocket handshake error.JPG">
      * 🛠️ 해결
        * nginx config: /etc/nginx/sites-enabled/default
        * 에서 header를 업그레이드 해줌
        ```sh
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
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

  * ⚠️ DB 컬럼의 예약어 주의,,
    * check, like, from
    * boolean column getter동작안하는 이유는?

  * 날짜 어노테이션 @Auditing
    * @CreatedDate , @LasetModifiedDate 자동
    * 모델에 @EntityListeners(AuditingEntityListener.class) 추가
    * application.java에 @EnableJpaAuditing 추가
    * 이렇게 많은 라이브러리 사용이 싫다면
      * @Column(insertable = false, updatable= false)만으로도 가능
## TID - 8
* CI/CD
  * gitlab Runner 사용하기
    * Runner 설치 & 등록
    * Build & Deploy Stage 구성
      * $CI_PROJECT_DIR : job 실행할 full path 클론
    * __빌드__
      * properties 권한 때문에 sudo mvn package로 빌드해야하는데
      * sudo 권한을 줘도 권한에러가 계속 발생해서
      * chmod +x mvnw --> ./mvnw package로 빌드 성공함
      * 빌드 성공하면 target/*.jar 를 $HOME/app1.jar로 복사
        * target/*.jar의 권한은 ubuntu이기 때문에 runner 권한의 jar를 만들어주는 것!
      * gitignore에 있던 .yml 읽어야 해서 다시 git에 올림
      * 🛠️ websocket에러로 디펜던시 추가
      ```properties
      spring.jpa.database-platform=org.hibernate.dialect.MySQL5Dialect
      ```
    * __실행__
       * gitlab-runner라는 유저로 ci/cd를 실행된다.
       * ubuntu 권한의 기존 파일들을 읽을 수 없기에 backend 폴더를
       * 복사해서 jar를 파일처럼 gitlab에도 backend 폴더를 만들어줌
         * 이래야 jar 실행한 $HOME path를 기준으로 /images 등을 찾아갈 수 있다.
         * gitlab-runner로 실행시 app.pid를 못만들고 추적이 불가능해서
            ```sh
            #restart.sh
            kill $(ps -ef |grep java | awk '{print $2}')
            ```
       * front build 위치 조정
         ```sh
         #root /home/ubuntu/SubPJT3/S05P13C102/frontend/dist;
         root /home/gitlab-runner/dist;
         ```

    * restart.sh
      * 상대경로로 app1.jar를 찾아야하므로 /backend에서 실행
      ```yml
      deploy-back: 
          ...
      script :
        - cd $HOME/backend
        - ~/restart_backend1.sh
      ```
    * __결과__
    <img src ="..\assets\gitlab_cicd_passed.JPG">

