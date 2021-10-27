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

* SQL서버의 텍스트 데이터
  * char 및 varchar 데이터 모두에 대해서 8,000바이트까지 허용
  * varchar(max)의 경우 2GB의 데이터를 저장할 수 있음 

### 숫자 데이터

* MySQL의 정수 자료형

  |  자료형   |  부호있는 정수 저장값 범위   | 부호없는 정수 저장값의 범위 |
  | :-------: | :--------------------------: | :-------------------------: |
  |  tinyint  |           -128~128           |            0~255            |
  | smallint  |       ~32,768 ~ 32,767       |         0 ~ 65,535          |
  | mediumint |          -8,388,608          |          8,388,607          |
  |    int    | -2,147,483,648~2,147,483,647 |       0~4,294,967,295       |
  |  bigint   |         -2^63~2^63-1         |          0~2^64-1           |

  * **불필요한 스토리지 공간을 낭비하지 않으려면서도 저장할 수 있는 가장 수를 저장할 자료형을 선택해야함 **



* 부동소수점 자료형

  |   자료형    |                           숫자범위                           |
  | :---------: | :----------------------------------------------------------: |
  | float(p,s)  | -3.402823466E+38 ~ -1.175494351E-38<br />1.175494351E-38 ~ 3.402823466E+38 |
  | double(p,s) | -1.7976931348623157E+308 ~ -2.2250738585072014E-308<br />2.2250738585072014E-308 ~ 1.7976931348623157E+308 |

  * **부동 소수점 자료형을 사용할 때는 정밀도(소수점 오른쪽 왼쪽에 허용되는 자릿수)와 척도(소수점 오른쪽에 허용되는 자릿수)를 지정할 수 있지만 필수는 아님**

  * 정수형과 마찬가지로 unsigned키워드를 통해 음수가 저장되지 못하도록 할 수 있음.



### 시간 데이터

* 시간 데이터

  |  자료형   |      기본 형식      |                         허용값                          |
  | :-------: | :-----------------: | :-----------------------------------------------------: |
  |   date    |     YYYY-MM-DD      |                 1000-01-01 ~ 999-12-31                  |
  | datetime  | YYYY-MM-DD HH:MI:SS | 1000-01-01 00:00:00.000000 ~ 9999-12-31 23:59:59.999999 |
  | timestamp | YYYY-MM-DD HH:MI:SS | 1970-01-01 00:00:00.000000 ~ 2038-01-18 22:14:07.999999 |
  |   year    |        YYYY         |                       1901 ~ 2155                       |
  |   time    |      HHH:MI:SS      |          -838:59:59.000000 ~ 838:59:59.000000           |

  * datetime, timestamp, time자료형에서는 소수점 이하 6자리(마이크로초) 까지 사용할 수 있음. datetime(2)로 지정하면 1/100초까지 포함 가능

  

* 날짜 형식의 구성 요소

  | 요소 |      정의       |    범위     |
  | :--: | :-------------: | :---------: |
  | YYYY | 연도, 세기 포함 | 1000 ~ 9999 |
  |  MM  |       월        |   01 ~ 12   |
  |  DD  |       일        |   01 ~ 31   |
  |  HH  |      시간       |   00 ~ 23   |
  | HHH  |   시간(경과)    | -838 ~ 838  |
  |  MI  |       분        |   00 ~ 59   |
  |  SS  |       초        |   00 ~ 59   |

  

### 테이블 생성

##### 1단계: 설계: 어떤 종류의 정보를 포함해야하는지?

한사람을 묘사할 정보를 담는 다고 했을때 정보는 다음과 같음(person 테이블)

* 이름(name)
* 눈동자색(eye_color)
* 생일(birth_date)
* 주소(address)
* 좋아하는 음식(favorite_foods)

|       열       |    자료형    |   허용값   |
| :------------: | :----------: | :--------: |
|      name      | varcher(40)  |            |
|   eye_color    |   char(2)    | BL, BR, GR |
|   birth_day    |     date     |            |
|    address     | varchar(100) |            |
| favorite_foods | varchar(200) |            |



##### 2단계: 정제, 정규화

Person 테이블의 문제

* name의 열은 이름과 성으로 구성된 복합 객체
* 여러 사람이 동일한 이름, 눈동자 색, 생년월일 등을 가질 수 있으므로 고유성을 보장하는 열이 없음.
* Address 열은 거리, 도시, 주/도, 국가 및 우편번호로 구성된 복합 객체
* favorite_foods열은 0개 또는 1개 이상의 독립적은 항목을 포함하는 목록(enumeration), 특정 음식이 어떤 사람에게 귀속될 수 있는지 알 수 있도록 이 데이터에 대한 별도의 테이블을 작성하고 person테이블에 대한 외래 키가 포함되도록 하는 게 가장 좋음



