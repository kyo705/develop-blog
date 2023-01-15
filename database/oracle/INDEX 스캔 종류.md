# INDEX 스캔 종류

> ### 인덱스의 종류
> 1. Index Range Scan
> 2. Index Unique Scan
> 3. Index Full Scan
> 4. Index Fast Full Scan
> 5. Index Skip Scan


### 1. Index Range Scan
> range scan은 b-tree 자료구조의 인덱스에서 조건절의 값에 해당하는 리프노드로 이동해 같은 조건에 해당하는 모든 데이터들을 찾는 방식이다.   
> 기본적으로 where 절의 =, IN, BETWEEN, 범위 조건 등을 사용할 때 기본적으로 제공하는 스캔 방식이다.

### 2. Index Unique Scan
> unique scan은 조건절의 특정 값만 찾는 방식으로 반드시 해당 인덱스가 존재해야 스캔이 된다.

### 3. Index Full Scan
> full scan은 조건절에 인덱스가 없는 경우 인덱스 풀스캔을 사용한다. 해당 스캔은 특정 인덱스의 모든 범위를 다 스캔하는 방식이다. 그래서 조건절에 인덱스의 부분 집합이 하나도 없는 경우
>  table full scan이 더 효율적일 수 있다. index full scan은 조건절에 인덱스의 부분 집합이 있는 경우 사용하면 table full scan 보다 빠르게 조회가능하다.

### 4. Index Fast Full Scan
> 기존의 스캔 방식들은 전부 단일 block을 디스크로부터 읽어오는 방식이지만 fast full scan은 디스크로부터 멀티 블록을 가져오는 스캔방식이다. 그래서 속도가 빠르다는 장점이 있는 반면, 논리적인
> 구조로 읽어오는 것이 아닌 물리적 저장소를 기준으로 읽어오기 때문에 정렬되어 있지 않다는 단점이 있다.

### 5. Index Skip Scan
> skip scan은 조건절에 인덱스의 선행 컬럼이 없으면서 선행 컬럼의 카디널리티가 적은 경우 사용하면 좋다. 해당 스캔 방식은 선행 컬럼에 모든 컬럼이 있다고 가정하고 후행 컬럼 인덱스를 조회하는 방식이다.
