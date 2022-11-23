## SGA(System Global Area)

SGA는 Oracle의 서버 프로세스 및 백그라운드 프로세스들이 공유하는 메모리 영역이다.

해당 영역은 크게 필수적, 부수적 메모리 영역으로 나뉠 수 있다.   
다음 내용들은 Oracle 19C 기준으로 설명하겠다.   

### 필수 메모리 영역
1. **Shared Pool** : 해당 영역에는 data dictionary 정보(테이블 스키마, 인덱스 스키마 등의 모든 데이터베이스 정보), sql 구문 분석 결과를 보관하는 library cache로 구성된다.   


**※주의점** : library cache에 저장된 sql문은 문자열이 달라지면 다른 값으로 인식하기 때문에 sql 문을 사용할 때 띄어쓰기, 바인딩 변수명을 규칙을 정해 사용하는 것이 library cache를 사용하여 성능을 향상시킬 수 있는 방법이다.

2. **Database Buffer Cashe** : database의 block 단위 데이터들을 올려놓는 메모리 영역이다. 해당 영역 덕분에 자주 사용되는 데이터들을 매번 disk I/O 없이 메모리 접근을 통해 빠르게 조회할 수 있다. 뿐만 아니라 데이터를 업데이트 할 경우에도 바로 disk I/O 하지 않고 메모리에 변경사항을 보관하다가 메모리 영역이 가득차게 되었을 때, LRU 알고리즘을 통해 메모리 영역에서 disk로 내리면서 업데이트를 한다. 즉, 한 번의 처리로 여러 업데이트를 처리하는 배치 처리로 성능을 향상시켰다.


3. **Redo Log Buffer** : 복구를 위해 커밋된 데이터들을 보관하는 파일을 redo log 파일이라 한다. 이런 redo log 파일을 커밋마다 매번 disk I/O를 통해 저장한다면 성능이 매우 느려진다. 그렇기 때문에 해당 메모리 영역에 보관하다가 특정 크키 이상 차게 되면 DB에 redo log 파일로 저장한다.

**참고** : 리두 로그 버퍼를 DB에 저장하는 역할을 하는 프로세스는 LGWR(Log Writer)이다. LGWR가 메모리의 로그 데이터를 DB에 저장하는 시점은 다음과 같다.   

- Redo log buffer가 1/3 이상 찼을 경우
- 1MB 이상 트랜잭션 발생시
- 3초마다
- DBWn 백그라운드 프로세스가 신호를 보낼 경우

### 부가 메모리 영역

1. **Flashback Buffer** : 

2. **Large Pool** : 

3. **In Memory Area** : 

4. **Memoptimize Pool** : 

5. **Shared I/O Pool** : 

6. **Streams Pool** : 
