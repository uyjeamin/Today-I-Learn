
### IAM 사용자란?  -> 일부 필요한 권한만 준 계정

>[!note] 루트계정을 그대로 쓰면 보안상 위험할 때가 있기 때문에 IAM 사용자를 만든다
>

IAM -> 사용자 -> 사용자 생성

이름 입력 후

``[] IAM 사용자를 생성하고 싶어요 
칸 클릭

권한 설정은 **직접 정책 연결로**

**정책은** 
## EC2 만 쓴다면 ?
``AmazonEC2FullAccess

## S3 도 쓴다면 ?
``AmazonS3FullAccess

## ECR 도 쓴다면 ?
``AmazonECRFullAccess

*  IAM 사용자는 ssh 로 ec2 에 접속 못함 (pem 키로 접속 못함)