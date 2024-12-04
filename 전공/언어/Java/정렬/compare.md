Comparator<>() 의 메서드로 Override 하여 사용함

o1 < o2 이라면 -1 반환 (기준값 한칸 앞으로 이동)
o1 == o2 이라면 0 반환(기준값 그대로 있음)
o1 > o2 이라면 1 반환 (기준값 한칸 뒤로 이동)

compare 는 객체를 정렬하는 메서드기 때문에 래퍼타입을 인자로 받아야함.

예제
``` java
Arrays.sort(arr,new Comparator<>()

	@Override
	public int compare(Integer o1, Integer o2){
	return o1-o2;
	}
)
```
