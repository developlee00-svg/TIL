# KoGPT2 기반 제로샷 문장 생성

## 핵심

사전 학습된 KoGPT2 모델을 추가 학습 없이 그대로 가져다 쓰는 **제로샷 러닝**으로 한국어 문장 생성 실습. 업스트림 GPT2(다국어) → 다운스트림 `skt/kogpt2-base-v2`(한국어 파인튜닝) 흐름.

## 모델 로드 패턴

```python
from transformers import AutoTokenizer, TFGPT2LMHeadModel

repo = 'skt/kogpt2-base-v2'
tokenizer = AutoTokenizer.from_pretrained(repo)
model = TFGPT2LMHeadModel.from_pretrained(repo, from_pt=True)
```

* **`LMHead`** — Language Model Head, 어휘 크기(51,200) 차원의 출력층으로 다음 토큰 로짓 출력
* **`from_pt=True`** — PyTorch 가중치를 TF로 변환해서 로드

## 두 가지 생성 방식

**1. `model.generate()` (고수준 API)**

토큰 예측·누적·종료 조건을 캡슐화. 간편하지만 출력 제어 제한적.

```python
output = model.generate(input_vector, max_length=64, use_cache=True)
print(tokenizer.decode(output.numpy().tolist()[0]))
```

**2. 수동 루프 + Top-K 샘플링 (스트리밍)**

토큰 단위 출력 + 창의성 제어. 직접 다음 토큰을 뽑는 전략을 구현.

```python
while len(input_vector) < (start_token_size + MAX_TOKEN):
    output = model(np.array([input_vector]))
    top5 = tf.math.top_k(output.logits[0, -1], k=5)
    token_id = random.choice(top5.indices.numpy())  # Top-5 중 랜덤
    input_vector.append(token_id)
    print(tokenizer.decode(token_id), end=' ')
```

## 모델 출력 shape

`output.logits.shape` = `(batch, seq_len, vocab_size)` = `(1, 4, 51200)`

마지막 토큰 위치의 51,200개 로짓이 곧 **다음 토큰 확률 분포**. 여기서 어떻게 하나를 뽑느냐가 생성 다양성을 결정.

## 선택 전략과 LLM temperature

* **Greedy (1등 선택)** → 결정적, 단조로움 → `temperature=0`
* **Top-K 랜덤** → 의외성, 창의성 → `temperature` 높음

LLM API의 `temperature`, `top_k`, `top_p`가 모두 이 샘플링 전략의 추상화.

## 한 줄 회고

> 문장 생성의 본질은 "51,200개 어휘 로짓에서 어떤 전략으로 하나를 뽑을 것인가"의 문제. 결정적 선택은 단조롭고 완전 랜덤은 의미를 잃는다. 그 사이 균형이 LLM API 파라미터로 추상화돼 있을 뿐, 내부 원리는 동일하다.

---
🔗 블로그 정리:  
[DL 6 - 트랜스포머 기반 학습 모델 가져오기](https://dev-lee.tistory.com/107)