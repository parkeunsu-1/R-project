# 602277107 박은수
## `[10월05일]`
### 1. 데이터 100건으로 수정
```
datelist <- seq(from = as.Date('2021-01-01'), # 시작
                to   = as.Date('2021-04-30'), # 종료 100개
                by    = '1 month')
```
### 2. 통합데이터 저장하기
```
dir.create("./03_integrated")   # 새로운 폴더 생성
save(apt_price, file = "./03_integrated/03_apt_price.rdata") # 저장
write.csv(apt_price, "./03_integrated/03_apt_price.csv") 

-> save와 write.csv로 RDATA와 CSV 형식으로 저장
-> RDATA 형식은 R에서 데이터를 저장하거나 관리하기에는 편리하고 효율적이지만 엑셀등 다른 프로그램에서 내용을 보기 어려워 CSV 형식으로도 저장하여 중간에 내용을 쉽게 확인할수있습니다.
```

### 3. 수집한 데이터 불러오기
> 1. options(warn=-1) -경고 메시지 무시
> 2. table(is.na(apt_price)) - 결측값 False, True 로 확인가능 - True 가 결측값(NA) 갯수
> 3. apt_price <- na.omit(apt_price) - 결측값 제거
> 4. table(is.na(apt_price) - 결측값 확인

### 4. 문자열 공백 처리
> 1. library(stringr) -> 문자열 처리 패키지
> 2. apt_price <- as.data.frame(apply(apt_price, 2, str_trim)) -> 공백제거( 1이면 행, 2면 열에 적용)(str_trim은 함수)

## `[9월28일]`
### 1. 자료 요청하고 응답받기
```
for(i in 1:length(url_list)){   # 요청목록(url_list) 반복
  raw_data[[i]] <- xmlTreeParse(url_list[i], useInternalNodes = TRUE,encoding = "utf-8") # 결과 저장
  root_Node[[i]] <- xmlRoot(raw_data[[i]])	# xmlRoot로 추출
```

### 2. 전체 거래 건수 확인하기
```  
items <- root_Node[[i]][[2]][['items']]  # 전체 거래내역(items) 추출
size <- xmlSize(items)                   # 전체 거래 건수 확인 
```
### 3. 개별 거래 내역 추출하기
```
  item <- list()  # 전체 거래내역(items) 저장 임시 리스트 생성
  item_temp_dt <- data.table()  # 세부 거래내역(item) 저장 임시 테이블 생성
  Sys.sleep(.1)  # 0.1초 멈춤
  for(m in 1:size){  # 전체 거래건수(size)만큼 반복
    #---# 세부 거래내역 분리   
    item_temp <- xmlSApply(items[[m]],xmlValue)
    item_temp_dt <- data.table(year = item_temp[4],     # 거래 년 
                               month = item_temp[7],    # 거래 월
                               day = item_temp[8],      # 거래 일
                               price = item_temp[1],    # 거래금액
                               code = item_temp[12],    # 지역코드
                               dong_nm = item_temp[5],  # 법정동
                               jibun = item_temp[11],   # 지번
                               con_year = item_temp[3], # 건축연도 
                               apt_nm = item_temp[6],   # 아파트 이름   
                               area = item_temp[9],     # 전용면적
                               floor = item_temp[13])   # 층수 
    item[[m]] <- item_temp_dt}    # 분리된 거래내역 순서대로 저장
  apt_bind <- rbindlist(item)     # 통합 저장
```

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
