## JPA (Java Persistence API)
> EJB의 복잡성과 속도를 개선하기 위한 자바 ORM.<br>
> JDBC API와 app 사이에서 동작하여 개발자가 SQL중심이 아닌 객체 중심으로 개발이 가능함.
* 장점
  * 생산성
    * JPA에 객체만 넘기면 CRUD 간단히 가능
    * ex) 수정: Object.setName("변경")
    * 자동으로 Update Query 전달
  * 유지보수
    * 필드 변경시 모든 SQL을 수정해야했는데
    * JPA는 필드만 추가하면 됨
  * 신뢰성
    * DAO의 query를 확인해야 객체를 정확히 반환했는지 알 수 있지만
    * JPA는 신뢰할 수 있음
  * 성능 최적화( 버퍼 & 캐시)
    * 버퍼
      * transaction.begin() ~ commit()까지 INSERT를 한번에 모아서 보내기 가능.
      * JDBC BATCH SQL 기능을 활용한 것
      * UPDATE, DELETE도 모아서 가능
    * 캐시
      * 똑같은 쿼리 요청하면 캐시에 저장했다가 반환
* 활용
  * **Insert**
    * ⚠️주의: insert시 DB schema에서 NOT NULL인 column들은 전부 객체에 set()
    * ⚠️주의: save() 로 insert시 같은 내용이 이미 있다면 Dirty Checking으로 update가 되어버림
      * PK로 검색해서 findById()로 객체가 반환되는지 체크하자!
      * 이걸 이용해서 메서드 하나에 삽입과 수정 기능을 만들 수도 있음
  * **자동 스키마 생성**
    * jpa 설정하고 schema를 정의한 클래스 파일만 만들고 실행해도 연결된 db에 해당 테이블이 없다면 자동으로 hibernate가 schema를 만들어준다
    * 하지만, 관계설정은 수정해야함
  * __Update__
    * JpaRepository에 따로 update() 함수는 없다
    * @Query()로 작성은 가능하지만 권장❌
    * hibernate가 select할 때마다 db와 **자동으로 sync**를 맞춰주는 기능을 활용해 java내에서 변수를 변경하고
    * findAll() 함수를 실행하면 select하기전 변경사항이 수정된다
    * 이 때 영속성 이전을 위한 **@Transactional** 필요
  