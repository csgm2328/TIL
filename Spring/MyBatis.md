### MyBatis
> mapper 활용
* 절차
  * Sub PJT 1 - Skeleton 회원가입
  * front 에서 회원가입 버튼 누르면
  * checkValue() 에서 [passwordValidator()]()로 정규표현식 검증한 후
  * 왜냐하면 Handler에 매핑 되는 파라미터가 정규표현식을 만족해야하는 SignupRequest 타입이다
  * 그러고 중복검사 후 등록
* MyBatis
  1. dependency 추가
  2. properties에 mapper location setting
  3. mappers/**.xml 생성
     1. namespace 주의
  4. sql문 작성(Mybatis는 파라미터 하나만 받으므로 Map으로 바꿔줌)
  5. ServiceImpl의 함수결과를 함수이름이 동일한 Mapper Interface의 결과로 return
  6. DB 생성
     1. uid --> nickname (PK) 변환
     2. insert 시 coloumn 순서, default 값 주의
  7. HandlerMapping 후 return 타입 주의 ResponseEntity<?>