# KDT - 5 DeepLearnig Project

## 표정 인식 딥러닝 모델을 통한 딥러닝 분석
  
#### DATA
https://www.kaggle.com/datasets/msambare/fer2013
  
#### 역할분담

역할|참여인원
:---:|:---:
자료조사 / 주제 선정 | 김규량, 박희진, 손예림, 이시명
기본형 모델 제작 | 박희진
배치 사이즈 조절 | 김규량
활성화 함수 | 박희진
옵티마이저 | 이시명
레이어/퍼셉트론/드롭아웃 | 손예림
통합 모델 제작 | 이시명
발표 자료 제작 | 김규량, 박희진, 손예림, 이시명

<details>
  <summary>
    김규량(소주제)
  </summary>

</details>

<details>
  <summary>
    박희진(활성화함수에 따른 모델 성능 차이)
  </summary>

### (1) 데이터 로딩 및 확인

- 데이터가 train과 test로 나눠져 있었는데, 하위 폴더의 폴더 이름이 피쳐 이미지의 라벨이길래 for문을 돌려 라벨과 피쳐를 들고옴
    - os.listdir에 enumerate해서 문자열로 된 라벨을 정수로 가지고 옴

 + 추가 → 데이터가 불균형 데이터임

평가지표로 acc를 사용하는 것은 부적합함 → f1 score나 recall 사용

### (2) 데이터 전처리

- 이미지 데이터를 255.로 나누어 정규화 시행

### (3) 데이터셋 준비

- 데이터셋 클래스를 생성 - 최대한 단순하게 클래스 구현
- train data에서 나누어 train data과 valid data를 8 : 2로 나눔
- 활성화 함수를 제외하고 모든 하이퍼파라미터는 통일
    - 배치사이즈는 32로 설정

### (4) 모델 클래스 정의

- 활성화 함수만 바뀐 여러 모델 인스턴스를 생성해야하기 때문에, 생성 메서드에 매개변수로 AF를 넣어서 넣는 활성화 함수에 따라 그에 맞는 모델이 생성되게끔 클래스를 정의함.

### (5) 학습준비 - 학습함수, 평가함수, 그래프출력함수, 예측함수, 내가 만든 모델링 함수

- 활성화 함수를 제외하고 모든 파라미터 통일
    - 에포크, 손실함수, 옵티마이저, 스케줄러 모두 통일
- 학습함수
    - 매개변수로 dataloder뿐만 아니라 model과 optimizer도 넣어서 각 모델을 함수 하나로 모두 학습할 수 있게 구조화
    - 리턴 값으로 한 에포크 당 loss_score와 acc_score를 반환
- 평가함수
    - 매개변수로 dataloder와 model, optimizer는 최적화 안할거기때문에 필요없음
    - 리턴 값으로 한 에포크당 loss_score와 acc_score가 든 리스트를 반환
        - 에포크가 늘어남에 따른 loss와 acc 변화를 시각화해서 그래프로 보기 위함
- 예측함수
    - 매개변수로 dataloder와 n(출력할 이미지 수), model, filename을 받음
    - 함수 내에서 filename을 통해 모델을 로딩하여 그 모델에 predict
    - 얼마나 정답과 같은지 계산하여 정확도 반환
- 그래프출력함수
    - 평가함수에서 리턴받은 loss_score 리스트와 acc_score 리스트를 통해 손실 그래프와 정확도 그래프 그리기
    - 왼쪽 축과 오른쪽 축을 다르게 하여 한꺼번에 표시
- 모델링 함수
    - 여러 모델을 계속해서 같은 코드에 적용하기 위해 함수를 작성
    - 초기 최소 손실을 설정하고 계속해서 최소 손실을 업데이트하는 동시에 모델을 저장하게끔 설정
    - 설정한 에포크 수만큼 for문을 돌면서 학습함수와 평가함수 실행
    - 조기 종료 기능 넣음
        - val_loss가 지정된 3회 이상 개선이 안되면 학습 종료

### (4) 학습 및 검증

