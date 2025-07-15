
객체.copy() 메서드는 데이터클래스에서 재공하는 메서드로 만약 객체를 정의한 클래스에 원시타입만 있다면 ``깊은 복사``로 데이터만 복사한 객체를 반환한다. 복사한 객체를 바꿔도 원래객체에 영향 X

만약 참조타입이 있을경우 ``얕은 복사`` 로 똑같은 주소값을 가진 객체를 반환한다. 그럼으로
복사한 객체가 바뀌면 원래 객체도 그 값으로 바뀐다. 

```kotlin 
data class Person(
val name : String;
val cars : ArrayList<String>
)

fun main{
	val person = Person("재민",arrayListOf("밴츠","페라리"))
	val copyPerson = person.copy()
	copyPerson.name("민지")
	print(person) // person(name=민지,cars=[밴츠,페라리])
}

```