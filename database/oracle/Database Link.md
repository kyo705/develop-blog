# Database Link

> **Database Link**란 원격 DB에 접근할 수 있도록 오라클에서 제공하는 스키마 객체이다.   
database link로 원격에 존재하는 테이블, 인덱스 등의 객체에 접근해 계정 권한에 적절한 crud를 할 수 있다.

## Database Link 사용 방법

### 1. 현재 데이터베이스에서 원격 데이터베이스에 접속하기 위한 스키마를 먼저 생성한다.   
```
CREATE DATABASE LINK ${db_link_name}
CONNECT TO ${user_name} IDENTIFIED BY ${password}
USING ${connect_string}
```

> - db_link_name : 데이터베이스 링크 이름
> - user_name : 원격 데이터베이스에 접속하기 위한 사용자 계정
> - password : 원격 데이터베이스에 접속하기 위한 사용자의 비밀번호
> - connect_string : 원격 데이터베이스에 접근하기 위한 연결 문자열 또는 tnsnames.ora 파일에 정의된 TNS이름

**※ connect_string 살펴보기**
```
(DESCRIPTION =
    (ADDRESS = (PROTOCOL = TCP)(HOST = 원격 호스트)(PORT = 원격 포트 번호))
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = 서버명)
    )
)
```

### 2. 생성된 스키마 객체로 접근해 CRUD를 한다.

```
SELECT * FROM ${remote_table_name}@${db_link_name}
```

**※** '@'를 사용하기 귀찮다면 SYNONYM 객체를 사용해 동의어를 만들어 사용하면 편리하다.
```
CREATE SYNONYM ${원하는 명칭} FROM ${테이블명@링크명}
```
