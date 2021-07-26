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

