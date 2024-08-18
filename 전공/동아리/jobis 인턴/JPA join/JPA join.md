join 이란? : 데이터베이스에서 두 개 이상의 테이블을 연결하여 하나의 결과 테이블로 만드는 작업을 의미한다.

양방향과 단방향 연관관계

@OneToMany 는

단방향(unidirectional)은 상대 엔티티에 @ManyToOne이 없는 경우

양방향(bidirectional)은 상대 엔티티에@ManyToOne이 있는 경우이다.

@ManyToOne 은

단방향은 상대 엔티티에 @OneToMany가 없는 경우,

양방향은 상대 엔티티에 @OneToMany가 있는 경우이다.

@OneToMany 양방향과 @ManytoOne 양방향은 기준만 다를뿐 똑같다.

RDB 는 단방향밖에 없다. 하지만 객체지향은 양방향

이러한 차이점을 해결하기 위해 jpa 라는 프레임 워크를 씀.

서로 참조하고 싶을땐 양방향을 쓴다.