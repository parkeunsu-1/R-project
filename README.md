# 602277107 박은수
## `[11월9일]`
### 1. 불필요한 부분 자르기
```
서울시 경계선을 기준으로 외곽의 불필요한 부분 잘라내기
crop() 함수를 이용해 외곽선 기준으로 래스터 이미지를 잘라낸다.
```
### 2. 지도 그리기
> 1. leaflet() 으로 지도 라이브러리 사용 선언
> 2. addProviderTiles() 옵션으로 지도 기본 테마 설정
> 3. addPolygon() 은 서울시 외곽선을 불러옵니다.
> 4. addRasterImage() 함수를 이용해 지도 위에 래스터 이미지를 올립니다.

### 3. 이전/이후 데이터 세트 만들기
```
kde_before 는 기준일인 2021-07-01 이전 데이터만 추출하고, kde_after 는 이후 데이터만 추출하여 kde_diff$diff에 입력합니다.
```

### 4. 마커 클러스터링 시각화하기
```
leaflet()으로 나타낸 지도에 addCircleMarkers()로 avg.formula라는 마커 클러스터링 기능을 추가합니다.
이때, lng은 경도, lat는 위도를 나타냅니다.
또한 addRasterImage() 로 최고가와 급등지 래스터 이미지를 지도 위에 표현합니다.
```

## `[11월02일]`
### 1. 지역별 평균 가격 구하기
```
setwd(dirname(rstudioapi::getSourceEditorContext()$path)) # 작업폴더 설정
load("./06_geodataframe/06_apt_price.rdata")   # 실거래 불러오기
library(sf)    # install.packages("sf") 
grid <- st_read("./sigun_grid/seoul.shp")     # 서울시 1km 그리드 불러오기
apt_price <-st_join(apt_price, grid, join = st_intersects)  # 실거래 + 그리드 결합
head(apt_price,2)


kde_high <- aggregate(apt_price$py, by=list(apt_price$ID), mean) # 그리드별 평균가격(평당) 계산
colnames(kde_high) <- c("ID", "avg_price")   # 컬럼명 변경
head(kde_high, 2)     # 확인

```
### 2. 평균 가격 정보 표시하기
> merge() 함수를 이용해 공간 결합 한다. 그러면 kde_high에 서울 시 지역코드(1100)을 나타내는 SIG_CD 와 그리드별 공간정보인 geometry 데이터가 추가됨

### 3. 지도 경계 그리기
> 데이터가 집중된 곳 찾기 -> 커널 밀도 추정 이용 

### 4. 밀도 그래프 표시하기
```
커널 밀도 추정할때는 지도 경계선(win) 내의 포인트 분포 데이터로 커널 밀도를 계산해야한다.
pop() 함수는 위도와 경도(x, y)를 포인트로 변환
평당 평균가를 가중치 옵션으로 설정
```

### 5. 래스터 이미지로 변환하기
> "빅데이터를 잘다룰려면 노이즈를 최소화하고 의미있는 신호를 찾아내는 것이 중요하다"

## `[10월26일]`
### 1. 좌표계란?
> 지오 코딩으로 평면의 지도 위에 위치를 표현할수 있는 위경도 정보를 수집하였으나, 우리가 살아가는 지구는 불규칙한 타원체라 실제 좌표값을 표현하려면 
보정해야한다. -> 이 보정 기준이 바로 좌표계 이다.
### 2. sp? sf?
> sp 패키지는 기본적인 정보를 살펴볼수는 있지만, 데이터 일부를 편집하거나 수정하는것은 어렵다.
> sf 패키지는 sp 패키지가 가지고 있던 기능과 속성을 그대로 이어받지만, 기존의 데이터프레임과 비슷하게 편집하거나 수정할수있다.

### 3. 데이터 불러오기 
```
1. geo_dataframe.R 파일 생성
2. setwd(dirname(rstudioapi::getSourceEditorContext()$path))
  load("./04_preprocess/04_preprocess.rdata")    # 주소 불러오기
  load("./05_geocoding/05_juso_geocoding.rdata") # 좌표 불러오기
```
### 4. 주소와 좌표 결합하기
```

library(dplyr)   # install.packages('dplyr')
apt_price <- left_join(apt_price, juso_geocoding, 
                       by = c("juso_jibun" = "apt_juso")) # 결합
apt_price <- na.omit(apt_price)   # 결측치 제거
```
### 5. 지오 프레임 생성하기
```
library(sp)    # install.packages('sp')
coordinates(apt_price) <- ~coord_x + coord_y    # 좌표값 할당
proj4string(apt_price) <- "+proj=longlat +datum=WGS84 +no_defs" # 좌표계(CRS) 정의
library(sf)    # install.packages('sf')
apt_price <- st_as_sf(apt_price)     # sp형 => sf형 변환
```

## `[10월12일]`
### 1. 필요한 칼럼만 추출하기
```
apt_price <- apt_price %>% select(ymd, ym, year, code, addr_1, apt_nm, 
                                  juso_jibun, price, con_year,  area, floor, py, cnt) # 칼럼 추출
head(apt_price, 2)  # 확인
```

### 2. 전처리 데이터 저장하기
```
setwd(dirname(rstudioapi::getSourceEditorContext()$path))
dir.create("./04_preprocess")   # 새로운 폴더 생성
save(apt_price, file = "./04_preprocess/04_preprocess.rdata") # 저장
write.csv(apt_price, "./04_preprocess/04_preprocess.csv") 
```

### 3. 지오코딩 준비하기
> 지오 코딩이란 ? 문자로 된 주소를 위도와 경도라는 숫자로 변환
> 카카오맵 API
> REST API 방식으로 이용
```
1. 카카오 개발자 사이트(developers.kakao.com) 에서 카카오 계정으로 로그인
2. 내 애플리케이션 클릭
3. 애플리케이션 추가하기 앱이름, 사업자명 입력후 저장
4. REST API 키 발급
```
### 4. 예외 처리
> tryCatch({}) 로 예외처리하여 오류가 발생하여도 반복문을 멈추지 않고 다음 반복으로 건너뛰도록함

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

### 5. 데이터 변환
> 1. sub(",","",.) -> 쉼표 제거
> 2. as.numeric() -> 문자를 숫자로 변환

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
