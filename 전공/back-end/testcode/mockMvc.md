
## 실제로 post 맨으로 요청보내서 url test 하지 않아도 컨트롤러를 테스트해준다.
 
 * mockMVC 는 톰캣을 거치지 않고 내부적으로 http 요청을 만들어 요청을 컨트롤러 테스트를 한다.
 * mocking(모킹) 을 통해 실제 동작은 하지 않지만 예상된 반환값을 반환하는 가짜 객체를 만들어 테스트를 진행함.
 * 인수테스트, 통합 테스트시 활용됨

```java
import static org.mockito.Mockito.*;
import org.junit.jupiter.api.Test;
import org.mockito.Mockito;

class UserServiceTest {
    
    @Test
    void testGetUser() {
        UserRepository mockRepo = mock(UserRepository.class); // 가짜 객체 생성
        when(mockRepo.findById(1L)).thenReturn(Optional.of(new User(1L, "John"))); // 동작 정의
        
        UserService userService = new UserService(mockRepo);
        User user = userService.getUser(1L);
        
        assertEquals("John", user.getName()); // 검증
    }
}

```
이런식으로 모킹을 사용하면 DB 를 사용하지 않아도 테스트를 할 수 있다.