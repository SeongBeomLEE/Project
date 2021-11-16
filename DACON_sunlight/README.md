# 동서발전 태양광 발전량 예측 AI 경진대회

## 소개
234팀 중에 6위의 성적을 냈던 대회로, 좋은 기회가 되어 최종 발표 까지 올라갈 수 있었다.

데이터 분석 대회에서 처음으로 DNN 모델을 통해서 좋은 성적을 거두었고,

총 30일간 실제 발전량으로 바탕으로 매일 평가가 이루어졌기 때문에, 실제 실무와 유사한 예측 프로세스를 경험할 수 있었던 대회이다.

## 내용

### 1. 데이터 수집

- 날씨 데이터 (기상청)
    - 2016년 12월 ~ 추가 데이터 수집
    - Temperature, PrecipitationForm, PrecipitationProb, Humidity, WindSpeed, WindDirection, Cloud, Precipitation
- energy 데이터 (공공데이터포털)
    - 2016년 12월 ~ 추가 데이터 수집
    - dangjin_floating 제외 (2018년 이전 데이터 존재X)
- 태양의 고도 및 방위각 데이터 (한국천문연구원)
    - 2016년 12월 ~ 추가 데이터 수집
    - 방위각, 고도, 적경, 적위

### 2. 데이터 전처리

- 가장 최신의 예보를 바탕으로 선형보간
- 각 발전소별로 energy 결측치가 존재하는 날짜 제거

### 3. 피쳐 엔지니어링

- 각도와 시간을 나타내는 칼럼은 모두 sin, cos 값을 추가로 구함
- Temperature의 경우 계절과 시간을 기준으로 평균 Temperature를 추가로 구함

### 4. Model

- Causal Padding을 사용한 CNN1D 모델 구축
- feature selection과 추가 적인 feature engineering을 모두 모델에 맡기기 위해서 모든 feature를 사용함
- 시간에 대한 상관성을 합성곱으로 나타내고자 CNN1D 모델 구축
- 처음 부터 모델을 만드는 것은 컴퓨팅 파워와 시간 면에서 좋지 않다고 판단하여 다른 시계열 데이터에 좋은 성능을 보였던 모델을 기본 베이스로 조금 수정해서 사용
- 예측하고자 하는 시간을 기준으로 이전 3일치 데이터를 가지고 예측을 하는 방식으로 모델을 구성

### 5. 학습

- Train Data를 Random Sampling 하고 CV를 5로 학습
- 8 : 2로 Train과 Val Data를 나눠서 학습
- Loss의 경우 Metric과 유사한 MAE로 선택
- Optimization의 경우 RMSprop으로 선택(시계열의 경우 RMSprop이 조금 더 우수한 성능을 보임)
- Best Model의 경우 Val 기준으로 가장 Metric이 우수한 모델로 선택

### 6. 예측

- CV별 예측 값을 평균 내어 최종 예측값으로 활용
- CV별 예측 시에 학습이 제대로 안된 모델은 예측에서 제외
- Random Sampling의 효과로 CV별 모델이 서로 잘 맞추는 부분이 달라짐으로써 강건한 모델이 만들어짐

### 7. 결과물
- [**분석 코드**](https://github.com/SeongBeomLEE/Project/tree/main/DACON_sunlight)
- [**발표 자료**](https://github.com/SeongBeomLEE/Project/blob/main/DACON_sunlight/%EB%8D%B0%EC%9D%B4%EC%BD%98_%EB%8F%99%EC%84%9C%EB%B0%9C%EC%A0%84_%ED%88%AC%EB%B9%85%EC%8A%A4%ED%8C%80_%EC%BD%94%EB%93%9C%EC%84%A4%EB%AA%85.pdf)

## 아쉬운 점