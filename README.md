# 데이터분석 캡스톤디자인 연구 

# 주제 : 주가 데이터 Back-Testing을 통한 투자 기법 검증 및 예측

### 산업경영공학과 16학번 김수영

***과제 주요 내용
1) 군집 분석을 통해 유사한 주가 흐름을 보이는 종목들을 Clustering
2) 기업의 Fundamental, 주가의 추세와 거래량 등 기본적, 기술적 분석 요소에 기반한 통계 분석
3) 가장 예측 확률이 높은 분석 지표 찾아내기(또는 새로운 지표 만들어내기)
4) RNN 등의 AI를 통한 주가 예측**

## 1. 데이터 셋 확보

### 1) 변수 데이터 셋

![Untitled](https://user-images.githubusercontent.com/44190559/102570253-4eb74e80-412a-11eb-8239-2d62d920e571.png)


- 향후 머신 러닝에 사용될 수 있는 변수 데이터 셋을 확보
1. 원달러환율 : KO-US Exchange, 일별 원/달러 환율 
2. 거래대금 : KOSPI 시장 전체에서 거래된 주식의 일별 총 거래대금 (단위 : 1억원)
3. 외국인 순매수 : KOSPI 시장 전체에서 외국인의 일별 총 순매수량 (단위 : 1억원)
4. 전체시총 : KOSPI 시장의 일별 전체 시가총액 (단위 : 1억원)

### 2) 주가 데이터 셋
![Untitled 1](https://user-images.githubusercontent.com/44190559/102569967-c20c9080-4129-11eb-8c5c-b9e5fb43de8c.png)


- 2016년 1월 초부터 현 시점까지의 약 5년 분량의 주가 데이터 (2016.01.04 ~ 2020.09.29)
- Yahoo Finance API를 이용하여 코스피 전 종목(795개)에 대한 데이터 수집

## 2. 전처리

### 1) 주가 데이터 전처리
![Untitled 2](https://user-images.githubusercontent.com/44190559/102570274-58d94d00-412a-11eb-88cd-128bdf231867.png)



- Min-Max 정규화 진행하지 않음
- 그러나 K-Means 클러스터링의 경우 data point의 거리를 기준으로 클러스터가 형성되기 때문에 주식의 단위 가격에 영향을 받지 않게 scaling함
- 대신 모든 종목의 2016년 1월 4일 가격을 100원이 되게끔 변형함
- K-Means Clustering 및 시각화를 위해서만 100원으로 Scaling 하였으며, 분석에서는 Raw data 사용
- Null 값은 제거

[주가 전처리 예시 → 한화생명](https://www.notion.so/dcbfde59216c4376abd6e9cb99c4993d)

### 2) 분석 변수 데이터 전처리

![Untitled 3](https://user-images.githubusercontent.com/44190559/102570314-6e4e7700-412a-11eb-9647-17fabdcb8f92.png)

- KRX(한국거래소) 사이트에서 월별 KOSPI 전 종목의 기본적 분석 지표 수집
- PER(주가수익비율)에서 결측치가 생기는 경우(해당분기 적자인 경우)
→ 0으로 치환

## 3. Clustering

### 주가 데이터 클러스터링

- 일별 가격을 기준으로 클러스터링 진행 ( 날짜 하나하나가 attribute가 되는 것)
- K-Means Clustering을 이용하였으며 최적의 클러스터 개수 선정에는 Elbow Model을 이용(SSE를 최소화하는 개수 선정)
- **Elbow Model을 통한 클러스터 평가** → SSE 감소 비율이 급격히 작아지는 시점인 3 선정, 클러스터의 개수 3개로 선정

![Untitled 4](https://user-images.githubusercontent.com/44190559/102570332-79090c00-412a-11eb-8a86-1a453675f468.png)


- **Silhouette analysis를 통한 클러스터 평가**
![Untitled 5](https://user-images.githubusercontent.com/44190559/102570356-83c3a100-412a-11eb-900f-9cffcfdaea6a.png)



- Clustering 결과
![Untitled 6](https://user-images.githubusercontent.com/44190559/102570368-8cb47280-412a-11eb-9d33-f276d20720d2.png)


- 클러스터 별로 각 클러스터 내 종목들의 일별 주가를 평균을 낸 후 하나의 그래프에 도식한 결과
- 2016년 1월의 클러스터 평균 주가를 100원이라고 하였을 때 **(총 3개 클러스터, 719개 종목)**
2020년 9월 클러스터 0의 평균 주가는  약 65원 (-35%) → "**하락주", 439개 종목**
2020년 9월 클러스터 1의 평균 주가는  약 142원 (+42%) → "**성장주", 246개 종목**
2020년 9월 클러스터 2의 평균 주가는  약 397원 (+297%) → "**급등주", 34개 종목**

- **분석 지표 통합**

![Untitled 7](https://user-images.githubusercontent.com/44190559/102570404-9b9b2500-412a-11eb-8d69-9ad3aa677b8b.png)


- 기본적, 기술적 분석에 이용될 수 있게끔 분류된 Cluster와 분석 지표 데이터를 통합

## 4. Back-Testing을 통한 투자 기법 검증

### 0) 개요

- Cluster 별로 최적의 투자 전략을 요약

- 투자 기법 별 Grid-Search를 통한 최적의 전략과 투자 기간 산출

![Untitled 9](https://user-images.githubusercontent.com/44190559/102570424-a786e700-412a-11eb-909f-8e8a93e37361.png)
![Untitled 8](https://user-images.githubusercontent.com/44190559/102570432-ac4b9b00-412a-11eb-945c-408d2c57f050.png)


### 1) Cluster 0 (하락주 클러스터)

- PBR ≤ 1 인 종목에 대해 투자
- 5일 연속 하락한 종목에 대해 투자
- 볼린저 밴드 하단에서 15~50일 간 횡보 후 상승 시 투자
- 580일 이상 투자

### 2) Cluster 1 (성장주 클러스터)

- 5≤ PBR ≤ 19인 종목에 대해  투자
- 0.1 ≤ PER ≤ 1.0인 종목에 대해 간 투자
- 4일 연속 하락한 종목에 대해 투자
- 볼린저 밴드 하단에서 45~55일 간 횡보 후 상승 시 투자
- 580 ~ 610일 간 투자

### 3) Cluster 2 (급등주 클러스터)

- PBR ≤ 3 인 종목에 대해 투자
- PER ≤ 4 인 종목에 대해 투자
- 2일 연속 하락한 종목에 대해 투자
- 볼린저 밴드 하단에서 30~50일 간 횡보 후 상승 시 투자
- 580 ~ 670일 간 투자

## 5. RNN 모형

- 기존 일별 지표 데이터에서 특정 기법에 맞는 상황인 경우 가중치를 주는 Weighted Data Set 구성
- LSTM Model

![Untitled 10](https://user-images.githubusercontent.com/44190559/102570456-b53c6c80-412a-11eb-8e1a-0b7dfd8b47f9.png)


- Simple한 RNN 구성

![Untitled 11](https://user-images.githubusercontent.com/44190559/102570485-c2f1f200-412a-11eb-9a1e-12decf0a2f82.png)
![Untitled 12](https://user-images.githubusercontent.com/44190559/102570487-c4231f00-412a-11eb-8f4d-e42159138861.png)

- 결과

![Untitled 13](https://user-images.githubusercontent.com/44190559/102570497-ceddb400-412a-11eb-88fa-3675b1748521.png)

    - 파라미터 조정

    - 가격을 연속적으로 예측한 결과
    - MAPE: 6.34

![Untitled 14](https://user-images.githubusercontent.com/44190559/102570520-de5cfd00-412a-11eb-9ec6-beaf9abee574.png)


    - 향후 5일간 평균을 연속적으로 예측한 결과
    - MAPE : 3.65→낮아짐!

![Untitled 15](https://user-images.githubusercontent.com/44190559/102570538-e6b53800-412a-11eb-8dd0-866fc6770e65.png)


    x일 간 평균 , 그 때 MAPE 

![Untitled 16](https://user-images.githubusercontent.com/44190559/102570543-eddc4600-412a-11eb-9cb5-8a9b2334caf3.png)


    **Cluster 별 모델링 결과 요약**

    C2 > C1 >C0

    급등주 예측에 효과적

![Untitled 17](https://user-images.githubusercontent.com/44190559/102570557-f6cd1780-412a-11eb-87a6-7540e528ad38.png)

