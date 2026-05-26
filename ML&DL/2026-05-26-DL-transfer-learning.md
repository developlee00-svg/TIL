# 전이학습 (Transfer Learning)

사전 학습된 모델의 지식을 재사용하여 새로운 과제를 빠르게 해결하는 방법. VGG16을 백본으로 이진 분류 모델을 구축한 학습 노트입니다.

---

## 핵심 요약

### 1. 핵심 아이디어

- **지식의 재사용·전이** — 이미 학습된 가중치를 활용, 처음부터 학습하지 않음
- **속도 + 비용 절감** — 대규모 데이터로 사전 학습된 결과를 그대로 가져옴
- **유전 개념** — 과거 세대의 학습 결과를 미래 세대(새 모델)에 전달

### 2. 핵심 용어

| 용어 | 설명 | 예시 |
| --- | --- | --- |
| 업스트림(백본) | 원래 목적에 맞게 사전 학습된 모델 | VGG16, BERT, GPT, YOLO |
| 다운스트림 | 새로 해결할 문제, 신규 학습 필요 | 본인 비즈니스 데이터 분류 |

### 3. 구조 변형 패턴

- **원본 유지** — 그대로 사용 (제로샷)
- **부분 제거 + 재구성** — 출력층만 떼고 새로 구성 (가장 일반적)
- **하이브리드 연결** — 여러 신경망 연결해 새 구조 설계
- **층별 학습 여부 선택** — 일부는 동결(freeze), 일부만 학습

### 4. 학습 방법

- **파인튜닝** — 가중치 일부 재학습, 특화 모델
- **제로샷** — 학습 데이터 0개, 그대로 추론
- **원샷 / 퓨샷** — 데이터 1개 / 몇 개로 미세 조정 (LLM 프롬프트 엔지니어링의 기반)

### 5. 사전 학습 모델 허브

- TensorFlow Hub — `https://www.tensorflow.org/hub`
- Kaggle Models — `https://www.kaggle.com/models`
- PyTorch Hub — `https://pytorch.org/hub/`
- **Hugging Face** — `https://huggingface.co/` (트랜스포머 집결지)

### 6. VGG16 핵심 옵션

```python
from tensorflow.keras.applications import VGG16

transfer_model = VGG16(
    include_top=False,           # 마지막 1000개 분류층 제거
    weights='imagenet',          # 사전학습 가중치 유지
    input_shape=(150, 150, 3)    # 입력 크기 재정의
)
transfer_model.trainable = False  # 가중치 동결 (학습 중 수정 X)
```

- `Non-trainable params: 14,714,688 (56.13 MB)` → 1,470만 파라미터 고정
- VGG16의 시각 특징 추출 능력은 유지, 새로 추가한 층만 학습

### 7. 커스텀 분류기 조립

```python
new_model = Sequential()
new_model.add(transfer_model)              # 백본 (None, 4, 4, 512)
new_model.add(Flatten())                   # 8192 = 4*4*512
new_model.add(Dense(64))                   # 8192 → 64 (서서히 수렴)
new_model.add(Activation('relu'))
new_model.add(Dropout(rate=0.5))           # 과적합 방지
new_model.add(Dense(1))
new_model.add(Activation('sigmoid'))       # 이진 분류
```

- **8192 → 64 → 1** 단계적 축소 (정보 손실 방지)
- Dropout 0.5는 학습 시에만 작동, 추론 시 자동 비활성화

### 8. 핵심 인사이트

> 전이학습은 "바닥부터 다시 학습하지 않는다"는 발상의 전환. **`include_top=False`로 마지막 분류층 떼어내기 + 본인 데이터용 새 출력층 붙이기**가 가장 일반적인 패턴이다. 이 방식이 NLP로 확장된 결과가 BERT/GPT 파인튜닝, LLM 퓨샷 러닝.

---

## 다루는 주요 키워드

`Transfer Learning` `백본` `업스트림` `다운스트림` `VGG16` `include_top` `weights='imagenet'` `trainable=False` `Flatten` `Dropout` `파인튜닝` `Hugging Face`

🔗 블로그 정리:
DL 2 - 전이학습 (<https://dev-lee.tistory.com/102>)