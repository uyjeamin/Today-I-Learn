____
1. **@Entitiy** : jpa 를 사용해 테이블을 연결시킬 클래스에게 붙여주는 어노테이션이다. jpa가 해당 클래스를 관리하게 해준다.

**속성**

name : jpa에서 사용할 엔티티의 이름을 지정함

name을 쓰지 않을 경우 default 로 클래스 이름이 엔티티 이름이 됨.

```java
@Entity(name = "test1")
public class User {}
```

name 을 “test1”로 함으로서 test1 이란 테이블이 생성되었다.

1. **@Table** 엔티티랑 메핑할 테이블을 지정

**속성**

name : 메핑할 테이블 이름을 지정. (생략시 Entity 이름으로 테이블이 만들어 진다.)

```java
@Entity
@Table(name = "DB1")
```

이름이 “DB1” 인 테이블이 추가된다.

```java
@Entity(name = "test1")
@Table
```

이름이 “test1” 인 테이블이 만들어짐.

<추가>

1. **@Id : 특정 속성을 기본키로 설정하는 어노테이션.**

```java
@Entity
@Table(name = "DB1")
public class test1 {
@Id
private Long id;
private Stirng name;
```

**@Id 어노테이션 만을 사용하여 기본키를 직접 할당해주는 방법이있다.**

하지만 보통 DB를 설계할때는 직접 부여하지 않는다.

**기본키를 자동으로 생성하는 4가지 방법.**

```java
@Entity
@Table(name = "DB1")
public class test1 {
@Id
@GeneratedValue(strategy = GenerationType.~)
private Long id;
private Stirng name;
```

@GeneratedValue 어노테이션을 사용하면 기본키를 자동으로 DB에서 생성하는 전략을 사용할수 있다.

**IDENTITY - @GenerationValue(strategy = GenerationType.IDENTITY)** : 기본키 생성을 DB에 위임(Mysql) (기본키를 1부터 시작해서 계속 늘려감.)

**SEQUENCE - @GeneratedValue(strategy = GenerationType.SEQUNCE) :** DB 시퀀스를 사용해 기본키 할당 (ORACLE)

**TABLE - @GeneratedValue(strategy = GenerationType.TABLE) :** 키 생성 테이블에 사용 ( 모든 DB 사용 가능)

**AUTO - @GeneratedValue(strategy = GenerationType.AUTO) :** 선택된DB에 따라 자동으로 전략 선택

1. **@Column : 객체 필드를 테이블 컬럼과 매핑**

```java
@column
private int age;
```

**속성들**

name :필드와 매핑할 테이블의 컬럼 이름 지정.

insertable = true : 엔티티 저장시 필드값 저장. (= false : 엔티티 수정시 값이 수정되지 않음)

updatable = true : 엔티티 수정시 값이 수정 , = false : 엔티티 수정시 값이 수정되지 않음.

table : 하나의 엔티티를 두 개 이상의 테이블에 매핑할때 사용.

nullable : null값 허용 여부 설정 false : not null 제약 조건./

unique : 컬럼에 유니크 제약조건 부여.

columnDefineition : 데이터베이스 컬럼 형 ex : VARCHAR(30)을 직접 부여
```java
@column(columndifineition = "VARCHAR(200)") private String bookname;

```
