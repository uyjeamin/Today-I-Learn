@Enumerated란 Enum 타입의 필드를 데이터베이스에 저장할 때 해당 Rnum 값을 어떻게 저장할 것인지를 설정하는 어노테이션

- **`EnumType.STRING`**: Enum의 이름(문자열)을 데이터베이스에 저장합니다.
- **`EnumType.ORDINAL`**: Enum의 순서(정수값)를 데이터베이스에 저장합니다