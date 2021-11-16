# AI야, 진짜 뉴스를 찾아줘!

## 소개
본 공모전은 내가 제일 처음으로 참여했던 DACON 대회이다. 결과는 192팀 중 143등으로 결과는 좋지 않았지만 나에게 데이터 분석의 재미를 일깨워 주웠던 대회로 꽤나 애착이 가는 대회이다. 본 대회 코드를 지금 다시 보면은 정말 간단한 접근법이지만 그 당시에는 정말 많은 노력을 했고 오류도 많이 발생해서 밤도 새고 구글링도 정말 많이 했었던 것 같다.

## 내용
- 본 대회는 뉴스의 date / title / content / ord를 바탕으로 뉴스가 진짜인지 가짜인지를 판단하는 ML Model을 만드는 대회
- Target 변수인 info에 영향을 주는 변수를 찾고자 EDA를 진행
- EDA 결과 content와 ord가 target에 가장 큰 영향을 주는 변수인 것으로 확인
- konlpy 패키지를 통하여 content 변수를 전처리하고 원핫인코딩을 진행
- 진짜 뉴스와 가짜 뉴스를 분류하는 간단한 MLP 모델을 구축
- Test data에 대하여 약 96%의 정확도를 보임
- [분석 코드](https://dacon.io/competitions/official/235658/codeshare/2027?page=1&dtype=recent)
- [대회 주소](https://dacon.io/competitions/official/235658/codeshare)

## 아쉬운 점
지금 생각해보면 단순한 MLP 모델을 만들고 자연어 처리를 한 content를 단순히 one-hot-encoding 형식으로 embedding 한 것이 아쉬운 것 같다. 만약 지금 다시 본 대회를 진행한다면, 현재 NLP Task에서 아주 좋은 성능을 보여주고 있는 Transformer 모델을 구축해서 대회에 참여했을 것 같다.

- content를 konlpy 패키지를 통해서 전처리
- 전처리된 content를 토큰화
- 토큰화된 content를 Transformer Model의 input으로 활용
- Transformer 모델의 인코더를 통해서 content를 Embedding
- Transformer 모델의 디코더 부분은 FC Layer로 만들어 Embedding 된 content를 진짜와 가짜 뉴스로 분리
- sparse한 one-hot-encoding 보다 dense한 Transformer 모델을 활용한 Embedding 방식을 활용하는 것이 모델 예측에 더 좋은 성능을 준다.

위와 같은 방식으로 본 대회를 진행했었다면 더 좋은 성적을 받았을 것이다. 하지만 본 대회가 없었다면 지금의 나도 없다고 생각하기 때문에 내가 성장을 하는데 좋은 밑거름이 되었다고 생각된다.