## 먼저 dump 파일이란?
기존에 있던 스키마와 DB 안에 있는 값들까지 모두 백업시켜 파일 형태로 만든것.


# 하는 법

## 준비물
* 구버전 데이터 그립 (2023.2)  or 인텔리 제이 (신버전은 커스텀이 자유롭게 안됨 바이너리 타입 깨짐 문제 발생)


1. 덤프 생성할 스키마에 import/Export -> Export with 'mysqldump'


2.1 
Path to executable : 
C:\Program Files\MySQL\MySQL Server 8.0\bin\mysqldump.exe 

윈도우 기준 "mysqldump 도구" (데이터 덤프를 하기 위한 명령줄 도구) exe 파일 경로 추가

2.2 
기본적으로 나와있는 문자열에 
--default-character-set=utf8mb4 --hex-blob 추가

각각 default 인코딩을 utf-8 로 한다는 것과 바이너리 타입의 데이터를 깨진 문자가 되지 않게 하는 속성들이다.


3. Run 클릭


![[Pasted image 20250607233013.png]]


5. mysql 워크밴치에서 스키마 생성

6. 코드 덤프 sql 가져와서 실행