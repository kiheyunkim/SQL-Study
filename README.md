# SQL-Study


## mysql 명령줄 도구 사용 방법

### 데이터 베이스 확인
```sql
show databases;
```

### 데이터 베이스 사용
```sql
use databaseName;
```

### from 절이 없으면?
일부 데이터 베이스에서는 from절이 없으면 쿼리를 실행할 수 없음. 그래서 함수를 호출하기만 하면 실행되도록 dual이라는 테이블 제공.

Mysql도 마찬가지로 제공함
```sql
select now();               #mysql에서는 이것이 가능
select now() from dual;     #oracle에서는 이와 같이 사용해야함.
```

## MySQL 자료형

### 문자데이터

문자 데이터는 고정 길이 또는 가변 길이 문자열로 저장될 수 있음.
* 고정 길이 문자열: 공백으로 오른쪽으로 채워지고 항상 동일
* 가변 길이 문자열: 공백으로 오른쪽이 채워지고 크기도 가변
```sql
char(20)    #고정 길이
varchar(20) #가변 길이
```
char의 최대길이는 255바이트이고 varchar은 최대 65,535바이트까지 사용할 수 있음

이메일이나 XML문서와 같은 긴 문자열을 저장할 때는 mediumtext, longtext를 사용할 수 있음

**varchar자료형을 오라클에서 사용하면 예외가 발생함. 오라클 사용자는 varchar2자료형을 사용해야함** [링크](https://mainia.tistory.com/775)

### 캐리터셋
영어와 라틴 알파벳은 문자 표현에 1바이트만이 필요하나, 일본어나 한국어를 비롯한 다른 언어는 많은 수의 문자를 포함하므로 여러 바이트의 저장공간이 필요함. 이러한 문자집합을 캐릭터셋이라고 함

```sql
show character set; #지원하는 캐릭터 셋 확인
```

```text
+----------+-----------------------------+---------------------+--------+
| Charset  | Description                 | Default collation   | Maxlen |
+----------+-----------------------------+---------------------+--------+
| big5     | Big5 Traditional Chinese    | big5_chinese_ci     |      2 |
| dec8     | DEC West European           | dec8_swedish_ci     |      1 |
| cp850    | DOS West European           | cp850_general_ci    |      1 |
....
```
maxlen이 1보다 크면 multibyte character set이라는 의미.

```sql
#테이블 열 정의시 기본 캐릭터 셋이 아닌 캐릭터 셋을 지정하기 위한 방법
#mysql8부터는 utf8mb4가 기본임.
varchar(2) character set latin1
#데이터베이스 단위로 캐릭터 셋을 지정
create database database_name character set latin1;
```

### 텍스트 데이터
* 텍스트 데이터 종류
  * tinytext: 최대 255바이트
  * text: 최대 65,535바이트
  * mediumtext: 최대 16,777,215바이트
  * longtext: 최대 4,294,967,295바이트

* 텍스트 데이터 고를 시 고려사항
  * 텍스트 열에 로드되는 데이터가 해당 유형의 최대 크기를 초과하면 데이터가 잘림
  * 데이터를 열에 로드되면 후행 공백이 제거되지 않음.
  * 정렬 또는 그룹화에 text열을 사용한 경우, 필요하면 한도를 늘릴 수 있지만 처음에는 1,024바이트만 사용됨
  * text를 제외한 텍스트 자료형은 MySQL의 고유한 자료형(DB2와 오라클은 큰 문자 오브젝트에 clob이라는 자료형을 사용함)
  * MySQL은 이제 varchar 열에서 최대 65,535바이트를 허용하므로(버전 4에서는 255바이트로 제한) tinytext나 text자료형을 사용할 필요가 없음

* 오라클 데이터 베이스의 텍스트 데이터
  * char : 최대 2,000바이트 
  * varchar2: 최대 4,000바이트 허용
  * clob: 그보다 더 큰 문서의 경우
