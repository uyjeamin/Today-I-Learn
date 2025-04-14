```yaml
# Pod 사용법(그냥 외우셈)
apiVersion: v1  
kind: Pod  
  
metadata: # Pod 에 대한 기본 정보  
  name: spring-pod-1  
  
spec: # Pod 에 대한 세부적인 정보  
  containers:  
    - name: spring-container  
      image: spring-app  
      ports:  
        - containerPort: 8080 # 문서같은 역활  
      imagePullPolicy: IfNotPresent #이미지 풀 정책 : 현재(로컬)에 없을때만 외부(dockHub) 에서 이미지 풀받아옴
```
