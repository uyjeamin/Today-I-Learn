위의 셋 어노테이션 모두 검증 인터페이스의 모음인 bean Vaildation에 속한 어노테이션이다.

* @NotNull : null만 허용하지 않음.
=> " "공백, ""(초기화 된 string) 은 허용함

* @NotEmpty : null과 ""를 둘다 허용함.
=> " "(공백) 은 허용.

* NotBlank : null과 ""(초기화된 string), " "(공백) 모두 허용 X

