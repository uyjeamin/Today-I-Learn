
## @PostConstructor 란?

Bean 이 처음 생성될때(주로 어플리케이션이 실행될때) 함수위에 붙여놓으면 딱 한번만 그 함수를 실행함. 
주로 초기설정할때 사용됨.
```java

@PostConstruct 
private void init() { 
// 초기화 처리 User admin = new User("admin", "admin password"); 
User normalUser = new User("user", "user password"); 
userRepository.save(admin, normalUser);
}
```



## @PreDestory 란?

Bean 이 삭제하기 직전에 @PreDestory가 붙은 함수를 한번 실행한다.
주로 마무리작업이나 자원 반환등 종료처리를 함
```java
@Configuration  
@EnableScheduling  
class TaskSchedulerConfig {  
  
        private val scheduler = ThreadPoolTaskScheduler()  
    @Bean  
    fun taskScheduler(): ThreadPoolTaskScheduler {  
        scheduler.poolSize = 5  
        scheduler.setThreadNamePrefix("TeskScheduler-")  
        scheduler.initialize()  
        return scheduler  
    }  
  
    @PreDestroy  // 안전하게 종료
    fun shutdown(){  
        scheduler.shutdown()  
    }  
}

```



