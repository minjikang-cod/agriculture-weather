# 날씨에 따른 농산물 거래 현황

### 개요
- 프로젝트 소개
    	날씨 변화와 농산물의 생산성(가격, 생산량)의 상관성을 파악하고자 함
***
## DATA  
#### 사용 API  

   1. 전국 도매시장 일별 정산 경락가격 상세정보  
  <https://data.mafra.go.kr/opendata/data/indexOpenDataDetail.do?data_id=20161229000000000736&service_ty=O>
          
   2. 기상청_지상(종관, ASOS) 일자료 조회서비스  
        <https://www.data.go.kr/tcs/dss/selectApiDataDetailView.do?publicDataPk=15059093>
          
   3. 농촌진흥청 국립농업과학원_농업기상 기본 관측데이터 조회  
        <https://www.data.go.kr/tcs/dss/selectApiDataDetailView.do?publicDataPk=15078057>

#### 사용 API별 설명  
__1. 전국 도매시장 일별 정산 경락가격 상세정보 api__  

![경락](https://drive.google.com/uc?id=1YxZ04adLRZz2XoPfpYXXNI5XnFHs9l2N)  
   - __추출변수__  
    경락일, 공영 도매시장명, 공영 도매시장 코드, 법인명, 법인코드, 품목명, 품목코드, 품종명, 품종코드, 경락가, 거래단량, 규격, 규격코드, 산지코드, 산지명, 거래량  
   - __문제상황__  
    	 추출변수 중 '산지명'의 데이터에 __결측__ 이 많음  
       - 농수축산식품 공공데이터 포털에서 [신구표준산지목록조회] 데이터셋 활용  
       - 경락데이터(읍면동)와 산지코드데이터(시군구)가 지역분류가 달라 산지코드가 서로 일치하지 않는 문제 발생  
       - 총 6자리의 산지코드 숫자중 앞 3자리만으로 비교하는 방법으로 해결


__2. 기상청_지상(종관, ASOS) 일자료 조회서비스 api__  

![기상청](https://drive.google.com/uc?id=1zp8gCx4jRDt7nkY7nRVlnULIQ555V_Ap)  
   - __추출변수__  
    	 지점번호, 시간, 평균기온, 평균지면온도, 평균상대습도, 평균풍속, 일강수량, 합계일사량, 합계일조시간  
       
   - __문제상황__  
     __지점번호__ 는 있지만 그에 해당하는 __지점명__ 은 없었음  
        - 직접 지점번호와 그에 해당하는 지점명 데이터셋을 생성  
       - 지점번호와 지점명을 연결시키는 방법으로 해결  
       

__3. 농촌진흥청 국립농업과학원_농업기상 기본 관측데이터 조회 api__  

![농업기상](https://drive.google.com/uc?id=1n4VEB7LlPF4u7x5UK1x9Z3OTit74bNOc)  
   - __추출변수__  
  관측지점코드, 관측지점명, 관측일자, 기온, 습도, 풍속, 강수량, 일조시간, 일사량, 초상온도, 지중온도  
       
   - __문제상황__  
       1. '관측지점코드'로 api를 호출하기 때문에 각 지점의 지점코드를 알아야 함  
           -__농촌진흥청 농업기상 관측지점 상세정보 api__ 에서 (관측지점코드, 관측지점명, 설치주소)를 불러옴  
           -관측지점코드를 리스트로 뽑아서 for문을 통해 모든 관측지점에 대한 농업기상데이터를 뽑아내는 방법으로 해결  
       2. 관측지점명은 (시군구 읍면동)의 형태이고 우리는 (시도 시군구)가 필요함  
           -__설치주소__ 에서 띄어쓰기 기준 앞 2개를 추출하는 방법으로 해결  
            
            
#### 전처리  
1. 기상청 데이터와 농업기상 데이터의 column을 동일하게 변경  
   - 변수명 : 관측지점명, 관측일자, 기온, 습도, 풍속, 강수량, 일조시간, 일사량, 초상온도, 시군구명, 시도명  
2. 두 데이터를 결합하여 __기상데이터__ 생성  
3. 경락 데이터의 __충북, 전남__ 과 같은 값을 __충청북도, 전라남도__ 의 값으로 변경  
4. 경락 데이터의 __20200101__ 과 같은 값을 __2020-01-01__ 과 같은 형식의 값으로 변경  
5. 일자, 시도명, 시군구명 을 key값으로 경락데이터와 기상데이터를 결합하여 __경락기상_통합__ 데이터 생성  


***
## Dashboard  

![대시보드](https://drive.google.com/uc?id=1wCpowf04WQqn3z9z5smkTawflzORG6mN)  
- 전처리가 끝난 데이터를 Tableau에 올려 대시보드를 생성한다  
- 태블로 소스코드에 대한 설명. (민지님께서... 부탁드립니다...)
