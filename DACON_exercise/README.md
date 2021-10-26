# 운동 동작 분류 AI 경진대회

## 소개
두번째로 참여했던 DACON 대회로 336팀 중에 41위의 성적을 냈던 대회이다. 센서 데이터를 처음으로 다뤄봤던 대회이고, 대회 종료 후에 2등 분의 코드를 바탕으로 스스로 pytorch를 활용하여 CNN1D 모델도 만들어 봤다. 처음으로 대회를 진행하면서 대회 관련 논문을 서치 했었고, 논문을 바탕으로 feature를 만들었을 때 모델의 성능이 매우 향상된 경험을 얻었다. 그래서 본 대회 이후 부터는 대회 및 프로젝트를 진행할 때 관련 논문 및 자료를 제일 먼저 서치하는 습관을 가지게 되었다. 

## 분석 요약
- 0.02초씩 측정된 운동에 따른 acc(가속도), gy(자이로)의 x, y, z 축 센서값 데이터가 주어짐
- EDA 결과 각 운동마다 서로 다른 분포를 가진다는 것이 확임됨
- 따라서 데이터의 분포를 설명할 수 있는 Max, Min, Mean, Q1, Q2, Q3, IQR, Mean Absolute Deviation, Root Mean Squr, MM, Zero Crossing Rate, Signal Magnitude Area, Mean Crossing Rate 등의 feature를 생성
- 생성된 feature를 모델에 넣어 성능을 비교하면서 feature를 설렉션
- RandomForestClassifier, lightgbm, xgboost, LogisticRegression 등의 Model을 oof ensemble 방식을 통해서 예측
- 가장 성능이 좋았던 xgboost를 하이퍼 파라미터 튜닝을 진행하여 최종 모델을 구축
- LogLoss 지표에서 0.66207의 성능을 가지는 최종 Model이 만들어 짐
- [분석 코드](https://github.com/SeongBeomLEE/Project/blob/main/DACON_exercise/%EC%9A%B4%EB%8F%99_%EB%8F%99%EC%9E%91_%EB%B6%84%EB%A5%98_AI_%EA%B2%BD%EC%A7%84%EB%8C%80%ED%9A%8C_%EC%B5%9C%EC%A2%85.ipynb)
- [대회 주소](https://dacon.io/competitions/official/235689/overview/description#desc-info)

## 아쉬운 점
본 대회에서 가장 좋은 성능을 보였던 Model을 보면 대부분 DNN 기반의 접근 방식이었다. 이러한 이유는 아마도 센서 데이터의 feature를 생성하는 것은 매우 어려운 일이고 도메인 지식이 부족한 상태에서는 사람이 직접 feature를 생성하는 것 보다, DNN 모델을 통해서 사람이 캡쳐하기 어려운 데이터의 특징도 반영하는 feature를 생성하고 학습하는 것이 조금 더 효과적이기 때문인 것으로 판단된다.

- 각 x,y,z 축에 따른 acc, gy 값을 제곱, 루트를 통해서 하나로 합치는 Energy feature 생성
- t+1의 데이터와 t 데이터의 차이를 구하여 전 시간 대비 변화량을 반영하는 feature 생성
- acc, gy 값을 퓨리에 변환한 feature 생성(time domain에서 해석하기 힘든 신호를 frequency domain에서 쉽게 해석할 수 있기 때문)
- 표준화 진행
- 3개의 1D Convolutional Layer를 가지는 간단한 CNN1D 모델 구축
- LogLoss 지표에서 0.49209의 성능을 가지는 최종 Model이 만들어 짐
- [분석 코드](https://github.com/SeongBeomLEE/Project/blob/main/DACON_exercise/private2%EC%9C%84_pytorch_%EA%B5%AC%ED%98%84.ipynb)

CNN1D 모델의 경우 합성곱 연산을 통해서 각 센서의 time에 대한 인과성을 더욱더 잘 캡쳐 할 수 있으며,
이에 시간에 대한 인과성을 학습하는 것이 어려운 Boosting 계열의 모델 보다 더 높은 성능을 보이는 것으로 생각됨