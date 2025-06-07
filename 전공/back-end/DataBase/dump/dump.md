## 먼저 dump 파일이란?
기존에 있던 스키마와 DB 안에 있는 값들까지 모두 백업시켜 파일 형태로 만든것.


# 하는 법

## 준비물
* 구버전 데이터 그립 (2023.2)  or 인텔리 제이 (신버전은 커스텀이 자유롭게 안됨 바이너리 타입 깨짐 문제 발생)


1. 덤프 생성할 스키마에 import/Export -> Export with 'mysqldump'


2. Path to executable : 
C:\Program Files\MySQL\MySQL Server 8.0\bin\mysqldump.exe (윈도우 기준 덤프파일 생성하는 )


![[Pasted image 20250607233013.png]]


3. 인텔리제이에서 open 으로 생성한 dump 파일 불러오기

4. mysql 워크밴치에서 스키마 생성

5. 데이터 그립에서 생성한 스키마를 선택후 