# 댐 유입 수량 예측을 통한 최적의 수량 예측 모형 도출

# 소개
- 본 프로젝트는 2021 빅콘테스트에 제출한 프로젝트입니다.
- 저는 본 프로젝트에서 데이터 전처리, 모델링, 실험을 맡았습니다.
- 본 프로젝트의 목적은 댐 유입 수량 예측을 위한 최적의 모델을 제출하는 것입니다. 
- 하나의 Target에 대하여 6개의 독립적인 데이터 집단이 주어져 데이터 집단을 효율적으로 활용하는 것이 중요했고, 변수로 K-댐 주변 지역의 유역평균강수, 강우량, 수위가 주어져 변수 간의 다중공선성이 발생하여 모든 변수를 효과적으로 활용하는 것이 중요했습니다.
- 따라서 본 프로젝트에서는 End-to-End 과정으로 변수와 독립적인 데이터 집단 간의 결합을 효과적, 효율적으로 학습할 수 있는 방법을 찾고자 노력했습니다.

# 요약
- **문제 정의 (Matplotlib, Seaborn)**
    - 변수 간의 강한 상관 관계가 가지는 독립적인 데이터 집단 6개가 존재
    - 데이터 집단을 효과적 효율적을 결합하는 방식이 필요
    - 데이터 집단, 변수 간의 결합을 End-to-End로 학습할 수 있는 CNN 모델 구상
- **모델 (Pandas, Numpy, Keras)**
    - 집단 간의 결합을 Feature Map의 생성으로 정의
    - 변수 간의 상호작용은 Filter의 연산으로 정의
    - Dilated Convolution을 이용해 멀리있는 변수와의 상호작용도 효율적으로 학습할 수 있도록 구성
    - End-to-End 과정으로 변수와 데이터 집단 간의 상호작용을 효과적, 효율적으로 학습하는 Dilated CNN1D 모델을 최종 모델로 제안
- **실험 (Scikit-learn, 이외 ML 모델 라이브러리)**
    - CNN1D 모델의 경우 모든 변수와 데이터 집단을 조화롭게 활용하기 때문에 ML 기반의 모델 중 가장 성능이 좋았던 XGBoost 모델보다 관측 오류(이상치)에 더 강건하다는 것을 실험을 통해서 확인함
