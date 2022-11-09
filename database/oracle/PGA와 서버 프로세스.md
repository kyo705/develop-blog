PGA와 서버 프로세스
-----------------
오라클의 서버 프로세스는 클라이언트의 요청을 처리하는 프로세스이다. 그리고 PGA는 서버 프로세스가 독립적으로 사용하는 메모리 영역이다.   
서버 프로세스는 클라이언트의 sql 요청이 오면 해당 sql을 구문 분석, 권한 체크, 실행 계획 생성, 최적화를 걸쳐 db로부터 실제 데이터를 받아온다.   

PGA 영역은 크게 3영역으로 나뉜다.   
**1. sql work area**   
**2. session memory area**   
**3. private sql area**   

![image](https://user-images.githubusercontent.com/89891704/200866986-6d1efae9-496c-4f2c-ae87-ee2491a3e584.png)


sql work area
----------------
sql work area는 또 크게 3가지 영역이 존재한다.   

1) sort area : 데이터를 정렬할 때 사용하는 메모리 영역
2) hash area : 해시 조인을 할 때 해시 테이블을 만들 때 사용하는 메모리 영역
3) bitmap merge area : 비트맵 인덱스의 merge scan을 사용하기 위한 영역

session memory area
----------------------
session memory area는 클라이언트 프로세스의 세션 정보를 저장하는 영역이다.   

private sql area
-------------------
private sql area는 크게 2가지 영역으로 나뉜다.   

1. persistent area : 바인딩 변수를 저장하는 영역
2. runtime area : 실행된 쿼리 결과를 저장하는 영역
