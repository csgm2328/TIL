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
  * ⚠️주의: insert시 DB schema에서 NOT NULL인 column들은 전부 객체에 set()
  * save() 로 insert시 같은 내용이 이미 있다면 Dirty Checking으로 update가 되어버리므로 주의
    * PK로 검색하는 findById()를 통해 위 상황 막을 수 있음
    * 이걸 이용해서 메서드 하나에 삽입과 수정 기능을 만들 수도 있음