- **본 모델은 현업에서 발생하는 국지성 호우로 인한 관측 오류 문제를 해결하는데 기여 가능**
- [**분석 코드**](https://github.com/SeongBeomLEE/Project/blob/main/2021_bigcontest/%EB%B6%84%EC%84%9D%20%EC%BD%94%EB%93%9C.ipynb)
- [**최종 보고서**](https://github.com/SeongBeomLEE/Project/blob/main/2021_bigcontest/%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B6%84%EC%84%9D%EB%B6%84%EC%95%BC_%ED%93%A8%EC%B2%98%EC%8A%A4%EB%A6%AC%EA%B7%B8_%ED%99%8D%EC%88%98ZERO_%EB%B2%94%ED%98%B8_%EA%B2%B0%EA%B3%BC%EB%B3%B4%EA%B3%A0%EC%84%9C.pdf)

# 내용
## 1) 문제 정의

- 하나의 Target에 대하여 독립적인 데이터 집단 6개 존재
→ 데이터 집단을 효율적으로 활용하는 것이 중요
- 변수로 K-댐 주변 지역의 유역평균강수, 강우량, 수위가 주어짐(변수간 강한 상관관계 존재)
→ 다중공선성으로 인해 모든 변수를 효과적으로 활용하기 어려움
- 자연적인 변수로 인한 예측의 어려움
→ feature interaction을 반영하는 새로운 feature가 필요

: 따라서 본 Task는 변수와 데이터 집단 간의 상호작용을 효과적, 효율적으로 학습하는 것이 핵심

## 2) 변수 생성

- EDA결과 유입량과 강우 사이에 시차가 존재한다는 것을 확인
- 다수의 논문에서 유입량과 강우 사이에 평균 6시간 정도의 시차가 존재한다고 함

: 따라서 강우 관련 변수를 1~6시간씩 shift한 변수 생성

## 3) 데이터 셋 분할

- 시계열 데이터 특성에 따라 다음과 같은 방식으로 데이터 셋을 분할

<p align="center"><img src="https://user-images.githubusercontent.com/65529313/172414686-afbc4e54-5324-499b-be99-747e9ad4b6a3.png" /></p>

## 4) 모델 학습 과정

- 다음과 같이 모델을 학습 시켜 가장 성능이 좋은 모델을 Ensemble함

<p align="center"><img src="https://user-images.githubusercontent.com/65529313/172415018-2c7783c1-7878-4fec-9a73-aaca59f33a28.png" /></p>

## 5) Machine Learning 모델

### (1) 학습 방법

- 데이터 집단을 효과적으로 활용하기 위해서 아래와 같이 2가지 방법으로 나눠서 모델을 학습

![image](https://user-images.githubusercontent.com/65529313/172415089-534f7ca6-8601-4bae-8bbf-377c1ac2598f.png)

### (2) Regression 모델

- 선형성을 제외한 회귀 분석의 기본 가정을 모두 만족하지 못함
- 모든 모델이 Underfitting된 상태로 좋지 못한 성능을 보여줌

![image](https://user-images.githubusercontent.com/65529313/172415211-2f0111ac-9e59-4f58-8b6c-72a2c1e5033b.png)

![image](https://user-images.githubusercontent.com/65529313/172415225-d763b069-a8fe-4d00-b01f-d952b90bf7f7.png)

![image](https://user-images.githubusercontent.com/65529313/172415243-a6f2613f-6ac6-40c9-b920-c6cbc8390af2.png)

![image](https://user-images.githubusercontent.com/65529313/172415260-98ee01b6-f62c-4726-a652-8e8e6ed65e14.png)

### (3) K-NN Regression & SVR

- Underfitting된 상태로 좋지 못한 성능을 보여줌

![image](https://user-images.githubusercontent.com/65529313/172415327-5a58cf71-70c3-443c-85e5-badf4b8d10c4.png)

![image](https://user-images.githubusercontent.com/65529313/172415345-bfb84de9-8d9d-49fe-a021-78491e16ab01.png)

### (4) Bagging 기반 Tree

- Bagging 기반 Tree 모델은 Variance를 줄여줘 Overfitting을 해소하는 것에 적합 
→ 따라서 본 과제에 적합하지 않은 모델

![image](https://user-images.githubusercontent.com/65529313/172415415-16743e8d-f513-4056-92b2-235715d1cac8.png)

### (5) Boosting 기반 Tree

- Boosting 기반 Tree 모델은 Bias를 줄여줘 Underfitting을 해소하는 것에 적합 
→ 따라서 본 과제에 가장 적합한 모델
- 방법2로 학습한 XGBoost가 가장 좋은 성능을 보여줌

![image](https://user-images.githubusercontent.com/65529313/172415511-6f3fc0c3-15e6-4eed-a636-27a52d26e16d.png)

### (6) 종합

- 본 과제에서 가장 적합한 Machine Learning 모델은 XGBoost로 판단됨
- XGBoost의 경우 유입량을 과소 추정한다는 특징을 보임
- XGBoost의 경우 학습 과정에서 feature interaction을 효과적으로 학습할 수 없음
- 얕은 트리 기반의 XGBoost는 변수간 강한 상관관계가 존재하여 모든 변수를 효과적으로 활용하기 어려움

: 따라서 본 과제에서는 변수 간의 상호작용과 데이터 집단 간의 결합을 효과적으로 학습하는 모델이 필요하며, ML 모델의 경우 본 Task에 적합하지 않은 모델로 판단됨

## 6) Convolutional Neural Network 모델

### (1) 개요

- 채널을 데이터 집단으로 구성
- Filter를 통해서 변수 간의 상호작용을 학습
- Feature Map의 생성을 통하여 데이터 집단을 하나로 결합
- WaveNet의 Dilated Convolution을 활용하여 멀리 있는 변수와의 상호작용을 효율적으로 학습할 수 있도록 구성
- ML 모델과 달리 데이터 집단의 결합과 변수 간의 상호작용을 End-to-End 과정으로 학습 가능

![image](https://user-images.githubusercontent.com/65529313/172415611-f0c74e41-58cd-4d70-8087-898a31d42f72.png)

![image](https://user-images.githubusercontent.com/65529313/172415632-890484ae-8cae-48ac-ac33-252af7d7c954.png)

### (2) 데이터 전처리

- 변수의 특성 상 최대 값에 제한이 없기 때문에 변형 MinMax Scaling을 진행
- 각 모델에 특성에 따라서 데이터 셋을 구성

![image](https://user-images.githubusercontent.com/65529313/172415706-9888cacf-00d7-4119-b942-de3ef06609ec.png)

### (3) 모델 학습 방법

- 다음과 같이 모델을 학습시켜서 일반화된 모델을 얻을 수 있도록 함

![image](https://user-images.githubusercontent.com/65529313/172415779-9b96ffc5-3c7f-40e6-aaef-8db01b3956a9.png)

### (4) WaveNet

- 기존 WaveNet을 본 과제에 맞게 변형한 형태

![image](https://user-images.githubusercontent.com/65529313/172415857-772d80ec-a11b-4ad8-8500-a0c609e3d95b.png)

![image](https://user-images.githubusercontent.com/65529313/172415867-8da9ea1c-d7d9-437a-baf2-e4e670613e49.png)

![image](https://user-images.githubusercontent.com/65529313/172415882-073da9f9-a715-425e-8879-f1b9c968bbf3.png)

### (5) CNN1D

- 집단마다 서로 다른 필터가 변수 간의 상호작용을 학습

![image](https://user-images.githubusercontent.com/65529313/172415969-14ac71fb-7b05-4e9d-8ab0-a2530e560493.png)

![image](https://user-images.githubusercontent.com/65529313/172415990-970ebc49-fd8e-43e9-a524-39a9fb9a931c.png)

### (6) CNN2D

- 집단마다 동일한 필터가 변수 간의 상호작용을 학습

![image](https://user-images.githubusercontent.com/65529313/172416040-ba0b2387-ba63-4b0e-b4e6-59b91aaef7a1.png)

![image](https://user-images.githubusercontent.com/65529313/172416050-4913b791-a23b-4f85-95fa-c77b776f4c2b.png)

### (7) Time_CNN2D

- 집단마다 서로 다른 필터가 시간의 인과성과 변수 간의 상호작용을 학습

![image](https://user-images.githubusercontent.com/65529313/172416112-5815496b-44f4-4aa2-b3d9-88ddbab922c8.png)

![image](https://user-images.githubusercontent.com/65529313/172416125-d5ea3e5a-0850-4980-99e9-b3e5c5b8420d.png)

## 7) 최종 모델

- CNN1D 모델을 최종 모델로 선정
    - ML 모델과 달리, 데이터 집단과 변수 간의 상호작용을 효과적으로 학습할 수 있음
    - 다른 CNN 모델에 비하여 param의 수가 적어 모델 학습 속도가 빠르고, 과적합의 발생가능성이 낮음
    - 홍수 피해를 막기 위해서는 과소 추정보다는 과대 추정이 더 안전하다고 판단

![image](https://user-images.githubusercontent.com/65529313/172416204-5d9c470a-b64b-41dd-8add-1f57773de820.png)

![image](https://user-images.githubusercontent.com/65529313/172416221-0c75c83b-ddfa-4001-aa56-fbfc5d98c8ce.png)

![image](https://user-images.githubusercontent.com/65529313/172416238-fd536b8d-44f3-4a67-8988-ec3abe0d1354.png)

## 8) 실험

- CNN1D 모델은 모든 변수와 데이터 집단을 조화롭게 활용하기 때문에 XGBoost 모델보다 관측오류에 강건하다는 것이 실험을 통해서 확인됨

![image](https://user-images.githubusercontent.com/65529313/172416313-7cb9a2a1-281c-4275-b0a6-01b79aebc852.png)

![image](https://user-images.githubusercontent.com/65529313/172416330-577147c6-ee42-45b5-94b3-43f9e57d18e5.png)

![image](https://user-images.githubusercontent.com/65529313/172416338-9f271392-27a7-4642-9ca3-2872e837ac52.png)

# 아쉬운 점
- 독립적인 데이터 집단의 결합과 변수간의 상호작용을 효과적으로 학습할 수 있는 방법을 고려한 점은 좋았으나 CNN 모델을 고집한 것이 제일 아쉬움
- CNN에서 Filter는 각 변수마다 동일한 값을 공유하기 때문에 변수 간의 상호작용을 효과적으로 학습할 수 있다는 보장이 없음
- CNN에 데이터 집단의 결합방식과 함께 변수 간의 상호작용을 학습할 수 있는 다른 모델을 고려했다면 더 좋은 결과를 가져왔을 것이라고 생각됨
