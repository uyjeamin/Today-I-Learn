
1. 도커 깔기
```
sudo apt-get update && \
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common && \
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - && \
sudo apt-key fingerprint 0EBFCD88 && \
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" && \
sudo apt-get update && \
sudo apt-get install -y docker-ce && \
sudo usermod -aG docker ubuntu && \
newgrp docker && \
sudo curl -L "https://github.com/docker/compose/releases/download/2.27.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose && \
sudo chmod +x /usr/local/bin/docker-compose && \
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

```

2. 쿠버네티스 k3s 깔기
```
curl -sfL https://get.k3s.io | sh -
```


3. vi 로 deployment.yaml 파일, service.yaml 파일 만들기(또는 필요한 매니패스트 파일)

```deployment.yaml
# Deployment 사용법 묶음  
apiVersion: apps/v1  
kind: Deployment  
  
metadata:  
  name: spring-deployment  
  
# Deployment 세부 정보  
spec:  
  replicas: 5  
  selector:  
    matchLabels:  
      app: backend-app  
  
# 배포할 Pod 정의  
  template:  
    metadata:  
      # 카테고리  
      labels:  
        app: backend-app  
    spec:  
      containers:  
        - name: spring-container  
          image: spring-app:1.1  
          imagePullPolicy: IfNotPresent  
          ports:  
            - containerPort: 8080
```


```service.yaml
  
# 한묶음  
apiVersion: v1  
kind: Service  
  
metadata:  
  name: spring-service  
  
spec:  
  type: NodePort  
  # Pod 정의 backend-app 이라고 레이블에 정의해 논 파드에 연결하겠다.  
  selector:  
    app: backend-app  
  ports:  
    - protocol: TCP  
      port: 8080 # 서비스의 포트 번호  
      targetPort: 8080 # 파드내 컨테이너가 리슨하고 있는 포트 (파드는 port 가 없고 ip 로 되어있다)  
      nodePort: 30000 # 사용자가 외부에서 접근할때 사용할 포트 번호
```