**위의 문제들을 해결한 테이블**

person 테이블

|     열      |       자료형       |   허용값   |
| :---------: | :----------------: | :--------: |
|  person_id  | smallint(unsigned) |            |
| first_name  |    varchar(20)     |            |
|  last_name  |    varchar(20)     |            |
|  eye_color  |      char(2)       | BR, BL, GR |
| birth_date  |        date        |            |
|   street    |    varchar(30)     |            |
|    city     |    varchar(20)     |            |
|    state    |    varchar(20)     |            |
|   country   |    varchar(20)     |            |
| postal_code |    varchar(20)     |            |



favorite_food 테이블

|    열     |       자료형       |
| :-------: | :----------------: |
| person_id | smallint(unsigned) |
|   food    |    varchar(20)     |

**person_id 및 food열은 favorite_food테이블의 기본키를 구성하고, person_id열은 person 테이블에 대한 외래키**



> 위의 케이스는 만약 음식 목록에서 선택하도록 만들어야한다면(중복 된 키워드를 등록 한다거나를 방지하기 위해) 더 정규화를 할 수 있음



##### 3단계: SQL 스키마 문 생성

```sql
#person 테이블
CREATE TABLE person
(person_id SMALLINT UNSIGNED,
 fname VARCHAR(20),
 lname VARCHAR(20),
 eye_color CHAR(2),
 birth_date DATE,
 street VARCHAR(30),
 city VARCHAR(20),
 state VARCHAR(20),
 country VARCHAR(20),
 postal_code VARCHAR(20),
 CONSTRAINT pk_person PRIMARY KEY (person_id)#기본 키 제약 조건, person_id 열에 생성되며 pk_person이라는 이름이 지정됨
);
```

* 체크 제약 조건이라는 제약조건을 통해서 특정 열에 대해 허용값을 제한할 수 있음.

```sql
eye_color CHAR(2) CHECK (eye_color IN ('BR', 'BL', 'GR')),
```

* 또는 enum이라는 자료형을 통해서 다음과 같이 할 수도 있음.

```sql
eye_color ENUM('BR', 'BL', 'GR'),
```


따라서 다음과 같이 표현이 가능함

```sql
CREATE TABLE person
(person_id SMALLINT UNSIGNED,
 fname VARCHAR(20),
 lname VARCHAR(20),
 eye_color ENUM('BR', 'BL', 'GR'),
 birth_date DATE,
 street VARCHAR(30),
 city VARCHAR(20),
 state VARCHAR(20),
 country VARCHAR(20),
 postal_code VARCHAR(20),
 CONSTRAINT pk_person PRIMARY KEY (person_id)
);
```



> NULL?
>
> NULL은 다음과 같은 경우에 사용됨
>
> * 해당사항 없음
> * 알 수 없음
> * 비어있는 셋
>
> 테이블에서 NULL을 지정하지 않으려면 NOT NULL을 지정하면 됨.



```sql
# favorite_food 테이블
CREATE TABLE favorite_food
(person_id SMALLINT UNSIGNED,
 food VARCHAR(20),
 CONSTRAINT pk_favorite_food PRIMARY KEY (person_id, food),# 한 사람이 좋아하는게 2개 이상일 수 있으니 고유성을 보장하기 위함
 CONSTRAINT fk_fav_person_id FOREIGN KEY (person_id) # person_id는 person테이블에 있는 것만을 사용하도록 제한
 REFERENCES person (person_id)
);
```



### 테이블 수정

##### 데이터 삽입

Insert 문의 세 가지 주요 구성요소는 다음과 같음.

* 데이터를 추가할 테이블 이름
* 데이터를 채울 테이블의 열 이름
* 열을 채울 값

**테이블의 모든 열이 NOT NULL로 정의되지 않는 한, 모든 열에 데이터를 제공할 필요도 없고, 나중에 update를 통해서 값을 지정할 수 있음**



##### 숫자 키 데이터 생성

person테이블에 데이터를 사입하기 전에 숫자 기본 키의 값이 생성되는 방법을 논의하면 유용함. 무작위로 숫자 뽑는 것 외에 몇가지 옵션이 있음

* 표에서 현재 가장 큰 값을 확인하고 값을 추가
* 데이터베이스 서버가 값을 제공하도록 함.