- 위의 함수에 각 모델들을 넣어 학습 및 검증 실행

### (5) 그래프 결과

- Sigmoid

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/c19dc72a-3d43-4061-8eee-c886a3ffbb0f/474acd98-5af3-4318-afcd-30be82588c8b/Untitled.png)

- Softmax

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/c19dc72a-3d43-4061-8eee-c886a3ffbb0f/120c967a-a5f5-4818-810e-6459c947741f/Untitled.png)

- Relu

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/c19dc72a-3d43-4061-8eee-c886a3ffbb0f/55628a5b-f016-4e05-926a-9960c049dc7a/Untitled.png)

- Leaky_Relu

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/c19dc72a-3d43-4061-8eee-c886a3ffbb0f/07845133-7acd-45a6-94c0-38f0340499eb/Untitled.png)

- Tanh

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/c19dc72a-3d43-4061-8eee-c886a3ffbb0f/747cc5dd-7898-4227-89fe-fc7d9b71dc2d/Untitled.png)

### (6) 그래프 결과 분석

- Relu와 Tanh을 제외하고 모두 과적합이 일어났다. valid_loss는 감소하다 다시 증가하는 반면 train_loss는 계속 감소하고 train_acc는 계속해서 올라갔다. valid_loss 그래프는 2차 함수의 모양을 띠었다. 반면 Relu는 에포크가 늘어남에도 불구하고 loss와 acc에 큰 변화가 없었다. Relu는 은닉층이 많아서 기울기 소실 발생 확률이 있는 경우에 효과적인 활성화함수지만 기본 모델 자체의 은닉층이 1개뿐이라 Relu의 장점과 뛰어난 성능이 두각되지 못한 것 같다. 반대로 Tanh는 기대하지 않았는데 과적합도 일어나지 않았을 뿐만 아니라 에포크가 늘어남에 따라 train_loss와 valid_loss 둘 다 줄어 들고 있었고, train_acc와 valid_acc가 늘어나고 있었다. 에포크를 더 크게 했다면 계속해서 모델 성능을 향상시켰을 것이라고 예상한다. 은닉층이 하나인 해당 모델 구조에서는 에포크를 늘인다면 가장 성능이 좋을 것으로 판단된다.

### (7) 결론

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/c19dc72a-3d43-4061-8eee-c886a3ffbb0f/2f08e1b7-f1a0-4456-b11b-d00dc6a01145/Untitled.png)

- Relu의 기울기 소실 문제를 보완한 Leaky_Relu가 검증에서 성능이 더 좋게 나왔지만 테스트를 진행해보니 Relu가 더 좋았다. 그러나 에포크를 더 늘인다 해당 모델 구조에서는 Tanh가 성능이 가장 좋을 것으로 예상된다.

### (8) 예측

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/c19dc72a-3d43-4061-8eee-c886a3ffbb0f/4f46a759-9c09-4921-b6f4-de99a4864e67/Untitled.png)

- 예측함수를 통하여 이미지를 출력하고 True값과 Predict값 출력
- 각 모델마다 정확도 확인

### (9) 피드백

불균형데이터인데 왜 정확도를 평가지표로 했는가. 불균형 데이터에는 재현율이 더 적합한 평가지표임 !

- 실수 🥲
- 평가지표를 재현율로 해서 다시 한번 재현해보자

### (10) 스스로 아쉬운점

- 기본 모델의 은닉층을 더 많이 했다면 기울기 소실에 따른 활성화함수의 성능 차이가 더 눈에 띠게 보였을텐데, 그런 특징들을 강조하지 못해서 아쉽다.
- 기본 모델 은닉층을 더 많이 해서 활성화함수의 성능 차이를 다시 한번 비교해보자

### (11) 피드백 후 추가

- 평가지표를 재현율로 할 것
- 모델의 은닉층을 더 늘려서 활성화 함수의 성능 차이를 비교해볼 것

</details>

<details>
  <summary>
    이시명(소주제)
  </summary>

</details>

<details>
  <summary>
    손예림(소주제)
  </summary>



