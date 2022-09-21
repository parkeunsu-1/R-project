# 602277107 박은수
## `[9월21일]`
### 1. 변수 지우기
```
콘솔에서
1. ls() -> 변수 확인
2. rm(변수명) -> 해당 변수명 삭제
3. rm(list=ls()) -> 모든 변수 삭제
```
### 2. 콘솔
* 콘솔에서 getwd() -> 현재 작업디렉토리 확인가능
* R > session > set working directory > choose directory > 작업디렉토리 경로 지정
* Source with Echo -> 코드와 코드 결과가 같이 출력됨

### 3. paste()
```
paste(1,2,3,4,5) -> 1 2 3 4 5 출력
paste('function','in','r') -> function in r 출력
test <- c(1,2,3,4,5) 
paste(test) -> '1','2','3','4','5' 출력
```
> 분리되어있으면 하나로 연결하고, 묶여있으면 분리시킨다.

### 4. 서비스키
```
service_key <- "3wwDr%2BMDMccvqptpResvpBkp%2FwZYqqgLR7ENtnvYcLpsAhmZKByU%2F8ORTnhFGhg6q7SuJc9B6COksVII0pnWtA%3D%3D"
```

## `[9월14일]`
### 1. 작업 폴더 설정하기
```
C:\Program Files\R\R-4.2.1\bin\x64 경로 복사
visual studio code 에서 좌측 확장 아이콘 클릭후 R 검색해서 다운로드
visual studio code에서 좌측 하단 설정 -> 설정 -> 확장 -> R -> Rpath:window -> 복사한 경로 입력
```
* C:\r_project 폴더 생성
* 파일 내 data_coll.R 파일 생성
* visual studio code 와 연동됨

## `[9월7일]`
### 1. api 인증키 얻기
```
공공데이터포털(data.go.kr) 접속
국토교통부 아파트매매 실