첫번째 옵션이 유효해 보일 수 있지만, 다중 사용자 환경에서는 동시성 문제때문에 문제가될 수 있다. 따라서 오라클 에서는 시퀀스라는 별도의 스키마 개체가 사용되고 MySQL에서는 기본 키 열에 대한 자동증가(auto-increment) 기능만 켜면 됨.



```sql
ALTER TABLE person MODIFY person_id SMALLINT UNSIGNED AUTO_INCREMENT;
```



```sql
# favorite_food 테이블에 외래키 제약에 오류가 발생하면 다음과 같이 처리
set foreign_key_checks=0;
ALTER TABLE person MODIFY person_id SMALLINT UNSIGNED AUTO_INCREMENT;
set foreign_key_checks=1;
```



##### INSERT문: 데이터를 추가

```sql
INSERT INTO person
	(person_id, fname, lname, eye_color, birth_date)
	VALUES(null, 'William', 'Turner', 'BR', '1972-05-27');
```

* 어떤 주소 열에도 값을 제공하지 않았지만, 해당 열들은 null이 허용되므로 문제없음.
* birth_date 열에 제공된 값은 문자열. 형식이 일치하는 경우 MySQL은 문자열을 날짜로 변환.
* 열 이름과 값은 각각 그 개수와 자료형이 일치해야함. 7개의 열에 이름을 지정하고 6개의 값만 제공하거나, 해당 열에 적합한 자료형으로 변환할 수 없는 값을 제공하면 오류가 발생

```sql
INSERT INTO favorite_food (person_id, food) VALUES (1, 'pizza');
INSERT INTO favorite_food (person_id, food) VALUES (1, 'cookies');
INSERT INTO favorite_food (person_id, food) VALUES (1, 'nachos');
```



##### XML데이터로 가져오는방법

```bash
mysql -u root -p --xml # 왼쪽과 같이 접속하면 모든 것들이 xml로 출력됨
```

```sql
#아래의 경우도 된다고 하지만 mariadb에서는 안됨
SELECT * FROM table_name FOR XML AUTO, ELEMENTS
```



##### 데이터 수정

```sql
UPDATE person
	SET street = '1225 Tremont st.',
	city = 'Boston',
	state = 'MA',
	country = 'USA',
	postal_code = '02138'
WHERE person_id = 1;
```



##### 데이터 삭제

```sql
DELETE FROM person WHERE person_id = 1
```



### 좋은 구문을 망치는 경우

#### 고유하지 않은 키본키

테이블 정의에는 기본 키 제약조건 생성이 포함되므로 MySQL은 중복 키 값을 테이블에 삽입하지 않도록 함.

```sql
INSERT INTO person
	(person_id, fname, lname, eye_color, birth_date)
	VALUES (1, 'Charles', 'Fulton', 'GR', '1968-01-15');
```

```bash
ERROR 1062 (23000): Duplicate entry '1' for key 'PRIMARY'
```



#### 존재하지 않는 외래 키

favorite_food 테이블에 입력된 person_id의 모든 값이 person 테이블에 존재함을 보증함. 이를 위반하는 행을 만들려 할때 일어나는 현상

```sql
INSERT INTO favorite_food (person_id, food)
	VALUES (999, 'lasagna');
```

```bash
ERROR 1452 (23000): Cannot add or update a child row: a foreign key constraint fails (`sakila`.`favorite_food`, CONSTRAINT `fk_fav_person_id` FOREIGN KEY (`person_id`) REFERENCES `person` (`person_id`))
```



#### 열 값 위반

enum값에 위반된 값을 넣는경우, 일어나는 현상

```sql
UPDATE person
	SET eye_color = 'ZZ'
	WHERE person_id = 1;
```

```bash
ERROR 1265 (01000): Data truncated for column 'eye_color' at row 1
```



#### 잘못된 날짜 변환

date 열을 채울 문자열을 구성할 때 문자열 예상 형식과 일치하지 않을 경우 일어나는 현상

```sql
UPDATE person
	SET birth_date = 'DEC-21-1980'
	WHERE person_id = 1;
```

```bash
ERROR 1292 (22007): Incorrect date value: 'DEC-21-1980' for column `sakila`.`person`.`birth_date` at row 1
```



```sql
UPDATE person
	SET birth_date = str_to_date('DEC-21-1980', '%b-%d-%Y')
	WHERE person_id = 1;
```

```bash
Query OK, 1 row affected (0.002 sec)
```
