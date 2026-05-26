# 딥러닝(DL) 개요와 베이스라인 구현

딥러닝의 역사·구조·구현 필요 요소를 정리하고 흉부외과 CT 데이터로 이진 분류 DNN 베이스라인을 구축한 학습 노트입니다.

---

## 핵심 요약

### 1. DL의 위치

회귀의 수식 `y = w1x1 + w2x2 + ... + wnxn + bias`가 그대로 퍼셉트론으로 확장된 것이 출발점. 층을 깊게 쌓아 표현력을 확보하는 방식으로 발전.

> **회귀계수 = 퍼셉트론 가중치 = 딥러닝 파라미터**. 같은 개념의 다른 이름.

### 2. 딥러닝 역사 (마일스톤)

| 연도 | 사건 |
| --- | --- |
| 1943 | 인공지능 논문 제안 |
| 1957 | 퍼셉트론 제안 |
| 1969 | XOR 문제 → 1차 AI 겨울 |
| 1986 | 오차 역전파 제안 |
| 2009 | CNN 논문 발표 |
| 2012 | 이미지넷 → 딥러닝 패러다임 전환 |
| 2017 | 트랜스포머 발표 |
| 2022~ | LLM 특이점 |

### 3. 신경망 구조 진화

- **단일 퍼셉트론** — 입력층 + 출력층, XOR 못 풂
- **다중 퍼셉트론** — 입력층 + 은닉층 + 출력층, XOR 해결
- **ANN** — 은닉층 1개
- **DNN** — 은닉층 2개 이상

### 4. 핵심 메커니즘

- **오차 역전파(1986)** — 모든 DL 프레임워크 기본 지원
- **활성화 함수** — Sigmoid → tanh → ReLU → PReLU → Softmax
- **최적화 계보** — GD → SGD → Momentum → AdaGrad → **Adam**(종착역)

### 5. 프레임워크 비교

| 항목 | TensorFlow + Keras | PyTorch |
| --- | --- | --- |
| 개발사 | 구글 | 메타 |
| 스타일 | Define By Run | Define And Run |
| 강점 | high-level API | R&D, LLM |
| 디버깅 | 텐서보드 필요 | 즉시 결과 확인 |

### 6. 베이스라인 구현 (흉부외과 CT 이진 분류)

```python
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Dense

model = Sequential()
model.add(Dense(30, input_dim=17, activation='relu'))   # 은닉층: 540 params
model.add(Dense(1, activation='sigmoid'))               # 출력층: 31 params

model.compile(optimizer='adam', loss='binary_crossentropy', metrics=['accuracy'])
history = model.fit(X_train, Y_train, batch_size=16, epochs=20)
```

- 입력 17 → 은닉 30 → 출력 1 (이진 분류)
- 손실: `binary_crossentropy` / 최적화: `adam`
- **테스트 정확도: 83.9%**

### 7. 학습 전략 키워드

- **반영 주기** — 온라인 학습 vs 오프라인 학습
- **학습 스타일** — 일반 학습 / 전이 학습(파인튜닝, 제로샷, 원샷, 퓨샷)
- **데이터 사이즈** — 배치 학습 vs 미니배치 학습 (`batch_size`, `epoch`)

### 8. 핵심 인사이트

> 파라미터 수 = `(입력 × 출력) + 출력(bias)`. 17×30+30=540, 30×1+1=31. 학습이란 결국 이 파라미터들을 자동으로 최적화하는 과정이다.

---

## 다루는 주요 키워드

`퍼셉트론` `DNN` `오차 역전파` `ReLU` `Adam` `Sequential` `Dense` `binary_crossentropy` `TensorFlow` `PyTorch` `미니배치` `에포크`

🔗 블로그 정리:
DL 1 - 딥러닝 개요 (<https://dev-lee.tistory.com/101>)